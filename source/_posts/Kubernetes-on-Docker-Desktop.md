---
title: Kubernetes on Docker Desktop
date: 2019-04-10 00:00:00
tags: 
- docker
- kubernetes
categories:
- [Kubernetes]
---

Kubernetes setup now comes bundled with Docker Desktop for windows and mac.

<!-- more -->

<!-- toc -->

With the latest docker desktop for windows/mac we get a Single Node Kubernetes Cluster, so no longer need minikube setup to test kubernetes locally.

[Docker for Developers](https://www.docker.com/get-started)

{% asset_img image01.JPG %}

Once kubernetes is running, check kubectl.

```bash
kubectl version
```

If this doesnt work then set the environment variable

```bash
KUBECONFIG=C:\Code\.kube\config
HOME=C:\Code
```

You can use the Kubernetes Dashboard to deploy container and manage the kubernetes cluster. Hence to install the dashboard

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml

secret "kubernetes-dashboard-certs" created
serviceaccount "kubernetes-dashboard" created
role.rbac.authorization.k8s.io "kubernetes-dashboard-minimal" created
rolebinding.rbac.authorization.k8s.io "kubernetes-dashboard-minimal" created
deployment.apps "kubernetes-dashboard" created
service "kubernetes-dashboard" created
```

To access Dashboard run the proxy

```bash
kubectl proxy

Starting to serve on 127.0.0.1:8001
```

Open the dashboar url in a browser

[http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/](http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/)

{% asset_img image02.JPG %}

You need to update your kubectl config file with the token and then sign in. Open power shell terminal and run the commands

```bash
PS C:\Users\gitorko> $TOKEN=((kubectl -n kube-system describe secret default | Select-String "token:") -split " +")[1]
PS C:\Users\gitorko> kubectl config set-credentials docker-for-desktop --token="${TOKEN}"
```

Now choose the kubeconfig file in the browser and click signin.

{% asset_img image03.JPG %}

You can also use kitematic which provides graphical dashboard to interact with containers.

{% asset_img image04.JPG %}