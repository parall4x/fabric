# Setting up an ordering node

In this topic, we'll describe the process for bootstrapping an ordering node.
If you want more information about the different ordering service implementations
and their relative strengths and weaknesses, check out our
[conceptual documentation about ordering](./orderer/ordering_service.html).

Broadly, this topic will involve a few interrelated steps:

1. Creating the organization your ordering node belongs to (if you have not already
   done so)
2. Configuring your node (using `orderer.yaml`)
3. Creating the genesis block for the orderer system channel
4. Bootstrapping the orderer

Note: this topic assumes you have already pulled the Hyperledger Fabric orderer
images from docker hub.

## Create an organization definition

Like peers, all orderers must belong to an organization that must be created
before the orderer itself is created. This organization has a definition
encapsulated by a [Membership Service Provider](./membership/membership.html)
(MSP) that is created by a Certificate Authority (CA) dedicated to creating the
certificates and MSP for the organization.

For information about creating a CA and using it to create users and an MSP,
check out the [Fabric CA user's guide](https://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html).

## Configure your node

The configuration of the orderer is handled through a `yaml` file called
`orderer.yaml`. The `FABRIC_CFG_PATH` environment variable is used to point to
an `orderer.yaml` file you've configured, which will extract a series of files
and certificates on your file system.

To look at a sample `orderer.yaml`, check out the [`fabric-samples` github repo](https://github.com/hyperledger/fabric/blob/release-1.4/sampleconfig/orderer.yaml), which **should be read and studied closely** before proceeding.
Note in particular a few values:

* `LocalMSPID` --- this is the name of the MSP, generated by your CA, of your
orderer organization. This is where your orderer organization admins will be
listed.

* `LocalMSPDir` --- the place in your file system where the local MSP is located.

*  `# TLS enabled`, `Enabled: false`. This is where you specify whether you want
to [enable TLS](enable_tls.html). If you set this value to `true`, you will have
to specify the locations of the relevant TLS certificates. Note that this is
mandatory for Raft nodes.

* `GenesisFile` --- this is the name of the genesis block you will generate for
this ordering service.

* `GenesisMethod` --- the method by which the genesis block is created. For now,
this can only be `file`, in which the file in the `GenesisFile` is specified.

If you are deploying this node as part of a cluster (for example, as part of a
cluster of Raft nodes), make note of the `Cluster` and `Consensus` sections.

If you plan to deploy a Kafka based ordering service, you will need to complete
the `Kafka` section.

## Generate the genesis block of the orderer

The first block of a newly created channel is known as a "genesis block". If
this genesis block is being created as part of the creation of a **new network**
(in other words, if the orderer being created will not be joined to an existing
cluster of orderers), then this genesis block will be the first block of the "orderer
system channel" (also known as the "ordering system channel"), a special channel
managed by the orderer admins which includes a list of the organizations permitted
to create channels. *The genesis block of the orderer system channel is special:
it must be created and included in the configuration of the node before the node
can be started.*

To learn how to create a genesis block using the `configtxgen` tool, check out
[Channel Configuration (configtx)](configtx.html).

## Bootstrap the ordering node

Once you have built the images, created the MSP, configured your `orderer.yaml`,
and created the genesis block, you're ready to start your orderer using a
command that will look similar to:

```
docker-compose -f docker-compose-cli.yaml up -d --no-deps orderer.example.com
```

With the address of your orderer replacing `orderer.example.com`.

<!--- Licensed under Creative Commons Attribution 4.0 International License
https://creativecommons.org/licenses/by/4.0/) -->
