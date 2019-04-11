# Connect to a Testnet

This document explains how to connect to the latest Testnet of the [Lino Blockchain](https://github.com/lino-network/lino).

NOTE: We are aware this documentation is sub-par and are actively working to improve both the tooling and the documentation to make this as painless as possible. In the meantime, join us on [Discord](https://discord.gg/TUxp3ww) for technical support. Thanks very much for your patience :)

## Software Setup (Manual Installation)

Follow these instructions to install the Lino Core and connect to the latest Testnet. This instructions work for both a local machine and a VM in a cloud server. Lino Core is based on [Cosmos SDK](https://github.com/cosmos/cosmos-sdk) so the setup process is similar.

If you want to run a non-validator full-node, installing the Lino Core on a Cloud server is a good option. However, if you are want to become a validator for Lino you should look at more complex setups, including [Sentry Node Architecture](https://github.com/cosmos/cosmos/blob/master/VALIDATORS_FAQ.md#how-can-validators-protect-themselves-from-denial-of-service-attacks), to protect your node from DDOS and ensure high-availability (see the [technical requirements](https://github.com/cosmos/cosmos/blob/master/VALIDATORS_FAQ.md#technical-requirements)). You can find more information on validators in our [website](https://lino.network).

### Install [GNU Wget](https://www.gnu.org/software/wget/)

**MacOS**

```
brew install wget
```

**Linux**

```
sudo apt-get install wget make
```

Note: You can check other available options for downloading `wget` [here](https://www.gnu.org/software/wget/faq.html#download).


### Install [Go](https://golang.org/)

Install `go` following the [instructions](https://golang.org/doc/install) in the official golang website.
You will require **Go 1.11+** for this tutorial.

Example
```
$ wget https://dl.google.com/go/go1.11.linux-amd64.tar.gz

$ sudo tar -xvf go1.11.linux-amd64.tar.gz
$ sudo mv go /usr/local
```

#### Set GOPATH

First, you will need to set up your `GOPATH`. Make sure that the location `$HOME` is something like `/Users/<username>`, you can corroborate it by typing `echo $HOME` in your terminal.

Go to `$HOME` with the command `cd $HOME` and open the the hidden file `.bashrc` with a code editor and paste the following lines \(or `.bash_profile` if your're using OS X\).

Example
```
$ export GOROOT=/usr/local/go
$ export GOPATH=$HOME/go
$ export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
```

### Install Lino Core

Now we can fetch the correct versions of each dependency by running:

```
$ mkdir -p $GOPATH/src/github.com/lino-network/
$ cd $GOPATH/src/github.com/lino-network/
$ git clone https://github.com/lino-network/lino
$ cd lino
$ git checkout v0.2.9
$ make get_tools && make install
```


## Full Node Setup

If you go through above process, you should be able to start a node with single validator. The genesis account's private key will show up at last step of above process. Now you can start you own node by running:

```
$ lino init
$ lino start
```

If you want to connect to Lino Testnet, you should clone this repo and copy genesis file.

```
$ lino init
$ git clone https://github.com/lino-network/testnets.git
$ cp -a testnets/lino-testnet-upgrade1/genesis.json $HOME/.lino/config/genesis.json
$ cp -a testnets/lino-testnet-upgrade1/config.toml $HOME/.lino/config/config.toml
$ lino unsafe-reset-all
```

If you wanna sync the blocks from the first block, please download all previous blockchain data from S3:

```
$ wget https://s3.amazonaws.com/lino-blockchain-opendata/prd/prevstates.tar.gz
$ tar -xzvf prevstates.tar.gz -C ~/.lino/
```

Lastly change the `moniker` string in the `$HOME/.lino/config/config.toml`to identify your node.

```
# A custom human readable name for this node
moniker = "<your_custom_name>"
```

Optional: to increase number of connections

```
$ ulimit -n 4096
```

## Run a Full Node

Start the full node:

```
$ lino start
```
You node should start syncing to our Lino Testnet.

## Join as Validator

Validator will get hourly inflation from Lino Blockchain. All validators are shown on [tracker](https://tracker.lino.network/#/).

To be a validator, you should first be a voter with 200000 locked LINO Points and have 200000 more Lino Points in balance. Then run a full node with public access IP and sync to current height. You can check current height on [tracker](https://tracker.lino.network/#/). To check your node's height you can access http://<your node's IP>:26657/status. Once your node sync with the latest height, you can run command on your node:
```
./linocli validator-deposit --user=<your username> --priv-key=<your wallet private key> --amount=200000 --chain-id=lino-testnet-upgrade1 --sequence=<your sequence number>
```
In above command, username is the voter who locks 200000 LINO Points and with 200000 LINO Points in balance. You can get your wallet private key on [Lino Account](https://account.lino.network/privkey). If you don't know your account's current sequence number, you can set sequence to 1 then execute the command. Blockchain will prompt your correct sequence number if it is incorrect. Once the command execute successfully, you should be able find yourself on [tracker](https://tracker.lino.network/#/) as the validator.

If you got error:
```
ERROR: CheckTx failed: (155) {"codespace":"lino","code":155,"message":"msg: signature verification failed, chain-id:lino-testnet-upgrade1, seq:n"}
```

just put the sequence number to the command above.
