# RealTheory Collector

## Introduction
This document provides information related to the minimum requirements for running RealTheory with your cluster

### Metrics Server
The RealTheory collector requires the Metrics Server to be installed on the Kubernetes cluster. The Metrics Server is an aggregator of resource usage data in your Kubernetes cluster. For more information on the Kubernetes Metrics Server, including installation instructions, please visit https://github.com/kubernetes-sigs/metrics-server

The Metrics Server is automatically installed with some managed versions of Kubernetes. The following table indicates which managed Kubernetes offerings include the Metrics Server as part of the standard offering.

| Managed Kubernetes Offering | Includes Metric Server | More information |
|-----------------------------|------------------------|------------------|
| AKS | Yes | |
| GKE | Yes | |
| EKS | No | [How to install the Metrics Server on EKS](https://docs.aws.amazon.com/eks/latest/userguide/metrics-server.html) |