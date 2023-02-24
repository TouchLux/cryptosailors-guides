<p align="center">
 <img src="https://i.postimg.cc/bwgvTC8N/Moonbeam.jpg"width="900"/></a>
</p>

# In this guide, we will describe how to setup the Moonbeam testnet RPC node.

#### System requirements:
- 8 CPU cores
- 16 GB RAM
- 1 TB SSD (Recommended)

## 1. Node Preparation.
```
sudo apt update && sudo apt upgrade -y
```
```
sudo apt install make clang pkg-config libssl-dev libclang-dev build-essential git curl ntp jq llvm tmux htop screen unzip cmake clang protobuf-compiler libprotobuf-dev -y
```
Install Rust and its prerequisites:
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
```
Update your PATH environment variable by running:
```
source $HOME/.cargo/env
```

## 2. Create Moonbeam User and add it to Sudo group.
```
adduser --gecos "" moonbeam
sudo usermod -aG sudo moonbeam
```

## 3. Install golang go.
Enter the shell of the moonbeam account:
```
su -s /bin/bash moonbeam
```
Install the latest version of the Golang "Go" tool:
```
cd /usr/src
sudo rm -Rf go*
sudo wget https://go.dev/dl/`curl -s https://go.dev/dl/?mode=json | jq -r '.[0].version'`.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go*.linux-amd64.tar.gz
```
```
cat <<EOF >> ~/.profile
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
EOF
```
```
source ~/.profile
go version
sudo rm -rf /usr/src/go*.linux-amd64.tar.gz
```

## 4. Install Moonbeam.\
Check out to the latest [release](https://github.com/PureStake/moonbeam/releases). In this example the latest release is `v0.29.0`
```
RELEASE=v0.29.0
```
Go to the user home directory:
```
cd ~
```
Clone the Moonbeam repo:
```
git clone https://github.com/PureStake/moonbeam
cd moonbeam
git checkout $RELEASE
cargo build --release
```
## 5. Setup the Sytemd Service.
```
sudo tee <<EOF >/dev/null /etc/systemd/system/moonbeam.service
[Unit]
Description="Moonbeam systemd service"
After=network.target
StartLimitIntervalSec=0
[Service]
Type=simple
Restart=on-failure
RestartSec=10
User=moonbeam
SyslogIdentifier=moonbeam
SyslogFacility=local7
KillSignal=SIGHUP
ExecStart=/home/moonbeam/moonbeam/target/release/moonbeam \\
     --port 30333 \
     --rpc-port 9935 \
     --ws-port 9944 \
     --rpc-cors=all \
     --unsafe-rpc-external \
     --unsafe-ws-external \
     --execution wasm \
     --wasm-execution compiled \
     --pruning=archive \
     --trie-cache-size 0 \
     --db-cache 16000 \
     --base-path /home/moonbeam/moonbram \
     --chain alphanet \
     --name "YOUR_NODE_NAME" \
     -- \
     --port 30334 \
     --rpc-port 9934 \
     --ws-port 9945 \
     --execution wasm \
     --pruning=1000 \
     --name="YOUR_NODE_NAME"
[Install]
WantedBy=multi-user.target
EOF
```
## 5. Add Service to autostart and run it.
```
sudo systemctl enable moonbeam
sudo systemctl start moonbeam
```
Verify that the service is running and works fine:
```
sudo systemctl status moonbeam
sudo journalctl -u moonbeam -f -n 100
```
You should see something like this:
```
20:53:04 [Relaychain] ⚙️  Syncing 352.4 bps, target=#14306783 (40 peers), best: #12567 (0x8b98…8882), finalized #12288 (0x006d…48ea), ⬇ 418.4kiB/s ⬆ 209.6kiB/s
20:53:04 [🌗] ⚙️  Syncing 60.0 bps, target=#2977702 (19 peers), best: #2155 (0x4e4f…f202), finalized #0 (0xfe58…b76d), ⬇ 199.3kiB/s ⬆ 2.6kiB/s
20:53:09 [Relaychain] ⚙️  Syncing 350.6 bps, target=#14306783 (40 peers), best: #14320 (0x5cd0…26bc), finalized #13824 (0x4666…eb66), ⬇ 385.7kiB/s ⬆ 206.0kiB/s
20:53:09 [🌗] ⚙️  Syncing 59.4 bps, target=#2977702 (19 peers), best: #2452 (0x9d6a…1cff), finalized #0 (0xfe58…b76d), ⬇ 199.1kiB/s ⬆ 1.5kiB/s
```

You can run a cURL request to see the status of your node:
```
curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "eth_syncing", "params":[]}' localhost:9935
```

When the node will be successfully synced, the output from above will print 
```
{"jsonrpc":"2.0","result":false,"id":1}
```

#

👉[Hetzner — server rental](https://hetzner.cloud/?ref=NY9VHC3PPsL0)

👉[SSH terminal MobaxTerm](https://mobaxterm.mobatek.net/download.html)

👉[Moonbeam Official guide](https://docs.moonbeam.network)

🔰[Our Telegram Channel](https://t.me/CryptoSailorsAnn)

🔰[Our WebSite](cryptosailors.tech)

🔰[Our Twitter](https://twitter.com/Crypto_Sailors)

🔰[Our Youtube](https://www.youtube.com/@CryptoSailors)

#### Guide created by 
AntonM | C.Sailors#6222 / @antonmashnin