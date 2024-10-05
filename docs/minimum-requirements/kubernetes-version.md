# Minimum Requirements

## Introduction
This document provides information related to the minimum requirements for running RealTheory with your cluster.

## Kubernetes Version
The RealTheory collector is currently supported on the following versions of Kubernetes:

- 1.31
- 1.30
- 1.29
- 1.28
- 1.27
- 1.26
- 1.25

## How do I check my cluster's Kubernetes version?
The simplest way of checking your cluster’s Kubernetes version is to use the kubectl version command. This command will output information for the kubectl cluster and the Kubernetes client.

```sh
kubectl version
```

Output
```
Client Version: v1.30.2
Server Version: v1.30.4
```

The Server Version is the cluster's Kubernetes version.