---
copyright:
  years: 2017, 2019
lastupdated: "2019-05-29"
keywords: vpc, features, benefits, isolation, provisioning, security, cloud-native, workloads, BYOIP

subcollection: vpc-on-classic

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:download: .download}
{:table: .aria-labeledby="caption"}
{:DomainName: data-hd-keyref="DomainName"}

# About Virtual Private Cloud
{: #about}

{{site.data.keyword.cloud}} Virtual Private Cloud (VPC) allows you to create your own space in IBM Cloud, to run an isolated environment within the public cloud. VPC gives you the security of a private cloud, with the agility and ease of a public cloud.

You can manage network services and launch instances as needed to support your mission-critical, cloud-tolerant, and cloud-native applications. Key features available:

* Create and manage isolated application environments through an API
* Define your own networking policies designed for security and convenient access
* Design network topologies with BYOIP (Bring Your Own IP)
* Provision your resources and connect them to each other, or isolate them from one another
* Cover multiple regions for disaster recovery and resilience
* Use availability zones that allow high-speed & low-latency connections across regions, with HA
* Utilize high-speed networking and storage devices
* Allow Always On services (control plane)
* Provide and utilize core services:  IPAM, VPN, firewalls, SSH, DNS, and L4 Load Balancing
* Set up other services: auto-scaling, CDNs, 3rd-party NFV

The VPC Overview figure, which follows, illustrates the available VPC components and how they work together to provide you the service and connectivity you may need. Refer back to this figure as you dive into topics that give more details on each component.

![IBM Cloud VPC Overview](images/vpc-experience-simple.svg "IBM Cloud VPC Overview"){: caption="Figure: IBM Cloud VPC Overview" caption-side="top"}

The following sections give you an overview of the features available in IBM Cloud VPC.

## Networking capabilities

{{site.data.keyword.cloud_notm}} VPC offers easy and comprehensive networking capabilities, including the ability to create multiple Virtual Private Clouds in [multi-zone regions available globally](/docs/vpc-on-classic?topic=vpc-on-classic-creating-a-vpc-in-a-different-region), subnets in different zones, [IP address range selection](/docs/vpc-on-classic-network?topic=vpc-on-classic-network-working-with-ip-address-ranges-address-prefixes-regions-and-subnets), virtual firewalls, ([security groups](/docs/vpc-on-classic-network?topic=vpc-on-classic-network-using-security-groups) and [network ACLs](/docs/vpc-on-classic-network?topic=vpc-on-classic-network-setting-up-network-acls)), site-to-site virtual private networks ([VPN](/docs/vpc-on-classic-network?topic=vpc-on-classic-network---using-vpn-with-your-vpc)), and load balancing ([LBaaS](/docs/vpc-on-classic-network?topic=vpc-on-classic-network---using-load-balancers-in-ibm-cloud-vpc)) with elasticity.

A VPC is divided into subnets, using a range of private IP addresses. However, by default, all resources (such as VSIs) within the same VPC can communicate with each other, regardless of their subnet. Subnets are contained within a single zone, and they cannot span multiple zones, which helps with security, with reducing latency, and with high availability.

Create multiple VPCs and subnets easily by using the suggested prefix ranges and pre-configured network security policies or design and define your own address prefixes and custom security policies.

CIDR blocks 161.26/16 and 166.8/14 are both reserved and routed into every subnet. Read about our [Service endpoints available for IBM Cloud VPC](/docs/vpc-on-classic?topic=vpc-on-classic-service-endpoints-available-for-ibm-cloud-vpc). Further, CIDR block 10.240/13 is reserved for VPC address prefixes and divided up among zones [in a predefined way](/docs/vpc-on-classic-network?topic=vpc-on-classic-network-working-with-ip-address-ranges-address-prefixes-regions-and-subnets#ibm-cloud-vpc-and-address-prefixes) and cannot be changed.
{: important}

### Internet access

Three options are available for enabling communications from your virtual server instances (VSIs) to the public internet:
* Use a public gateway (PGW) to enable communication to the internet for all virtual server instances on the attached subnet. There is no charge for using a PGW, except for the bandwidth used.
* Use a Floating IP (FIP) to enable communication to and from a single virtual server instance (VSI) to the internet.
* Use a VPN gateway. For more information, see our [VPN for VPC documentation](/docs/vpc-on-classic-network?topic=vpc-on-classic-network---using-vpn-with-your-vpc#--using-vpn-with-your-vpc)
{: note}

### Classic access

Existing {{site.data.keyword.cloud_notm}} infrastructure users can connect their classic infrastructure, including Direct Link connectivity, to one VPC per region using our [Classic Access](/docs/vpc-on-classic?topic=vpc-on-classic-setting-up-access-to-your-classic-infrastructure-from-vpc).

### BYOIP

You can bring your own public IPv4 address range (BYOIP) to your IBM Cloud VPC account.

When you use BYOIP, {{site.data.keyword.cloud_notm}} must configure those IPv4 addresses on {{site.data.keyword.cloud_notm}} resources, which will send packets to and from the addresses you've provided. Therefore, as a result of using your supplied IPv4 range on {{site.data.keyword.cloud_notm}}, these IP addresses may be exposed to IBM's support staff and third parties.
{: important}

You must use the API or the CLI to use BYOIP. This capability is not available through the IBM Cloud console UI.
{: note}

### Global connectivity

You can scope your applications and available resources to span across multiple regions. Using [VPN](/docs/vpc-on-classic-network?topic=vpc-on-classic-network---using-vpn-with-your-vpc), you can create private connections to other projects and other portions of your hybrid cloud deployments.

Easily scalable and sharable, a VPC network and resources can stretch from your on-premises facility into your cloud.

## Security

Security is integrated into your {{site.data.keyword.cloud_notm}} VPC, with [security groups](/docs/vpc-on-classic-network?topic=vpc-on-classic-network-using-security-groups) that act as virtual firewalls for instance-level protection, and with [network access control lists](/docs/vpc-on-classic-network?topic=vpc-on-classic-network-setting-up-network-acls) (ACLs) for subnet-level protection.

## High availability

The {{site.data.keyword.cloud_notm}} Virtual Private Cloud (VPC) gives your applications logical isolation from other networks in all regions, while providing scalability and security. Use [Load Balancers](/docs/vpc-on-classic-network?topic=vpc-on-classic-network---using-load-balancers-in-ibm-cloud-vpc) to distribute your network traffic across a set of targets to improve performance and HA. Load Balancers also monitor the health of your applications and services. You can set up a load balancer to distribute incoming application traffic across instances in a single zone or across multiple zones within a region.

## Compute capabilities

Use [IBM Cloud Virtual Servers for Virtual Private Cloud](/docs/vpc-on-classic-vsi?topic=vpc-on-classic-vsi-virtual-private-cloud) to provision scalable compute resources in the IBM Cloud. Create virtual server instances (VSIs) quickly, using pre-defined [profiles](/docs/vpc-on-classic-vsi?topic=vpc-on-classic-vsi-profiles) optimized for your specific workloads. When provisioning the multi-homed, [multi-vNIC](/docs/vpc-on-classic-vsi?topic=vpc-on-classic-vsi-network-security-options) instances, choose from the supported stock [images](/docs/vpc-on-classic-vsi?topic=vpc-on-classic-vsi-images) or upload a custom image.

{{site.data.keyword.cloud_notm}} VPC adds a network orchestration layer that eliminates the pod boundary of virtual server instances, creating infinite capacity for scaling instances. The network orchestration layer handles all of the networking for all virtual server instances that are within an IBM Cloud VPC across regions and zones. With the software defined networking capabilities that {{site.data.keyword.cloud_notm}} VPC provides, you have more options for VPNs, LBaaS, multi-vNIC instances, and larger subnet sizes.

## Storage capabilities

When you provision an {{site.data.keyword.cloud_notm}} Virtual Servers for Virtual Private Cloud instance, a 100 GB, general purpose IOPS (3 IOPS/GB) block storage volume is created automatically, as a primary boot volume, and attached to the instance. You can create block storage volumes when you provision a virtual server instance in a VPC network, or create new volumes independent of the VSI lifecycle.

When provisioning additional block storage for your VPC, you can select an [IOPS tier](/docs/vpc-on-classic-block-storage?topic=vpc-on-classic-block-storage-block-storage-about#tiers) for your block storage volume, or specify  a [custom IOPS profile](/docs/vpc-on-classic-block-storage?topic=vpc-on-classic-block-storage-block-storage-about#custom).

## Designed for cloud-native and hybrid workloads

A VPC is ideal for cloud-native workloads and for linking your existing infrastructure into {{site.data.keyword.cloud_notm}}. You can create the best cloud for workloads such as cognitive, AI, and Machine Learning computations.

Here are more ways that VPC supports your hybrid, cloud-tolerant, and cloud-native workloads:

* Load balancing with horizontal auto-scaling
* Monitoring and health checks
* Support for GPUs
* VSI provisioning with affinity and anti-affinity groups

## Learn more

* [**VPC terminology**](/docs/vpc-on-classic?topic=vpc-on-classic-vpc-glossary)
* [**VPC security**](/docs/vpc-on-classic-network?topic=vpc-on-classic-network-security-in-your-ibm-cloud-vpc#security-in-your-ibm-cloud-vpc)
* [**VPC regions and subnets available**](/docs/vpc-on-classic-network?topic=vpc-on-classic-network-working-with-ip-address-ranges-address-prefixes-regions-and-subnets)
* [**Creating and managing network resources in VPC**](/docs/vpc-on-classic?topic=vpc-on-classic-creating-and-managing-network-resources-in-vpc)
* [**Creating and managing virtual server instances in VPC**](/docs/vpc-on-classic?topic=vpc-on-classic-creating-and-managing-virtual-server-instances)
* [**Creating and managing block storage in VPC**](/docs/vpc-on-classic?topic=vpc-on-classic-creating-and-managing-storage-in-vpc)
