---
layout:     post
title:      OpenStack's provider networks
#subtitle:   How to connect to a
date:       2016-12-17 22:48:00 +0100
author:     Maxime
#header-img: "img/post-bg-01.jpg"
comments: true
---

Provider networks might not seem like much at first glance but they are mighty powerful - they allow you to connect **any network** (L2 or L3) to your cloud. They are useful if your OpenStack workloads need private access to a legacy system on a specific VLAN. Common usage examples are physical appliances, storage systems, enterprise databases etc.
In this post I will show you how to setup a provider network in an OpenStack cloud.

### Prerequisites

To manage provider networks you need *admin* permissions/role on an OpenStack cloud deployed with Neutron networking as well as VLAN-aware network gear.

### How to

The cloud administrator (you need the *admin* role for this bit) can create a provider network and specify the VLAN ID with the following command:

```bash
VLAN_ID=1234
neutron net-create vlan-$VLAN_ID --provider:physical_network physnet2 \
  --provider:network_type vlan \
  --provider:segmentation_id $VLAN_ID
```

Now we have a Neutron network for the desired VLAN ID (1234 in the example) that we can use as usual:

* Configure subnets
* Connect Virtual Machines
* Attach Neutron routers

![provider network]({{site.url}}/img/posts/openstack-provider-network.svg)

### Under the hood

In the `neutron net-create` command the value of the `--provider:physical_network` parameter depends on the network topology and the Neutron ML2 plugin configuration.

In this example we trunk all VLANs on *eth0* which is bridged on *br-prv*, then we configure the Neutron ML2 plugin to associate a label (*physnet2*) to the *br-prv* bridge interface.

```ini
# from /etc/neutron/plugins/ml2/ml2_conf.ini
[ovs]
bridge_mappings=physnet2:br-prv
```

For more information, the official documentation covers in details provider networks for [Linux Bridges](http://docs.openstack.org/mitaka/networking-guide/scenario-provider-lb.html) and for [OVS](http://docs.openstack.org/mitaka/networking-guide/scenario-provider-ovs.html).
