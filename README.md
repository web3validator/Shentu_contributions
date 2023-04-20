# Shentu_contributions




### web3validator
```
web3validator provides much more than security! We are actively participating in the development of the Network and Community by providing informational, technical and humanitarian support!
```


### Mainnet
* [Install node](https://github.com/web3validator/Shentu_contributions#install-binary)
* [State sync](https://github.com/web3validator/Shentu_contributions#state-sync)


## RPC

<details open>
  <summary><b>Сontents</b></summary>

RPC Endpoin
```
http://142.132.158.158:27557
```

LCD (Rest) API Endpoint
```
http://142.132.158.158:1317
```

GRPC Endpoint
```
http://142.132.158.158:9750
```
</details>



## Install binary

You can check new version here ([New_version_bin](https://github.com/shentufoundation/shentu/releases))
```
cd $HOME
git clone https://github.com/ShentuChain/shentu.git -b v2.6.0
cd $HOME/shentu
make install

```
### Genesis
```
wget http://65.108.66.34/genesis_shentu.json -O $HOME/.shentud/config/genesis.json

```
### Adderbook
```
wget http://65.108.66.34/addrbook_shentu.json -O $HOME/.shentud/config/addrbook.json

```
### You need to install `shentud.service`
```
wget http://65.108.66.34/shentud.service -P /etc/systemd/system/

```
If you want to quickly catch up with the network, use this [State Sync](https://github.com/web3validator/Shentu_contributions#state-sync)

```
sudo systemctl start shentud && sudo journalctl -u shentud -f --no-hostname -o cat
```



## State sync

<details >
  <summary><b>Pruning</b></summary>
  
  ```
  pruning = "default"
  
  pruning-keep-recent = "0"
  pruning-keep-every = "0"
  pruning-interval = "0"
  ```
  
</details>

  ```
  SNAP_RPС="http://142.132.158.158:27557"
  peers="db097e5538d5ad5fc6acb9911414cadd1aa9512f@142.132.158.158:27556"
  ```
  
  ### Let's put the height and trusthash to the-> `config.toml`
  ```
  LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
  BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
  TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash) && \
  echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
  sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
  s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
  s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
  s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
  s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.shentud/config/config.toml
  sed -i.bak -e  "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.shentud/config/config.toml

  ```
  ### Reset the database 
  ```
  sudo systemctl stop shentud && 
  cp $HOME/.shentud/data/priv_validator_state.json $HOME/.shentud/priv_validator_state.json.backup &&
  
  shentud tendermint unsafe-reset-all --home $HOME/.shentud --keep-addr-book &&
  
  cp $HOME/.shentud/priv_validator_state.json.backup $HOME/.shentud/data/priv_validator_state.json
  
  ```
  ## Restart node and check the logs
  ```
  sudo systemctl start shentud && sudo journalctl -u shentud -f --no-hostname -o cat
  ```








