---
id: upgrading-geth
title: "How to Upgrade Geth"
sidebar_label: "Upgrading Geth"
sidebar_position: 2
description: "Updating geth on Core is straightforward: download the latest binary, stop your node, replace the binary, and restart to complete the network upgrade."
keywords: ["how", "to", "upgrade", "geth"]
tags: ["maintenance", "upgrade"]
hide_table_of_contents: false
pagination_next: "syncing-core-nodes"
pagination_prev: "node-maintenance"
---
# How to Upgrade Geth
---

Updating `geth` is as easy as it gets. You just need to download and install the newer version of `geth`, shut down your node, and restart with the new software. Geth will automatically use the data of your old node and sync the latest blocks that were mined since you shut down the old software.


### Step 1: Compile the New Version or download new pre-built binaries from the release


```bash
git clone https://github.com/coredao-org/core-chain

make geth
```bash

```bash
# Download pre-built binaries

# Linux
wget   $(curl -s https://github.com/coredao-org/core-chain/releases/latest |grep browser_ |grep geth_linux |cut -d\" -f4)
mv geth_linux geth
chmod -v u+x geth

# MacOS
wget   $(curl -s https://github.com/coredao-org/core-chain/releases/latest |grep browser_ |grep geth_mac |cut -d\" -f4)
mv geth_mac geth
chmod -v u+x geth
make geth
```bash

### Step 2: Stop Geth

```bash
$ pid=`ps -ef | grep geth | grep -v grep | awk '{print $2}'`
$ kill  $pid
```bash

### Step 3: Restart
:::note
Ensure that you use the same start-up command as before the upgrade.
:::


```bash
##
./geth --config ./config.toml --datadir ./node --cache 8000 --rpc.allow-unprotected-txs --txlookuplimit 0
```bash

## Related

- [Node Maintenance](/docs/node-maintenance)
- [Snapshot Sync](/docs/syncing-core-nodes)
- [Full Node Configuration](/docs/full-node)
