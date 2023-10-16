![Car Crash Tv (4)](https://github.com/okannako/minatestworld2/assets/73176377/82a42385-c8d6-4f8d-8b84-1e4f81e03915)


- Friends, this guide is prepared only for Block Producer. If you proceed with the video (The video is in Turkish, but there are steps that are understood when you follow by watching.), you will install much more comfortably.

## Tavsiye Edilen Sistem Gereksinimleri
- CPU: 8 Core
- RAM: 16GB
- SSD: 10GB
- İşletim Sistemi: Ubuntu 20.04LTS

## Installation Codes

### Create a User

- It wants you to set a password when creating a user. (I recommend your creation). When it asks for a password, it won't appear on the screen when you press the keys, but it saves it in the background. Please be careful.

```
sudo adduser username
sudo visudo
username= <the name you type instead of the username>
echo $username
```
- After writing the codes above, we find the line ```root ALL=(ALL:ALL) ALL``` below and enter it as follows > ```username ALL=(ALL:ALL) ALL```. After that > CTRL+X Y Enter
```
sudo usermod -a -G sudo $username
sudo su - $username
```

### Updates & Docker
```
sudo rm /etc/apt/sources.list.d/mina*.list
echo "deb [trusted=yes] http://packages.o1test.net/ CODENAME rampup" | sudo tee /etc/apt/sources.list.d/mina-rampup.list
sudo apt-get update
sudo apt-get install -y mina-berkeley=2.0.0rampup5-55b7818
curl -fsSL https://get.docker.com -o get-docker.sh && sh get-docker.sh
sudo chmod 666 /var/run/docker.sock
```

### Peer & Key

- We download the information that comes with the mail to our computer and use it here.
- We exit the nano screen by pressing these keys > CTRL+X Y Enter
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
sudo chown -R $username:$username ~/keys
sudo chown -R $username:$username ~/keys/my-wallet 
```

### Generation of libp2p Keypair
```
mina libp2p generate-keypair -privkey-path ~/keys/my-libp2p-key
```

### Create .env 
```
nano .env
```
```
UPTIME_PRIVKEY_PASS="password"
MINA_LIBP2P_PASS="password"
MINA_PRIVKEY_PASS="password"
EXTRA_FLAGS="--log-json --log-snark-work-gossip true --internal-tracing --insecure-rest-server --log-level Debug --file-log-level Debug --config-directory /home/$username/.mina-config/ --external-ip $(wget -qO- eth0.me) --itn-keys  f1F38+W3zLcc45fGZcAf9gsZ7o9Rh3ckqZQw6yOJiS4=,6GmWmMYv5oPwQd2xr6YArmU1YXYCAxQAxKH7aYnBdrk=,ZJDkF9EZlhcAU1jyvP3m9GbkhfYa0yPV+UdAqSamr1Q=,NW2Vis7S5G1B9g2l9cKh3shy9qkI1lvhid38763vZDU=,Cg/8l+JleVH8yNwXkoLawbfLHD93Do4KbttyBS7m9hQ= --itn-graphql-port 3089 --uptime-submitter-key  /home/$username/keys/my-wallet --uptime-url https://block-producers-uptime-itn.minaprotocol.tools/v1/submit --metrics-port 10001 --enable-peer-exchange  true --libp2p-keypair /home/$username/keys/my-libp2p-key --log-precomputed-blocks true --max-connections 200 --generate-genesis-proof  true --node-status-url https://nodestats-itn.minaprotocol.tools/submit/stats --node-error-url https://nodestats-itn.minaprotocol.tools/submit/stats --file-log-rotations 500"
```

### Run Docker
```
docker run --env-file .env --name mina -d \
-p 8302:8302 \
--restart=always \
--mount "type=bind,source=`pwd`/keys,dst=/keys,readonly" \
--mount "type=bind,source=`pwd`/.mina-config,dst=/home/$username/.mina-config" \
gcr.io/o1labs-192920/mina-daemon:2.0.0rampup4-55b7818-focal-berkeley \
daemon \
--block-producer-key /home/$username/keys/my-wallet \
--peer-list-url  https://storage.googleapis.com/seed-lists/testworld-2-0_seeds.txt
```
