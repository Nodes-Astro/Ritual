# Ritual Node Kurulumu

![image](https://github.com/Alping0/Ritual/assets/105454859/6e7785f5-1acb-4ea5-b6bd-b7441c5add8f)

## Sistem Gereksinimleri

![Sistem gereksinimleri](https://github.com/Alping0/Ritual/assets/105454859/12bdff02-a307-428d-b06f-3e0ceac3ec62)

## Gereklilikleri yükleyelim ve güncelleme yapalım

```
sudo apt update && sudo apt upgrade -y
sudo apt -qy install curl git jq lz4 build-essential screen
```
## Docker yükleyelim

```
sudo apt install docker.io
```
```
docker --version
```
#### ⚠️v24.0.5 çıktısı almalısınız

## En güncel docker compose'u yükleyelim

```
sudo curl -L "https://github.com/docker/compose/releases/download/v2.24.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
#### Docker compose directorysine izin verelim

```
sudo chmod +x /usr/local/bin/docker-compose
```
#### ⚠️v2.24.5 çıktısı almalısınız

## Starter Repository'i klonlayalım.

```
git clone https://github.com/ritual-net/infernet-container-starter
```
#### ℹ️ Dosyamızın içine girmek için

```
cd infernet-container-starter
```
## Örnek docker çalıştıralım gerekli dosyalarımızı oluşturalım

#### Screen oluşturalım

```
screen -S ritual
```

#### Ardından bunu run edelim

```
project=hello-world make deploy-container
```

#### Loglarımız başarılı bir şekilde aktıktan sonra CTRL + A + D ile screenden çıkalım. (Tekrar girmek için screen -r ritual)

#### Screenden çıkalım ardından containerlar düzgün çalışıyor mu kontrol edelim

```
docker ps -a
```

![image](https://github.com/Alping0/Ritual/assets/105454859/90dd3895-f2e5-4536-8fef-819a3b5c5286)


## Konfigürasyonu değiştirelim

#### ℹ️ Öncelikle bunun için Alchemy veya Infura kullanmanız gerekli ben Alchemy üzerinden anlatacağım, yoksa Alchemy'den bir üyelik alın:

https://dashboard.alchemy.com/

#### Giriş yaptıktan sonra sol menüden app sekmesine tıklayın ve create new app diyin.

![create new app](https://github.com/Alping0/Ritual/assets/105454859/14b38c4d-fecc-455b-878f-e05f6cf6f4aa)

#### Burada Base mainnet'i seçin istediğiniz ismi ve açıklamayı yazıp oluşturun.

![Select chain](https://github.com/Alping0/Ritual/assets/105454859/e2b440d4-443e-4b96-9b6f-fd7f4f6ecf19)

#### Oluşturduğunuz app'in üstüne tıklayın ve api key butonuna basın.

![api key](https://github.com/Alping0/Ritual/assets/105454859/824432b6-bd51-42cc-9f19-f2ff36f0e2b8)

#### Ardından https ile başlayan private endpointi kopyalayıp not defterine yapıştırın.

![http](https://github.com/Alping0/Ritual/assets/105454859/6b931931-3392-457a-9d20-ee5fe2c0c36c)

### Config'in içine girin

```
nano ~/infernet-container-starter/deploy/config.json
```
![config](https://github.com/Alping0/Ritual/assets/105454859/43a40606-4fa1-4f10-a08e-b0856b775122)

### İşaretli üçünü şu şekilde değiştirin:

#### Rpc yerine Alchemy'den aldığınız endpointi yazın

#### Coordinator adresi değiştirin ve Base coordinator adresini yazın: 0x8D871Ef2826ac9001fB2e33fDD6379b6aaBF449c

#### Priv keyi değiştirip kendi metamask'ınızın priv keyini kopyalayın.

### UYARI: ⚠️ Sadece 0x ile başlayan priv keyleri kabul ediyor eğer priv keyiniz 0x ile başlamıyorsa başına 0x koyun!

### UYARI: ⚠️ Base ağında en az 5 usd değerinde ETH bulunsun!

## Konfigürasyonu uygulayın

```
docker restart anvil-node
```

```
docker restart hello-world
```

```
docker restart deploy-node-1
```

```
docker restart deploy-fluentbit-1
```

```
docker restart deploy-redis-1
```

#### ℹ️ docker ps -a ile deploy-node-1 containerını öğrenip şu komut ile konfigürasyonunuz başarılı bir şekilde değişmiş mi ve node'unuz hatasız çalışıyor mu öğrenebilirsiniz.

![deploy node1](https://github.com/Alping0/Ritual/assets/105454859/7d8f7231-28d7-476b-a6ac-82f9ac480d1d)

```
docker logs -f <CONTAINER ID>
```

![log](https://github.com/Alping0/Ritual/assets/105454859/e682e8d1-7d5d-4067-8d84-3c24f81a381f)

#### Loglar bu şekilde akıyor ise devam edebiliriz, konfigürasyonun değiştiğine emin olun.

## Foundry ve Gereksinimleri Yükleyelim

#### Yeni dosya oluşturalım

```
cd
mkdir foundry
cd foundry
```

#### Scripti çalıştıralım

```
curl -L https://foundry.paradigm.xyz | bash
```

#### Konfigürasyonu update edelim ve çalıştıralım

```
source ~/.bashrc
foundryup
```

![image](https://github.com/Alping0/Ritual/assets/105454859/948c67ad-ea0a-4c45-a9c3-9bec14c47633)

#### ℹ️ Böyle bir çıktı aldıysanız tamam

#### Gerekli library ve SDK'leri yükleyelim

```
cd ~/infernet-container-starter/projects/hello-world/contracts
```

```
forge install --no-commit foundry-rs/forge-std
```

```
forge install --no-commit ritual-net/infernet-sdk
```

## Contract deploy edelim

#### Dosyamızın içine girelim ve contract deploy edelim

```
cd ~/infernet-container-starter
project=hello-world make deploy-contracts
```

#### ℹ️ Ardından başarılı bir tx olmuş mu kontrol edelim

```
docker logs -f anvil-node
```

![image](https://github.com/Alping0/Ritual/assets/105454859/8871cbdb-80e5-4e9c-ab17-44f10c54fb2d)

#### Loglarda bu şekilde görünmesi gerekiyor.

## Contract çağıralım

```
cd ~/infernet-container-starter
project=hello-world make call-contract
```

#### ℹ️ Kısa bir süre sonra yine tx'inizin logda görüntülendiğinden emin olun

```
docker logs -f anvil-node
```

## On-chain Kayıt yaptıralım.

Bunun için bu linke gidin:
https://basescan.org/address/0x8d871ef2826ac9001fb2e33fdd6379b6aabf449c#writeContract

### Aşamaları takip edelim:

![image](https://github.com/Alping0/Ritual/assets/105454859/303fd14c-c3bb-435a-af56-f34b86c09500)

### ⚠️ Öncelikle cüzdanımızı bağlayalım ve bu cüzdanın config dosyamızdaki cüzdan olduğuna emin olalım.

![image](https://github.com/Alping0/Ritual/assets/105454859/620a156b-875e-49e6-b4bc-46bf704fd3e3)

#### Cüzdan adresimizi yazalım ve tx'i onaylayalım

### ⚠️ 1 saat bekledikten sonra activateNode sekmesinden on-chain kaydımızı tamamlayalım.

## Şimdi test edelim

#### Alchemy dashboard'a girin ve oluşturduğunuz app'e tıklayın

![image](https://github.com/Alping0/Ritual/assets/105454859/33b2b59e-32ba-49a4-8e29-5c759e4ab777)

#### ℹ️ Total request gözüküyorsa tamam
![image](https://github.com/Alping0/Ritual/assets/105454859/ce031131-a2c9-46ed-ad8d-21f0c783ef8a)

## Base explorerda cüzdan adresinizi aratın, register ve active olmak üzere 2 tane tx gözükmesi gerekiyor.

![image](https://github.com/Alping0/Ritual/assets/105454859/dde62aeb-5023-410e-ad0e-3007e91df887)

### ℹ️ Logları kontrol etmek için container id'ye göre bu komutu çalıştırabilirsiniz

```
docker logs -f <CONTAINER ID>
```

### Node kurulumu tamamlandı hayırlı olsun :)






















































