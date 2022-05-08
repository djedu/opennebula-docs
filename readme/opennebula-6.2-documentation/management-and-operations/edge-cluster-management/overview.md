# Overview

OpenNebula

Edge Clusters provide you with the tools needed to dynamically grow your cloud infrastructure with physical or virtual resources running on remote cloud providers. Edge Clusters support two main use cases:

* **Edge Cloud Computing**, to transition from centralized clouds to distributed edge-like cloud environments. You will be able to grow your on-premises cloud with resources at edge data center locations to meet the latency, bandwidth or data regulation needs of your workload.
* **Hybrid Cloud Computing**, to address peaks of demand and need for extra computing power by dynamically growing your underlying physical infrastructure.

### How Should I Read This Chapter[¶](broken-reference)

In this chapter you can find a guide on how to automatically allocate and provision Edge Clusters on bare-metal and virtual instances on cloud providers:

> * [Equinix Edge Clusters](broken-reference)
> * [Amazon AWS Edge Clusters](broken-reference)
> * [DigitalOcean Edge Cluster](broken-reference)
> * [Google Edge Cluster](broken-reference)
> * [Vultr Edge Cluster](broken-reference)
> * [On-Premise Edge Cluster](broken-reference)

In this chapter you’ll also learn how to [operate your clusters](broken-reference) and [manage providers](broken-reference).

### Hypervisor Compatibility[¶](broken-reference)

Edge Clusters can be _virtual_ or _metal_ depending of the instance type used to build the cluster. Note that not all providers offer both instance types.

| Edge/Cloud Provider              | Edge Cluster        | Hypervisor               |
| -------------------------------- | ------------------- | ------------------------ |
| [Equinix](broken-reference)      | metal               | KVM, Firecracker and LXC |
| [AWS](broken-reference)          | metal               | KVM, Firecracker and LXC |
| [AWS](broken-reference)          | virtual             | LXC                      |
| [Google](broken-reference)       | virtual             | LXC                      |
| [DigitalOcean](broken-reference) | virtual             | LXC                      |
| Vultr                            | metal (in progress) | KVM, Firecracker and LXC |
| [Vultr](broken-reference)        | virtual             | LXC                      |
| [On-prem](broken-reference)      | metal               | KVM, Firecracker and LXC |

The Edge Cluster type determines the hypervisor and workload that can be run in the cluster. The following table summarizes the Edge Cluster you need to run specific workloads:

| Use Case                              | Edge Cluster     | Hypervisor      |
| ------------------------------------- | ---------------- | --------------- |
| I want to run application containers… | virtual          | LXC             |
| metal                                 | LXC, Firecracker |                 |
| I want to run virtual servers…        | metal            | KVM, LXC        |
| I want to run a Kubernetes cluster…   | metal            | KVM (k8s based) |
| Firecracker (k3s based)               |                  |                 |

In the above table, _application containers_ are those imported from DockerHub, LinuxContainers or TunrkeyLinux, as well as images created from DockerFiles. On the other hand _Virtual servers_ use full system disk images.
