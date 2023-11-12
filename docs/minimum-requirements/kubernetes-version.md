# Minimum Requirements

## Introduction
This document provides information related to the minimum requirements for running RealTheory with your cluster.

## Kubernetes Version
The RealTheory collector is currently supported on the following versions of Kubernetes:

- 1.27
- 1.26
- 1.25
- 1.24
- 1.23
- 1.22
- 1.21
- 1.20
- 1.19
- 1.18

## How do I check my cluster's Kubernetes version?
The simplest way of checking your cluster’s Kubernetes version is to use the kubectl version command. This command will output information for the kubectl cluster and the Kubernetes client.

```sh
kubectl version
```

Output
```
Client Version: version.Info{Major:"1", Minor:"22", GitVersion:"v1.22.1", GitCommit:"632ed300f2c34f6d6d15ca4cef3d3c7073412212", GitTreeState:"clean", BuildDate:"2021-08-19T15:45:37Z", GoVersion:"go1.16.7", Compiler:"gc", Platform:"windows/amd64"}
Server Version: version.Info{Major:"1", Minor:"21", GitVersion:"v1.21.7", GitCommit:"3c28d5b50e68d9c6da51840bbdebf6bd0673dde5", GitTreeState:"clean", BuildDate:"2022-06-04T17:59:02Z", GoVersion:"go1.16.10", Compiler:"gc", Platform:"linux/amd64"}
```

The Server Version is the cluster's Kubernetes version.