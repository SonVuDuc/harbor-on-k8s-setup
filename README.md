# Guide setup Kubernetes
### Contents
[1. Introduction]<br>
[2. Prerequisites]<br>
[3. Create Cluster.]<br>
[4. Setup the Kubernetes Master]<br>
[5. Join your nodes to your Kubernetes cluster]<br>

## 1. Introduction
Kubernetes is a system designed to manage applications built within containers across clustered environments. It handles the entire life cycle of a containerized application including deployment and scaling.

![alt text](https://techvccloud.mediacdn.vn/zoom/650_406/2018/10/15/kubernetes-15395717142261348450270-0-57-799-1479-crop-1539571719483162513005.png)

Kubernetes is an open-source platform that manages Docker containers in the form of a cluster. Along with the automated deployment and scaling of containers, it provides healing by automatically restarting failed containers and rescheduling them when their hosts die. This capability improves the application’s availability.

## 2. Prerequisites
+ Virtual Box
+ Vagrant
+ Visual Studio Code
+ Ubuntu 18.04 servers 
+ Master node’s minimal required memory is 2GB
+ Worker node’s minimal required memory is 1GB

## 3. Create Cluster

**Install Virtual Box**
```
$ sudo apt update
$ sudo apt install virtualbox
```
Launch Virtual Box

```
$ virtualbox
```

**Install Vagrant**

```
$ sudo apt-get install vagrant
```


I will start with creating 3 Ubuntu 18.04 servers. This will give you three servers to configure. To get this three member cluster up and running, you will need to use Vagrant to create Ubuntu 18.04 servers and enable Private Networking.

| Server  | Hostname   |  Role |IP Address     |
| ------- |:----------:|:-----:|--------------:|
| 1       | Master     | Master| 172.16.10.100 |
| 2       | Worker1    | Worker| 172.16.10.101 |
| 3       | Worker2    | Worker| 172.16.10.102 |

Create a folder named **kube-ubuntu** in your workspace

**Create Master Node**

In folder **kube-ubuntu**. create subfolder named **master**. In folder **master**, create file Vagrantfile with the following content
```
# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
  config.vm.box = "aspyatkin/ubuntu-18.04-server"
  config.vm.network "private_network", ip: "172.16.10.100"
  config.vm.hostname = "master"

  config.vm.provider "virtualbox" do |vb|
     vb.name = "master"
     vb.cpus = 2
     vb.memory = "2048"
  end
end

```
