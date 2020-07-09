# Guide setup Kubernetes
### Contents
[1. Introduction]<br>
[2. Prerequisites]<br>
[3. Create Servers.]<br>
[4. Setup the Kubernetes Cluster]<br>
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

## 3. Create Servers

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

In folder **kube-ubuntu**. create subfolder named **master**. In folder **master**, open in Terminal, run command
```
$ vagrant init
```
Vagrant will generates Vagrantfile. Open file in VS Code and edit like this:

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
To create Virtual Machine, run command:
```
$ vagrant up
```

After running the above command, you will have a fully running virtual machine in VirtualBox running Ubuntu 18.04 LTS 64-bit. Also you can SSH into this machine with ``` $ vagrant ssh```

**Create Worker Node**

Simillar to creating Master Node, in folder **kube-ubuntu**, you create 2 subfolders named **worker1** and **worker2**
In each folder, open it in Termial and run command:
```
$ vagrant init
```
Edit the Vagrantfile with VS Code

Node Woker1:


```
# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
  config.vm.box = "aspyatkin/ubuntu-18.04-server"
  config.vm.network "private_network", ip: "172.16.10.101"
  config.vm.hostname = "worker1"

  config.vm.provider "virtualbox" do |vb|
     vb.name = "worker1"
     vb.cpus = 1
     vb.memory = "1024"
 
  end
end
```

Node Worker2:

```
# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
  config.vm.box = "aspyatkin/ubuntu-18.04-server"
  config.vm.network "private_network", ip: "172.16.10.102"
  config.vm.hostname = "worker2"

  config.vm.provider "virtualbox" do |vb|
     vb.name = "worker2"
     vb.cpus = 1
     vb.memory = "1024"
 
  end
end
```

After all, run command 
```
$ vagrant up
```
Now you have 3 virtual machine Ubuntu server 18.04 LTS 

## 4. Setup the Kubernetes Cluster

SSH to your server with command ```$ vagrant ssh```, password: vagrant
You need to have **root** permission in order to set up.
In **vagrant** user, 
```
$ sudo passwd root
```
I will set **root** password is 123.

Run command ```$ su -```  to login with **root**



