---
layout: post
title: RaspberryPi Bitcoin Full Node
date: 2017-12-26
---

# RaspberryPi Bitcoin Full Node

## Links

[random string generator](https://www.random.org/strings/?num=10&len=20&digits=on&upperalpha=on&loweralpha=on&unique=on&format=html&rnd=new)
[ElectrumX bootstrap data](https://jdubya.info/electrum/ElectrumX/)

## Helpful Commands

- list installed packages

`apt --installed list`

- list installed packages through `apt-get`

`zcat /var/log/apt/history.log.*.gz | cat - /var/log/apt/history.log | grep -Po '^Commandline: apt-get install (?!.*--reinstall)\K.*'`

## Installing Electrum Server

[How-To](https://github.com/kyuupichan/electrumx/blob/master/docs/HOWTO.rst)

## 1. [Upgrade Raspian to Stretch distibution](https://github.com/kyuupichan/electrumx/blob/master/contrib/raspberrypi3/install_electrumx.sh)

```
# upgrade raspbian to 'stretch' distribution
sudo echo 'deb http://mirrordirector.raspbian.org/raspbian/ testing main contrib non-free rpi' > /etc/apt/sources.list.d/stretch.list
sudo apt-get update
sudo apt-get dist-upgrade
sudo apt-get autoremove
```

## 2. Install `leveldb`

`sudo apt-get install python3-leveldb libleveldb-dev`

- ensure python dependencies are installed
- check packages that are installed already

`pip list`

- upgrade `setuptools`

`pip3 install --upgrade pip setuptools wheel`

- install required packages

`pip3 install aiohttp pylru leveldb plyvel`

- clone `electrumx`

```bash
mkdir ~/source
cd ~/source
git clone https://github.com/kyuupichan/electrumx.git
cd electrumx
```

## 3. Install Python 3.6

```bash
wget https://www.python.org/ftp/python/3.6.4/Python-3.6.4.tgz
tar xzvf Python-3.6.4.tgz
cd Python-3.6.4
./configure
make
sudo make install
```

- Install required Python packages

```bash
sudo pip3 install --upgrade pip setuptools wheel
sudo pip3 install aiohttp pylru leveldb plyvel
```

## 4. Install and Setup `electrumx`

`python3 setup.py install`

- create data dir

```bash
mkdir ~/.electrumx
cd ~/.electrumx
```

- [download one of these files to speed up syncing the blockchain](https://jdubya.info/electrum/ElectrumX/)

- list disk drives, and mount the external drive in order to copy to data dir

```bash
cd ~/.electrumx
sudo fdisk -l
sudo mount /dev/sda1 /mnt
sudo cp /mnt/ElectrumX_1.2.0_RocksDB_2017-11-11.tgz .
```

## 5. Generate Self-Signed SSL Cert

```bash
openssl genrsa -passout pass:x -out server.pass.key 2048
openssl rsa -passin pass:x -in server.pass.key -out server.key
rm server.pass.key
openssl req -new -key server.key -out server.csr
openssl x509 -req -days 1825 -in server.csr -signkey server.key -out server.crt
```

- create ElectrumX service

```bash
sudo -s
cp /home/pi/source/electrumx/contrib/systemd/electrumx.service /lib/systemd/system/
```

- Edit config

> You need to edit at least ExecStart and User variables
> ExecStart=/home/bitcoin/source/electrumx/electrumx_server.py
> User=bitcoin

- listing users

`cat /etc/passwd |grep "/home" |cut -d: -f1`

`vi /lib/systemd/system/electrumx.service`

> ExecStart=/usr/local/bin/electrumx_server.py
> User=pi

- create the service's config

`vi /etc/electrumx.conf`

```
COIN = Bitcoin
DAEMON_URL = http://bitcoinrpc:Z6quJreCCqJ6dEemLR1L@localhost:8332/
NET = mainnet
DB_DIRECTORY = /home/pi/.electrumx/
DB_ENGINE = leveldb

USERNAME = pi
ELECTRUMX = /usr/local/bin/electrumx_server.py

ANON_LOGS = I <3 Privacy!
BANNER_FILE = banner.txt
DONATION_ADDRESS = 1D3qAT38EqkZu9RyFgPEZUL3qmgohc6do

CACHE_MB = 400
MAX_SESSIONS = 250

SSL_CERTFILE = /home/pi/.electrum/server.crt
SSL_KEYFILE = /home/pi/.electrum/server.key

HOST = ::
TCP_PORT = 5001
SSL_PORT = 5002
```

## 6. Download `electrumx-banner-updater`

```bash
cd source
git clone https://github.com/shsmith/electrumx-banner-updater.git
cd electrumx-banner-updater/
chmod 777 update-electrumx-banner
vi update-electrumx-banner
```

- edit `update-electrumx-banner`

```bash
#!/bin/bash

# update electrumx banner to report bitcoin memory pool, fees, height and time since last block
# activate with cron job similar to this
#*/2 * * * *  ~/bin/update-electrumx-banner

# specify your bitcoin-cli location

BITCOIN_CLI=/usr/local/bin/bitcoin-cli

# specify your bitcoin data directory
BITCOIN_DATADIR=/home/pi/.bitcoin

# specify the location of your electrumx banner file
# note: uses %{BANNER}.template as the template (top portion) of each banner update
BANNER=~/.electrumx/banner.txt
```

## 7. Launch ElectrumX Service

[Help for `systemctl` Command](http://www.ebugg-i.com/technology/linux/what-is-systemctl-linux-command.html)

`sudo -s`

`systemctl start electrumx`

- check the output

`journalctl -u bitcoin -f`

- if it gives you no errors, enable the service

`systemctl enable electrumx`

- listing services to view status of electrumx

`systemctl --all`

## 8. Accept Incoming Connections

- Create `iptables` Rules

- example includes ports for `bitcoind` (8332, 8333) and `electrumx` (50002, 50003)

```bash
iptables -A INPUT -p tcp --dport 8333 -j ACCEPT
iptables -A INPUT -p tcp --dport 8332 -j ACCEPT
iptables -A INPUT -p tcp --dport 50001 -j ACCEPT
iptables -A INPUT -p tcp --dport 50002 -j ACCEPT
iptables -A INPUT -p udp --dport 8333 -j ACCEPT
iptables -A INPUT -p udp --dport 8332 -j ACCEPT
iptables -A INPUT -p udp --dport 50001 -j ACCEPT
iptables -A INPUT -p udp --dport 50002 -j ACCEPT
iptables-save
```

- make iptables rules persist after reboot

```
apt-get install iptables-persistent
```

`sudo iptables -L` or `sudo iptables -L -n -v`

> Chain INPUT (policy ACCEPT)
> target     prot opt source               destination
> ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:8333
> ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:8332
> ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:50001
> ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:50002
> ACCEPT     udp  --  anywhere             anywhere             udp dpt:8333
> ACCEPT     udp  --  anywhere             anywhere             udp dpt:8332
> ACCEPT     udp  --  anywhere             anywhere             udp dpt:50001
> ACCEPT     udp  --  anywhere             anywhere             udp dpt:50002
> 
> Chain FORWARD (policy ACCEPT)
> target     prot opt source               destination
> 
> Chain OUTPUT (policy ACCEPT)
> target     prot opt source               destination

### Checking Ports Remotely

**`netcat`**

- get a list of open ports

`netstat -lntu`


- test if remote port is reachable

`nc -zv 127.0.0.1 80`

- multiple ports

`nc -zv 127.0.0.1 22 80 8080`

- range of ports

`nc -zv 127.0.0.1 20-30`

## 9. Start Electrum Client

- use `--oneserver` for added privacy

`electrum --oneserver`

