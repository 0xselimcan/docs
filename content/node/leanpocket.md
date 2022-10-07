---
title: LeanPocket
menuTitle: LeanPocket
weight: 35
description: Run multiple nodes on the same hardware without scaling your resource requirements.
---

LeanPocket (sometimes stylized as "LeanPOKT") is a mechanism that allows multiple Pocket nodes running on a single server to consolidate their resources, resulting in better scalability and reduced hardware costs.

Lean Pocket is a feature available from version 0.9.2 and above.

## Background

Pocket Core is a single-threaded application. This means that if you wanted to run multiple Pocket nodes on the same server, each node, running its own isolated process, would each use their own resources: RAM, disk space, network throughput, etc. As many aspects of nodes use shared resources (copies of the blockchain, most notably) this was deemed very inefficient.

For example, at the time of writing, a Pocket node requires a minimum of 400GB of disk space and 16GB of RAM, and that's not including the requirements of [the other chains the node may be servicing](/supported-blockchains).

LeanPocket is an optimization to the core client to allow nodes to share resources, significantly reducing resource requirements. This is a non-consensus-breaking change to the client.

<!-- Add charts here -->

{{% notice style="info" %}}
LeanPocket is specifically geared toward multiple nodes running on the same server. If you are running a single node, or have each of your nodes running on different server, LeanPocket will not have any benefit or effect.
{{% /notice %}}

## Setup

To enable LeanPocket:

1. Create a new `lean_nodes_keys.json` file inside your Pocket configuration directory (typically `.pocket`).

2. Format the file with a JSON array of private keys:

   ```
   [ { "priv_key": "0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef" } ]
   ```

<!-- What does the array with multiple private keys look like? -->
<!-- Add note on where you can find your private key -->
<!-- Is the first one in the list always the "parent" node? Do we say this? -->

3. Set/update your validators:

   ```
   pocket accounts set-validators <path/to/lean_nodes_keys.json>
   ```

   <!-- Are the commands get-validators / set-validators (plural) new because of LeanPocket? Do they replace the singular versions of get-validator/set-validator? What happens if you use one instead of the other? -->

4. Edit your node's `config.json` file to add `lean_pocket: "true";` in the `pocket_config` block.

   <!-- Need to verify this -->

5. Restart your Pocket service.

Using `--forceSetValidators` as an argument when starting Pocket will ensure that your validators are updated every time the process starts. If using this argument, you won't have to use the `set-validators` command when you update your `lean_node_keys.json` file.

Do not use `--useCache` as an argument when starting Pocket, as it can delay restarting your node.


## Other useful commands

**Get validators**

```
pocket accounts get-validators
```

<!-- What other commands would be useful to add here? -->

## Testing

<!-- What's the simplest way to test that LeanPocket is working? -->

## Considerations and Risks

* Because your nodes are commingling resources with LeanPocket, there is a risk of downtime on all of your nodes simultaneously. Depending on your risk for downtime, we recommend that you institute some form of failover system for your nodes, or perhaps divide your nodes into multiple LeanPocket-managed groups.
* Since all LeanPocket nodes use the same configuration, it is not possible to serve different groups of chains on each individual node. Each node uses the same `chains.json` file.
* If you are converting your node fleet into using fewer servers (or just one), you will want to ensure that your server open file limit (`ulimit` and `fd` on Linux-based systems) is sufficient, as these values still scale linearly with each additional node.
* We don't recommend running Validator nodes via Lean Pocket at this time, because of the increased importance of the consensus state on the network.
* Be aware that without LeanPocket, there is a one-to-one correlation between a node and its Peering ID, so when you use `set validator`, it will set the Peering ID to be the validator. However, with LeanPocket, the first node in the list is your Peering ID for every node managed by Lean Pocket.

<!-- Is point #3 about ulimits correct? -->
<!-- What's the key/value for Peering ID in the config file? -->

{{% notice style="info" %}}
For more information on LeanPocket:
* [PEP-35: The v0 Optimization, LeanPocket](https://forum.pokt.network/t/pep-35-the-v0-optimization-leanpocket/3042)
* [LeanPOKT Proposal & Design Specification (GitHub)](https://github.com/pokt-network/pocket-core/issues/1437)
{{% /notice %}}