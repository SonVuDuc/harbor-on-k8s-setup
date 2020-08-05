# Hướng dẫn cài đặt Harbor trên Rancher
### Nội dung
1. Yêu cầu 
2. Cài đặt Rancher
3. Tạo cluster k8s
4. Cài đặt Harbor

## 1. Yêu cầu 

Danh sách server:
  + 1 Node Rancher
  + 1 Node Master
  + 1 Node Worker

Cần 3 VPS hoặc VM
  + HĐH: Ubuntu server 18.04
  + Mỗi node đều được cài đặt Docker
  

| Server  | Hostname   |  Role  |IP Address      |
| ------- |:----------:|:-----: |---------------:|
| 1       | Rancher    | Rancher| 192.168.41.1   |
| 2       | Master     | Master | 192.168.41.128 |
| 3       | Worker1    | Worker | 192.168.41.130 |



## 2. Cài đặt Rancher

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

SSH to your server with command ```$ vagrant ssh```, password: **vagrant**

You need to have **root** permission in order to set up.

In **vagrant** user, 
```
$ sudo passwd root
```
I will set **root** password is 123.

Run command ```$ su -```  to login with **root**

On each of the 3 Ubuntu servers run the following commands as root:
```
$ apt-get update
$ apt-get install curl
$ apt-get install gnupg
$ cat >>/etc/hosts<<EOF
172.16.10.100 master
172.16.10.101 worker1
172.16.10.102 worker2
EOF
$ apt-get update && apt-get install -y apt-transport-https
$ curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
$ cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF
$ apt-get update
$ apt-get install -y kubelet kubeadm kubectl docker.io
```
On the master node run the following command:
```
kubeadm init --apiserver-advertise-address=172.16.10.100 --pod-network-cidr=192.168.0.0/16
```
After that, run these command to start using your cluster:

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

I will use Addon plugin name calico, run this command to install:
```
kubectl apply -f https://docs.projectcalico.org/v3.10/manifests/calico.yaml
```

## 5. Join your nodes to your Kubernetes cluster
You can now join any number of machines by running the kubeadm join command on each node as root. This command will be created for you as displayed in your terminal for you to copy and run. 

Use this command:
```
kubeadm token create --print-join-command
```

![Screenshot from 2020-07-09 16-42-26](https://user-images.githubusercontent.com/32956424/87024431-4ebe3480-c203-11ea-8a50-1483712808ec.png)


An example of what this looks like is below:
```
This node has joined the cluster:
* Certificate signing request was sent to master and a response was received.
* The Kubelet was informed of the new secure connection details.
```


SSH to worker nodes as root and run join command to join worker nodes to cluster


To check that all nodes are now joined to the master run the following command on the Kubernetes master node:
```
$ kubectl get nodes
```

![Screenshot from 2020-07-09 16-38-41](https://user-images.githubusercontent.com/32956424/87024123-e8d1ad00-c202-11ea-8d64-83e9025d0a56.png)

## 6. Using Rancher to manage Kubernetes cluster

Rancher is a complete software stack for teams adopting containers. It addresses the operational and security challenges of managing multiple Kubernetes clusters across any infrastructure, while providing DevOps teams with integrated tools for running containerized workloads.

In this part, I will install Rancher, use it to manage Kubernetes cluster and launch app.
I will use Brigde Apdater instead of Host only Adapter 



| Server  | Hostname   | Role  |IP Address     |
| ------- |:----------:|:-----:|--------------:|
| 1       | Rancher    |Server | 192.168.1.15  |
| 2       | Master     |Master | 192.168.1.201 |
| 3       | Worker1    |Worker | 192.168.1.202 |


SSH to node Rancher and run this command to install Rancher:

```
$ docker run -d --restart=unless-stopped \
    -p 80:80 -p 443:443 \
    -v /opt/rancher:/var/lib/rancher \
    rancher/rancher:latest
```

When it done, you can access to Rancher web UI from web browser. System will ask you set password for it. 

![Screenshot from 2020-07-30 11-39-55](https://user-images.githubusercontent.com/32956424/88881360-6d8b7600-d259-11ea-8e8a-9fcca717518a.png)

Login with username: **admin** and password you have already set
In this page, you can create a new cluster or import existed cluster. I will create a new one and set name for it


![Screenshot from 2020-07-30 11-24-57](https://user-images.githubusercontent.com/32956424/88880808-ebe71880-d257-11ea-9835-581605469e17.png)


![Screenshot from 2020-08-01 15-41-47](https://user-images.githubusercontent.com/32956424/89097914-876dba00-d40d-11ea-87bd-4de6a6540c0d.png)


In this case, you can custom roles for every nodes you add to cluster. Tick in role, copy command and run in Node you want


![Screenshot from 2020-08-01 15-42-47](https://user-images.githubusercontent.com/32956424/89097946-a9ffd300-d40d-11ea-96d8-cc65f5782441.png)


Waiting for it


![Screenshot from 2020-07-30 10-08-38](https://user-images.githubusercontent.com/32956424/89098072-8e48fc80-d40e-11ea-9c0e-daa7fbc6d414.png)

Now, you can run app on Rancher. Click on **Global -> 'Your Cluster' -> Default**

![Screenshot from 2020-07-30 10-08-48](https://user-images.githubusercontent.com/32956424/89098101-c0f2f500-d40e-11ea-9459-59c633100c54.png)

Click on **App -> Launch**. Choose app Harbor

![Screenshot from 2020-07-30 10-09-11](https://user-images.githubusercontent.com/32956424/89098131-f992ce80-d40e-11ea-90b1-943f03e6ac05.png)

Enter your admin password, click Launch and wait. I will use xip.io to generate link

![Screenshot from 2020-07-30 11-12-44](https://user-images.githubusercontent.com/32956424/89098142-08798100-d40f-11ea-8f18-cd6444901e28.png)

![Screenshot from 2020-07-30 11-23-58](https://user-images.githubusercontent.com/32956424/89098163-38c11f80-d40f-11ea-92ca-d65a780e7eb2.png)

![Screenshot from 2020-07-30 11-24-03](https://user-images.githubusercontent.com/32956424/89098176-52626700-d40f-11ea-918c-8a79851554ba.png)
 
When it's done, you can access to Harbor through xip.io link 

![Screenshot from 2020-07-30 10-34-18](https://user-images.githubusercontent.com/32956424/89098179-58584800-d40f-11ea-88ce-71c3707407e4.png)




















