# Guide setup Kubernetes
### Contents
[1. Introduction](#mohinh)<br>
[2. Prerequisites](#thietlap)<br>
[3. Set up each server in the cluster.](#controller)<br>
[4. Setup the Kubernetes Master](#compute)<br>
[5. Join your nodes to your Kubernetes cluster](#dashboard)<br>

## 1. Introduction
Kubernetes is a system designed to manage applications built within containers across clustered environments. It handles the entire life cycle of a containerized application including deployment and scaling.

![alt text](https://techvccloud.mediacdn.vn/zoom/650_406/2018/10/15/kubernetes-15395717142261348450270-0-57-799-1479-crop-1539571719483162513005.png)

Kubernetes is an open-source platform that manages Docker containers in the form of a cluster. Along with the automated deployment and scaling of containers, it provides healing by automatically restarting failed containers and rescheduling them when their hosts die. This capability improves the application’s availability.
