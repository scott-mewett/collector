# Minimum Requirements

## Introduction
This document provides information related to the minimum requirements for running RealTheory with your cluster.

## Kubernetes Version
The RealTheory collector is currently supported on the following versions of Kubernetes:

- 1.20
- 1.19
- 1.18 - Best experienced with version 1.18.9 or later
- 1.17 - Best experienced with version 1.17.12 or later
- 1.16 - Best experienced with version 1.16.15 or later

## How to check whether your cluster's Kubernetes version?
The simplest way of checking a cluster’s Kubernetes version is to use the kubectl version command. This command will output information for the kubectl cluster and the Kubernetes client.

```sh
kubectl version
```

Output
```
Client Version: version.Info{Major:"1", Minor:"19", GitVersion:"v1.19.1", GitCommit:"092fbfbf53427de67cac1e9fa54aaa09a28371d7", GitTreeState:"clean", BuildDate:"2021-02-16T12:59:11Z", GoVersion:"go1.15.0", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"19", GitVersion:"v1.19.11", GitCommit:"c18ff7bd5227b42c617cf371cf315d0423e91d5e", GitTreeState:"clean", BuildDate:"2021-05-25T17:38:04Z", GoVersion:"go1.15.12", Compiler:"gc", Platform:"linux/amd64"}
```

The Server Version is the cluster's Kubernetes version.