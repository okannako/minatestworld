![Car Crash Tv (4)](https://github.com/okannako/minatestworld2/assets/73176377/77f8d8ac-3783-4e11-a74b-56db1f5b55f9)

- Arkadaşlar, bu kılavuz sadece Block Producer(BP) için hazırlanmıştır. Video ile devam ederseniz çok daha rahat kurarsınız

## Tavsiye Edilen Minimum Sistem Gereksinimleri
- CPU: 8 Core
- RAM: 16GB
- SSD: 10GB
- İşletim Sistemi: Ubuntu 20.04LTS

## Kodlar

### Güncelleme & Docker
```
sudo apt-get update
sudo apt-get install -y mina-berkeley=2.0.0rampup5-55b7818
curl -fsSL https://get.docker.com -o get-docker.sh && sh get-docker.sh
sudo chmod 666 /var/run/docker.sock
```

### Peer & Key
- Mail ile gelen bilgileri bilgisayarımıza indirip dosyaları açarak bilgileri alıyoruz.
- Bu tuşlara basarak nano ekrandan çıkıyoruz > CTRL + X Y girmek
- my-wallet için ""box_primitive"" ile başlayan kod, my-walley.pub için B62 ile başlayan adres girilecek.
```
wget -O ~/peers.txt  https://storage.googleapis.com/seed-lists/testworld-2-0_seeds.txt
mkdir $HOME/.mina-config
mkdir ~/keys
sudo chmod 700 ~/keys
sudo apt-get install nano
sudo nano ~/keys/my-wallet
sudo chmod 600 ~/keys/my-wallet
sudo nano ~/keys/my-wallet.pub
sudo chmod 600 ~/keys/my-wallet.pub
sudo chown -R root:root ~/keys
sudo chown -R root:root ~/keys/my-wallet 
```

### Generation of libp2p Keypair
- Oluştururken ki girdiğiniz şifreyi bir yere kaydedin.
```
mina libp2p generate-keypair -privkey-path ~/keys/my-libp2p-key
```

### Run Docker
- PRIVKEY_PASS için mail ile alınan password dosyanın içerisindeki değeri tırnak içine giriyoruz. LIBP2P_PASS için, libp2p oluşturulurken girdiğimiz şifreyi giriyoruz.
- yournodeip'niz, node çalıştığı sistemin ip adresini girin.
```
docker run --name mina -d \
-p 8302:8302 \
--restart=always \
--mount "type=bind,source=$(pwd)/keys,dst=/keys,readonly" \
--mount "type=bind,source=$(pwd)/.mina-config,dst=/root/.mina-config" \
-e MINA_PRIVKEY_PASS="password" \
-e UPTIME_PRIVKEY_PASS="password" \
-e MINA_LIBP2P_PASS="password" \
gcr.io/o1labs-192920/mina-daemon:2.0.0rampup5-55b7818-focal-berkeley \
daemon \
--block-producer-key /keys/my-wallet \
--uptime-submitter-key /keys/my-wallet \
--internal-tracing \
--insecure-rest-server \
--log-level Debug \
--file-log-level Debug \
--enable-peer-exchange true \
--libp2p-keypair /keys/my-libp2p-key \
--log-precomputed-blocks true \
--external-ip yournodeip \
--max-connections 200 \
--peer-list-url https://storage.googleapis.com/seed-lists/testworld-2-0_seeds.txt  \
--generate-genesis-proof true \
--node-status-url https://nodestats-itn.minaprotocol.tools/submit/stats \
--node-error-url https://nodestats-itn.minaprotocol.tools/submit/stats \
--uptime-url https://block-producers-uptime-itn.minaprotocol.tools/v1/submit  \
--file-log-rotations 50 \
--stop-time 24 \
--itn-keys f1F38+W3zLcc45fGZcAf9gsZ7o9Rh3ckqZQw6yOJiS4=,6GmWmMYv5oPwQd2xr6YArmU1YXYCAxQAxKH7aYnBdrk=,ZJDkF9EZlhcAU1jyvP3m9GbkhfYa0yPV+UdAqSamr1Q=,NW2Vis7S5G1B9g2l9cKh3shy9qkI1lvhid38763vZDU=,Cg/8l+JleVH8yNwXkoLawbfLHD93Do4KbttyBS7m9hQ= \
--itn-graphql-port 3089 \
--log-snark-work-gossip true
```

- Aşağıdaki kodlarla Docker'a giriyoruz ve node durumuna bakıyoruz.
```
docker exec -it mina bash
mina client status
```