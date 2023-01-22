---
description: >-
  Here you find information how to run a full node as well as produce blocks. If
  you want to run a sentry, most of the instructions are the same.
---

# 🔐 For Node Operators

### Run a full node

We assume you are on some sort of Ubuntu/Debian Linux. Other Linux distributions and macOS works very similar.

#### 1. Update your OS and install dependencies

```shell
sudo apt update
# Maybe also this if you want: sudo apt upgrade -y && reboot
sudo apt install -y make gcc build-essential git jq
```

#### 2. Install Go

This way: [https://go.dev/doc/install](https://go.dev/doc/install)

#### 3. Clone the nois full node repository

```shell
git clone https://github.com/noislabs/full-node.git
cd full-node/full-node/
git checkout nois-testnet-003
```

#### 4. Build and install the noisd binary

```shell
# This creates the binary in ./out/noisd and move it
# to your /usr/local/bin folder.
# Alternatively any other installation folder in $PATH can be used.
./build.sh
sudo mv out/noisd /usr/local/bin
```

#### 5. Check the installation (also creates folder `$HOME/.noisd`):

```shell
noisd version
```

#### 6. Adapt the block time and minimum gas prices parameters

```shell
export CONFIG_DIR="$HOME/.noisd/config"

# Update app.toml
sed -i 's/minimum-gas-prices =.*$/minimum-gas-prices = "0.05unois"/' $CONFIG_DIR/app.toml

# Update block time settings (config.toml)
sed -i 's/^timeout_propose =.*$/timeout_propose = "2000ms"/' $CONFIG_DIR/config.toml \
  && sed -i 's/^timeout_propose_delta =.*$/timeout_propose_delta = "500ms"/' $CONFIG_DIR/config.toml \
  && sed -i 's/^timeout_prevote =.*$/timeout_prevote = "1s"/' $CONFIG_DIR/config.toml \
  && sed -i 's/^timeout_prevote_delta =.*$/timeout_prevote_delta = "500ms"/' $CONFIG_DIR/config.toml \
  && sed -i 's/^timeout_precommit =.*$/timeout_precommit = "1s"/' $CONFIG_DIR/config.toml \
  && sed -i 's/^timeout_precommit_delta =.*$/timeout_precommit_delta = "500ms"/' $CONFIG_DIR/config.toml \
  && sed -i 's/^timeout_commit =.*$/timeout_commit = "1800ms"/' $CONFIG_DIR/config.toml
  
# Update p2p setting (config.toml)
export MY_EXTERNAL_ADDR="$(curl -sS https://api.ipify.org):26656" # or set explicitely if this API does not return the correct value
sed -i 's/external_address =.*$/external_address = "'$MY_EXTERNAL_ADDR'"/' $CONFIG_DIR/config.toml \
  && sed -i 's/^max_num_inbound_peers =.*$/max_num_inbound_peers = 80/' $CONFIG_DIR/config.toml \
  && sed -i 's/^max_num_outbound_peers =.*$/max_num_outbound_peers = 40/' $CONFIG_DIR/config.toml
```

#### 7. Download the genesis file

```shell
wget -O "$HOME/.noisd/config/genesis.json" https://raw.githubusercontent.com/noislabs/testnets/main/nois-testnet-003/genesis.json
```

#### 8. Draw the rest of the fucking owl

The rest is similar to running a standard cosmos validator. You can check this cosmos hub docs [link](https://hub.cosmos.network/main/validators/validator-setup.html) for more details. For the faucet, rpc links, permanent peers and similar details visit [networks-and-contracts](../../networks-and-contracts/ "mention")
