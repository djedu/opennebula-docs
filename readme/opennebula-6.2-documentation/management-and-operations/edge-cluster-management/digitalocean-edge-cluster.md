# DigitalOcean Edge Cluster

DigitalOcean supports **Virtual** Edge Clusters, that use a virtual machine instance to create OpenNebula Hosts. This provision is better suited for PaaS-like workloads. Virtual DigitalOcean Edge Clusters run primarily **LXC** to execute system containers.

### DigitalOcean Providers[¶](broken-reference)

A DigitalOcean provider contains the credentials to interact with DigitalOcean service and also the region to deploy your Edge Clusters. OpenNebula comes with four pre-defined providers in the following regions:

* Amsterdam
* London
* New York (US)
* San Franciso (US)
* Singapur

In order to define a DigitalOcean provider, you need the following information:

* **Credentials**: to authenticate with DigitalOcean service. You need to provide `token`, check [this guide for more details](https://www.digitalocean.com/community/tutorials/how-to-use-oauth-authentication-with-digitalocean-as-a-user-or-developer).
* **Region**: this is the location in the world where the resources are going to be deployed. All the available regions are [listed here](https://docs.digitalocean.com/products/platform/availability-matrix/).

#### How to Add a New DigitalOcean Provider[¶](broken-reference)

To add a new provider you need a YAML template file with the following information:

```
cat provider.yaml
name: 'digitalocean-nyc3'

description: 'cluster on DigitalOcean in Amsterdam 3'
provider: 'digitalocean'

plain:
  image: 'DIGITALOCEAN'
  location_key: 'region'
  provision_type: 'virtual'

connection:
  token: 'DigitalOcean token'
  region: 'ams3'

inputs:
- name: 'digitalocean_droplet'
    type: 'list'
    options:
    - 'centos-8-x64'
```

Then you just need to use the command `oneprovider create`:

```
oneprovider create provider.yaml
ID: 0
```

The providers’ templates are located in `/usr/share/one/oneprovision/edge-clusters/virtual/providers/digitalocean`. You just need to enter valid connection `token`.

#### How to Customize an Existing Provider[¶](broken-reference)

The provider information is stored in the OpenNebula database and it can be updated just like any other resource. In this case, you need to use the command `oneprovider update`. It will open an editor so you can edit all the information there.

### DigitalOcean Edge Cluster Implementation[¶](broken-reference)

An Edge Cluster in DigitalOcean creates the following resources:

* **DigitalOcean droplets**: will be the OpenNebula hosts to run virtual machines.
* **DigitalOcean VPC**: it creates an isolated virtual network for all the deployed resources.
* **DigitalOcean firewall**: by default all the traffic is allowed, you can later setup custom Security Groups through the OpenNebula interface.

The network model is implemented in the following way:

* **Public Networking**: this is implemented using port forwarding between the host and the VM. Each time a network is attached to the virtual machine, ports will be forwarded from the public IP of the host (droplet) where it is running.
* **Private Networking**: this is implemented using (BGP-EVPN) and VXLAN.

Warning

If a VPC is created in an empty region (without any VPC) it becomes the DEFAULT one, and you will not be able to delete it. We recomend to populate the regions with a default VPC, for this default VPC let DigitalOcean pick the address range (recommended option in the DO console).

### Tutorial: Provision a Google Edge Cluster[¶](broken-reference)

### Tutorial: Provision a Digital Ocean Edge Cluster[¶](broken-reference)

In this tutorial, we are going to show you how you can access an Alpine VM running inside DigitalOcean Edge Cluster.

#### Step 1: Deploy Edge Cluster[¶](broken-reference)

First you need to create a provision (see this guide for more details) and wait for it to be ready:

```
oneprovision list
ID NAME                  CLUSTERS HOSTS NETWORKS DATASTORES         STAT
 1 digitalocean-cluster         1     1        1          2      RUNNING
```

#### Step 2: Download Alpine From Marketplace[¶](broken-reference)

```
onemarketapp export 'Alpine Linux 3.13' 'Alpine' -d 'digitalocean-cluster-image'
IMAGE
    ID: 0
VMTEMPLATE
    ID: 0
```

#### Step 3: Instantiate the Template[¶](broken-reference)

```
onetemplate instantiate 'Alpine' --name 'alpine_test' --nic 'digitalocean-cluster-public'
VM ID: 0
```

#### Step 4: Connect to the VM[¶](broken-reference)

```
onevm ssh 'alpine_test'
localhost:~# cat /etc/os-release
NAME="Alpine Linux"
ID=alpine
VERSION_ID=3.13.3
PRETTY_NAME="Alpine Linux v3.13"
HOME_URL="https://alpinelinux.org/"
BUG_REPORT_URL="https://bugs.alpinelinux.org/"
localhost:~#
```

If you check the VM template, you will see the port ranges assigned by OpenNebula:

```
<EXTERNAL_PORT_RANGE><![CDATA[9001:9100]]></EXTERNAL_PORT_RANGE>
<INTERNAL_PORT_RANGE><![CDATA[1-100/9001]]></INTERNAL_PORT_RANGE>
```

As you are using the same public networking in the cluster, these ports will never collision.

You can use the command `onevm port-forward` to check what port you need to connect to access services:

```
onevm port-forward 0 80
35.246.64.97@9080 -> 80
```

### Operating Providers & Edge Clusters[¶](broken-reference)

Refer to the [cluster operation guide](broken-reference) to check all the operations needed to create, manage, and delete an Edge Cluster. Refer to the [providers guide](broken-reference) to check all of the operations related to providers.
