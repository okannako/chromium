<p align="center">
  <a href="">
    <img alt="Hero" src="https://github.com/user-attachments/assets/7a341fbd-1853-4993-99ce-ba3827956060" width="100%" />
  </a>
</p>

## Ön Bilgi
- Chromium'u VPS'ler üzerinde Chrome gibi bir tarayıcıya ihtiyaç duyduğumuzda kurup kullanıyoruz. Eklenti, bir siteye girme vs gibi durumlarda çok işe yarıyor. Yükleme için VPS'e giriş yaptıktan sonra aşağıdaki kodları kullanabilirsiniz.
- İlk olarak Docker ile Chromium tarayıcıyı vps'e kuruyoruz.
```
apt install tmux
tmux
sudo apt update -y && sudo apt upgrade -y
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done

sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update -y && sudo apt upgrade -y

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Docker version check
docker --version
```

### Timezone Kontrolü
```
realpath --relative-to /usr/share/zoneinfo /etc/localtime
```

### Chromium Yükleme Adımları

1-) Klasör oluşturma
```
mkdir chromium
cd chromium
```

2-) docker-compose.yaml dosyası oluşturmak
```
nano docker-compose.yaml
```

```
---
services:
  chromium:
    image: lscr.io/linuxserver/chromium:latest
    container_name: chromium
    security_opt:
      - seccomp:unconfined #optional
    environment:
      - CUSTOM_USER=Kullanıcı
      - PASSWORD=Sifre
      - PUID=1000
      - PGID=1000
      - TZ=Europe/London
      - CHROME_CLI=https://github.com/okannako/depinnodes/tree/main #optional
    volumes:
      - /root/chromium/config:/config
    ports:
      - 3010:3000   #Change 3010 to your favorite port if needed
      - 3011:3001   #Change 3011 to your favorite port if needed
    shm_size: "1gb"
    restart: unless-stopped
```

3-) Chromium Başlatmak
```
docker compose up -d
```
```
http://VpsIP:3010/
```

4-) Chromium Durdurma ve Silme
```
docker stop chromium
docker rm chromium
docker system prune
```
