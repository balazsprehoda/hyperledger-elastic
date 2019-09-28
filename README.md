# Blockchain Analyzer: Analyzing Hyperledger Fabric Ledger, Transactions

Mentor: Salman Baset [salmanbaset](https://github.com/salmanbaset)
Mentee: Balazs Prehoda [balazsprehoda](https://github.com/balazsprehoda)


## Contents
1. [Description](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#description)
2. [Background](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#background)
3. [Overview](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#overview)
4. [Prerequisites](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#prerequisites)
5. [Getting Started](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#getting-started)
6. [Fabric Network](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#fabric-network)
  6.1. [Basic network](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#basic-network)
  6.2. [Multichannel network](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#multichannel-network)
  6.3. [Applechain network](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#applechain-network)
  6.4. [(Optional) Generate makefile configuration](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#optional-generate-makefile-configuration)
  6.5. [Start the network](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#start-the-network)
  6.6. [Stop the network](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#stop-the-network)
7. [Dummyapp Application](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#dummyapp-application)
  7.1. [Installation](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#installation)
  7.2. [Configuration](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#configuration)
  7.3. [User enrollment and registration](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#user-enrollment-and-registration)
  7.4. [Invoke transactions](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#invoke-transactions)
  7.5. [Query](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#query)
8. [Appleapp Application](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#appleapp-application)
  8.1. [Installation](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#installation-1)
  8.2. [Configuration](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#configuration-1)
  8.3. [User enrollment and registration](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#user-enrollment-and-registration-1)
  8.4. [Invoke transactions](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#invoke-transactions-1)
  8.5. [Query](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#query-1)
9. [Elastic Stack](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#elastic-stack)
  9.1. [Credit](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#credit)
  9.2. [Description](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#description-1)
  9.3. [Start](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#start)
  9.4. [Stop and destroy](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#stop-and-destroy)
10. [Beats Agent](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#beats-agent)
  10.1. [Environment setup](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#environment-setup)
  10.2. [Configure fabricbeat](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#configure-fabricbeat)
  10.3. [Build fabricbeat](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#build-fabricbeat)
  10.4. [Start fabricbeat](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#start-fabricbeat)
  10.5. [Stop fabricbeat](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#stop-fabricbeat)
11. [License](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master#license)

## Description

Each blockchain platform, including Hyperledger Fabric, provide a way to record information on blockchain in an immutable manner. In the case of Hyperledger Fabric, information is recorded as a `key-value` pair. All previous updates to a `key` are recorded in the ledger, but only the latest value of a `key` can be easily queried using CouchDB; the previous updates are only available in ledger files. This mechanism makes it challenging to perform analysis of updates to a `key`, a necessary requirement for information provenance.

Currently, the project includes:

1. an Elastic beats module (in Go), that ships ledger data from a Hyperledger peer to an Elasticsearch instance. 

2. generic Kibana dashboards, that allow selection of a particular key and visualization updates to it (channel, id, timestamp etc) - similar to Hyperledger Explorer.

3. [scripts](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master/network) to create Fabric network in different configurations, i.e., basic and mulitchannel.

4. a [program](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master/apps/dummyapp) to generate test data

5. a [standalone utility](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master/dumper) to dump Fabric transaction data as JSON file for loading and analyzing in any document store. 

6. [scripts](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master/stack) to start and stop Elasticsearch and Kibana.


### Eventual goal
The eventual goal of this project is to evaluate transactions of any blockchain using any document databases or search engines such as MongoDB, CouchDB, or Elastic stack.

## Background

This project was developed as part of [Hyperledger summer internship program](https://wiki.hyperledger.org/display/INTERN/Analyzing+Hyperledger+Fabric+Ledger%2C+Transactions%2C+and+Logs+using+Elasticsearch+and+Kibana). 

## Overview

Basic data flow: 

![alt text](https://github.com/hyperledger-labs/blockchain-analyzer/blob/master/docs/images/Fabric%20Network%20And%20Fabricbeat.jpg "Fabric Network And Fabricbeat Basic Data Flow")

Dashboard example:
![alt text](https://github.com/hyperledger-labs/blockchain-analyzer/blob/master/docs/images/Overview_with_filter_multi.png "Dashboard with peer and channel selected")

## Prerequisites

Please make sure that you have set up the environment for the project. Follow the steps listed in [Prerequisites](https://github.com/hyperledger-labs/blockchain-analyzer/blob/master/docs/Prerequisites.md).

## Getting Started

To get started with the project, clone the git repository. It is important that you place it under `$GOPATH/src/github.com`  
```
$ mkdir $GOPATH/src/github.com -p
$ cd $GOPATH/src/github.com  
$ git clone https://github.com/hyperledger-labs/blockchain-analyzer.git
```

This project provides an automated way to try the main features. For details, see [Basic Demo](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master/docs/Basic_demo.md) and [Multichannel Demo](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master/docs/Multichannel_demo.md).

For a manual setup, follow the instructions provided in [Basic_setup_example.md](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master/docs/Basic_setup_example.md) or [Multichannel_setup_example.md](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master/docs/Multichannel_setup_example.md). For more customizable setup, please see the next sections on this page.


## Starting a Fabric Network

The `network` directory contains two network setups, `basic` and `multichannel`. 

### Basic network

It is a simple test network with four organizations, one peer per organization, a solo orderer communicating over TLS and a sample chaincode called `dummycc`. It writes deterministically generated hashes and (optionally) previous keys as value to the ledger.  

### Multichannel network

It is a test network setup with four organizations, two peers per organization, a solo orderer communicating over TLS and two channels:

1. `fourchannel`:
  * members: all four organizations
  * chaincode: `dummycc`: It writes deterministically generated hashes and (optionally) previous keys as value to the ledger.
2. `twochannel`:
  * members: only `Org1` and `Org3`
  * chaincode: `fabcar`: The classic fabcar example chaincode extended with a `getHistoryForCar()` chaincode function.

### Applechain network

It is a test network setup similar to basic network, but with different chaincode (`applechain`). The goal of this network is to imitate a supply chain use-case: crates of apples are harvested on farms, transported to factories as resources for jam and juice production, and the products are transported to shops and sold.

### (Optional) Generate Makefile configuration
```
make generate
```

### Start the network

To generate crypto and setup the network, make sure that the `GOPATH` variable is correctly set. After that, set `ABSPATH` to point to the parent folder of `blockchain-analyzer` (e.g., `export ABSPATH=$GOPATH/src/github.com`). When these environment variables have been set, start the network. Issue the following command in the `network/basic` directory to start the basic network, in the `network/multichannel` directory to start the multichannel network, or in the `network/applechain` directory to start the applechain network:

```
make start  
```

Enter the Fabric CLI Docker container by issuing the command:
```
docker exec -it cli bash  
```

Inside the CLI, the `/scripts` folder contains the scripts that can be used to install, instantiate and invoke chaincode (though the `make start` command takes care of installation and instantiation).

### Stop the network

To stop the network and delete all the generated data (crypto material, channel artifacts 
and dummyapp wallet), run:

```
make destroy
```

## Dummyapp Application  
The dummyapp application is used to create users and generate transactions
for different scenarios so that the resulting transactions can be analyzed with the
Elastic stack. Examples of scenarios include which channels to use, and which 
Fabric ca users to invoke transactions.

This application can connect to both the basic and the multichannel networks.  

The commands in this section should be issued from the `blockchain-analyzer/apps/dummyapp` directory.

### Installation
Before the first run, we have to install the necessary node modules:
```
npm install
```

### Configuration
The `config.json` contains the following configuration sections:

1. `channel`: Specify the name of the channel and chaincode name. For basic network, specify channel name as `mychannel`. For multichannel network, specify channel name as `fourchannel`.
2. `connection_profile`: specify the relative path of connection profile being used. The underlying connection profile depends on the instantiated Fabric network (basic or multichannel).
3. `organizations`: specifies different organizations in this network.
4. `users`: specifies the user created in Fabric CA of each peer.
5. `transactions`: is an array that contains a series of transactions generated for the network. The generated transactions result in a set of key/value pairs being written to the ledger. All transactions specified in the array are invoked in a serial manner.

Transactions have four fields:
1. `user`: This field is required. We have to specify which user to use when making the transaction.
2. `txFunction`: This field is required. We have to specify here the chaincode function that should be called.
3. `key`: This field is required. We have to specify here the key to be written to the ledger.
4. `previousKey`: This field is optional. We can specify here the key to which the new transaction (key-value pair) is linked.

###  User enrollment and registration
To enroll admins, register and enroll users, run the following command:
```
make users
```

###  Invoke transactions
To add key-value pairs, run:
```
make invoke
```

### Query
To query a specific key, run
```
make query KEY=Key1
```
To query all key-value pairs, run
```
make query-all
```

## Appleapp Application (Advanced)

The appleapp application is used to generate users and transactions for a supply chain use-case.  
The commands in this section should be issued from the `blockchain-analyzer/apps/appleapp` directory.

### Configuration
The `config.json` contains the configuration for the application. The following
fields can be set with an array entry of the `transaction` block in `config.json`:

1. `user`: This field is required. Specify which user to use when making the transaction.
2. `txFunction`: This field is required. Specify here the chaincode function that should be called.
3. `key`: This field is required. Specify here the key to be written to the ledger.
4. `name`: This field is optional. Specify here the name of the facility to create with the transaction (farm, factory or shop). Can be used with `addFarm`, `addFactory` and `addShop`.
5. `state`: This field is optional. Specify here the state of the facility to create with the transaction (farm, factory or shop). Can be used with `addFarm`, `addFactory` and `addShop`.
6. `farm`: This field is optional. Reference a farm by its key. Can be used with `createCrate`.
7. `from`: This field is optional. Reference a facility (farm, factory or shop) from which the transport departs. Can be used with `createTransport`.
8. `to`: This field is optional. Reference a facility (farm, factory or shop) to which the transport arrives. Can be used with `createTransport`.
9. `asset`: This field is optional. Reference an asset (crate, jam or juice) that is transported. Can be used with `createTransport`.
10. `factory`: This field is optional. Reference a factory in which the product is produced. Can be used with `createJam` and `createJuice`.
11. `crate`: This field is optional. Reference a crate of apples of which the product is made. Can be used with `createJam` and `createJuice`.
12. `shop`: This field is optional. Reference a shop in which the product is sold. Can be used with `createSale`.
13. `product`: This field is optional. Reference a product that is being sold. Can be used with `createSale`.

###  User enrollment and registration
To enroll admins, register and enroll users, run the following command:
```
make users
```

###  Invoke transactions
To add key/value pairs, run
```
make invoke
```

###  Query key
To make a query, run
```
make query KEY=key1
```

## Elastic Stack
This project includes an Elasticsearch and Kibana setup to index and visualize blockchain data.  
The commands in this section should be issued from the `blockchain-analyzer/stack` folder.

If you are working in a virtual machine, the Elasticsearch container may not start. In this case, issue the following command:
```
sudo sysctl -w vm.max_map_count=262144
```
to set the vm.max_map_count kernel setting to 262144, then destroy and bring up the Elastic Stack again.

### Credit
This setup is borrowed from
https://github.com/maxyermayank/docker-compose-elasticsearch-kibana

### Description
Kibana container and Elasticsearch cluster with nginx and 3 Elasticsearch containers. To view Kibana in browser, navigate to http://localhost:5601

### Start
To start the containers, navigate to `blockchain-analyzer/stack` directory and issue:
```
make start
```

### Stop and destroy
To stop the containers, issue
```
make destroy
```
To stop the containers and remove old data, run
```
make erase
```

## Beats Agent

The fabricbeat beats agent is responsible for connecting to a specified peer, periodically querying its ledger, processing the data and shipping it to Elasticsearch. Multiple instances can be run at the same time, each querying a different peer and sending its data to the Elasticsearch cluster.  
The commands in this section should be issued from the `blockchain-analyzer/agent/fabricbeat` directory.

### Environment setup

Before configuring and building the fabricbeat agent, please make sure that the `GOPATH` variable is set correctly. Then, add `$GOPATH/bin` to the `PATH`:
```
export PATH=$PATH:$GOPATH/bin
```
We use vendoring instead of go modules, so we have to make sure `GO111MODULE` is set to `auto` (it is the default):  
```
export GO111MODULE=auto
```  

Setup python 2.7.0 using [`pyenv`](https://github.com/pyenv/pyenv). 
Python version > 2.7 gives errors when running `make update`. 

### Configure fabricbeat

The agent is configured using the `fabricbeat.yml` file. This file is generated 
based on the `fabricbeat/_meta/beat.yml` file. If you want to update the generated 
config file, please edit `fabricbeat/_meta/beat.yml`, and then run:
```
make update
```

The configurable fields are the following:
* `period`: defines how often an event is sent to the output (Elasticsearch in this case)
* `organization`: defines which organization the connected peer is part of
* `peer`: defines the peer which fabricbeat should query (must be defined in the connection profile)
* `connectionProfile`: defines the location of the connection profile of the Fabric network
* `adminCertPath`: absolute path to the admin certfile
* `adminKeyPath`: absolute path to the admin keyfile
* `elasticURL`: URL of Elasticsearch (defaults to http://localhost:9200)
* `kibanaURL`: URL of Kibana (defaults to http://localhost:5601)
* `blockIndexName`: defines the name of the index to which the block data should be sent
* `transactionIndexName`: defines the name of the index to which the transaction data should be sent
* `keyIndexName`: defines the name of the index to which the key write data should be sent
* `dashboardDirectory`: folder which should contain the generated dashboards
* `templateDirectory`: folder which contains the templates for Kibana objects (index patterns, dashboards, etc.)
* `chaincodes`: describes the chaincodes installed on the peer
  * `name`: the name of the chaincode
  * `values`: the keys of the values that get persisted with the key (e.g. fabcar: key: CAR0 values: [make, model, colour, owner])
  * `linkingKey`: the name of the key that links transactions (e.g. dummycc: previousKey)
* `setup.ilm.enabled`: setting this false makes possible to define our own indices (for blocks, transactions and keys per organization)
* `output.elasticsearch.index`: the template for runtime index creation
* `output.elasticsearch.hosts`: the list of elasticsearch hosts we want our agent to connect to
* `setup.template.name`: the name of the index template that is going to be automatically created if does not exist
* `setup.template.pattern`: the index template is loaded for indices matching this pattern
* `setup.dashboards.directory`: the directory that contains the (generated) dashboards to be imported into Kibana on start of the agent

The paths and peer/org names contain variables that can be passed when starting the agent:
* `GOPATH` is the value of the gopath environment variable
* `ORG_NUMBER` is the number of the organization (1 for Org1, ..., 4 for Org4)
* `NETWORK` is the name of the network (basic or multichannel)
* `PEER_NUMBER` is the number of the peer (in basic network, it can be only 0, in multichannel, it can be 0 or 1)


Thanks to these variables, it is possible to run multiple instances at the same time with different configurations without rebuilding the agent.

To use the agent with another (custom) network, modify the configuration according to that network's specifications. 

### About indices

Three different Elasticsearch indices per Fabric organization are setup. One for blocks, one for transactions and one for single writes.  If wmultiple agents are run for peers in the same organization, they are going to send their data to the same indices. You can then select the peer on the dashboards to view its data only.  
If multiple instances are run for peers in different organizations, you will see the data of different organizations on different dashboards.  

The name of the indices can be customized in the fabricbeat configuration file (\_meta/beat.yml and `make update` or directly in fabricbeat.yml).

### Build fabricbeat

To build the agent, issue the following command:
```
make
```

While we have tried to bake in all go dependencies in the repo (good for building, not good for long term), if there are dependency errors, please install them. Specifically, errors for these two dependencies may arise:

```
$ go get golang.org/x/net/context
$ go get golang.org/x/text/secure/bidirule

```

### Start fabricbeat

To start the agent, issue the following command from the `fabricbeat` directory:
```
./fabricbeat -e -d "*"
```
If you want to use the agent with one of the example networks from the `blockchain-analyzer/network` folder, you can start the agent using:
```
ORG_NUMBER=1 PEER_NUMBER=0 NETWORK=basic ./fabricbeat -e -d "*"
```
The variables passed are used in the configuration (`fabricbeat.yml`). To connect to another network or peer, change the configuration (and/or the passed variables) accordingly.

### Stop fabricbeat

To stop the agent, simply type `Ctrl+C`


## License
The Apache 2.0 License applies to the whole project, except from the [stack](https://github.com/hyperledger-labs/blockchain-analyzer/tree/master/stack) directory and its contents.
