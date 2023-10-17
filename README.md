
![Car Crash Tv (4)](https://github.com/okannako/minatestworld2/assets/73176377/b5e207b1-6ee2-4b0f-9f15-9615c2c7d37a)


- Friends, this guide is prepared only for Block Producer. If you proceed with the video (The video is in Turkish, but there are steps that are understood when you follow by watching.), you will install much more comfortably.

## Recommended Minimum System Requirements
- CPU: 8 Core
- RAM: 16GB
- SSD: 10GB
- Operating System: Ubuntu 20.04LTS

## Codes

### Updates & Docker
```
sudo apt-get update
sudo apt-get install -y mina-berkeley=2.0.0rampup5-55b7818
curl -fsSL https://get.docker.com -o get-docker.sh && sh get-docker.sh
sudo chmod 666 /var/run/docker.sock
```

### Peer & Key
- We download the information that comes with the mail to our computer and use it here.
- We exit the nano screen by pressing these keys > CTRL+X Y Enter
- The code that starts with my-wallet in ""box_primitive"". the address starting with B62 will be entered for my-wallet.hub
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
- Don't forget to save the password you entered when creating the key.
```
mina libp2p generate-keypair -privkey-path ~/keys/my-libp2p-key
```

### Run Docker
- For PRIVKEY_PASS, we enter the value written in the password file in the file received by mail into quotation marks. For LIBP2P_PASS, we enter the password that we entered in the previous step when creating the Generation of libp2p Keypair.
- yournodeip, enter the ip address of the system where your node is running.
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

- We go into the Docker with the following codes and look at the status of our node.
```
docker exec -it mina bash
mina client status
```

