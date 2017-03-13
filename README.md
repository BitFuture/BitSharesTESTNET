# TESTNET BitShares witness node and CLI wallet on Ubuntu 16.04 LTS VM

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FryanRfox%2FBitSharesTESTNET%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>
<a href="http://armviz.io/#/?load=https://raw.githubusercontent.com/ryanRfox/BitSharesTESTNET/master/azuredeploy.json" target="_blank">
    <img src="http://armviz.io/visualizebutton.png"/>
</a>

# What is TESTNET BitShares?

> The TESTNET for the BitShares project. [Learn more](https://github.com/BitSharesEurope/testnet) 

# Deployment Process

1. **Signup** for an Azure account [FREE $200 credit](https://azure.microsoft.com/en-us/free/)
1. **Deploy** the Azure template [click here](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FryanRfox%2FBitSharesTESTNET%2Fmaster%2Fazuredeploy.json)
1. **Contribute** to the TESTNET [learn more](https://github.com/BitSharesEurope/testnet) 

# Template Parameters

When you click the *Deploy to Azure* icon above, you will be redirected to your Azure account to sign in. The resource template requires the following parameters:

* `adminUsername`: This is the account for connecting to your Azure virtual machine running the TESTNET BitShares client.
* `adminPassword`: This is your password for the host.  Azure requires passwords to have one upper case, one lower case, a special character, and a number.
* `dnsLabelPrefix`: This is used as both the VM name and DNS name of your public IP address.  Please ensure it is unique within your subscription namespace.
* `newWitnessName`: This is the name of your new TESTNET BitShares account. It will registered to the blockchain and configured as an eligeble witness (votes required to become active). The name must be novel on the TESTNET BitShares blockchain.
* `vmSize`: This is the size of the VM to use. [Reference](https://docs.microsoft.com/en-us/azure/virtual-machines/virtual-machines-linux-sizes)

# What the Template Actually Does

1. **Download:** The Azure virtual machine downloads the [TESTNET BitShares source code](https://github.com/BitSharesEurope/testnet) from GitHub
1. **Build:** The project gets built, configured and the `testnet service` starts
1. **TODO: Register:** Your desired name will be registered to the blockchain
1. **Sync:** The TESTNET BitShares blockchain will be synced from the P2P network
1. **Witness:** Once synced your witness node will be fully functional on the network

# Getting Started Tutorial

1. Click the `Deploy to Azure` icon above
1. Complete the template parameters, accept the terms and click Create
1. Wait about 15 minutes for the VM to spin up and install the bits
1. Connect to the VM via SSH using the DNS name assigned to your Public IP
1. TODO: Review the install log file for status update `$ tail ~/status.log`
1. TODO: Obtain your credentials
  1. View the brain_key.json file `$ tail ~/brain_key.json`
  2. Copy the WIF private key for use in a future step
  3. Save the contents of this file in a secure location
1. Launch the cli-wallet: `$ sudo ~/launch_testnet_wallet.sh`
1. Assign a secure password `> set_password use_a_secure_password_here` (note: displayed on screen)
1. Unlock the wallet `>>> unlock my_secure_password_from_above` the prompt will change to `unlocked >>>`
1. Check the sync status of the blockchain `unlocked >>> info` 
  1. Within the results, note the values for _"time"_ and _"head_block_age"_
  1. While syncing these values will be the time of the synced block and how old that block is. Be patient
  1. Syncing is complete when these values are just a few seconds old
1. Wait for your desired name to be registered on the blockchain
1. Import your private key into your wallet
  1. Ensure the wallet is unlocked
  1. Use the WIF private key you copied earlier from the ~/brain_key.json file `unlocked >>> import_key your_account_name 5yourPrivateKeyStartsWith5plusMoreChars true`
1. Exit to save the wallet using `ctrl-d` 
1. View your wallet file: `tail ~/testnet/cli_wallet/wallet.json`
2. Save your wallet file in a secure location as a backup.

# Documentation

Please review the [TESTNET BitShares documentation](https://github.com/BitSharesEurope/testnet) to learn more. 

# Licensing

Copyright (c) 2015 Cryptonomex, Inc., and contributors.

[The MIT License](https://github.com/BitSharesEurope/testnet/blob/testnet/LICENSE.md)

# Troubleshooting

* Check current status of `testnet service` 
Issuing `service testnet status` will return the current state of the testnet daemon. Normal operation will return:
`Active: active (running)`
* The `testnet service status` is inactive (dead)
If the response contains `Active: inactive (dead)` please start the service:
`service testnet start`
* The `testnet service` fails to start
If after attempting to start the testnet service as above and the response remains `Active: inactive (dead)` please start the service with the addition of the `--resync-blockchain` switch:
`service testnet start --resync-blockchain` 
This will drop the blockchain database and download it anew from network peers.
* The `cli_wallet` will not connect to withness_node
The `cli_wallet` has a dependency on the `testnet.service` being in the `Active: active (running)` state. Verify the current as noted above. 