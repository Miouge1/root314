---
layout:     post
title:      Kubernetes All-In-One in 10 minutes
#subtitle:   How to connect to a
date:       2018-04-02 18:00:00
author:     Maxime
#header-img: "img/post-bg-01.jpg"
tags: [kubernetes, kubespray]
category: kubernetes
---

[Kubespray](https://github.com/kubernetes-incubator/kubespray) is a set of Ansible playbooks to deploy a production ready Kubernetes cluster. But we can also use it for quick and easy test/dev Kubernetes clusters.

## With Vagrant
The easiest way is to use Vagrant, simply download the `Vagrantfile` and run `vagrant up`.

```
git clone https://github.com/RootPi314/kubespray-aio.git
cd kubespray-aio
vagrant up
```

## Cloud Init

If you prefer to run it with a cloud provider instead of running things locally simply pass the [cloud-init.yml](https://github.com/RootPi314/kubespray-aio/blob/master/cloud-init.yml) file as `--user-data`.

## Do It Yourself & behind the scene
Finally, if you want to get your hands dirty you'll need an Ubuntu 16.04 server or VM, then run:
```
git clone git clone https://github.com/RootPi314/kubespray-aio.git
cd kubespray-aio
./install.sh
```

Depending on the performance of the server you should g

Documentation and info available on [GitHub.com/RootPi314/kubespray-aio](https://github.com/RootPi314/kubespray-aio)
