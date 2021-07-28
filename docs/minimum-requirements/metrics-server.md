# Minimum Requirements

## Introduction
This document provides information related to the minimum requirements for running RealTheory with your cluster.

## Metrics Server
The RealTheory collector requires the Metrics Server to be installed on the Kubernetes cluster. The Metrics Server is an aggregator of resource usage data in your Kubernetes cluster. For more information on the Kubernetes Metrics Server, including installation instructions, please visit https://github.com/kubernetes-sigs/metrics-server

The Metrics Server is automatically installed with some managed versions of Kubernetes. The following table indicates which managed Kubernetes offerings include the Metrics Server as part of the standard offering.

| Managed Kubernetes Offering | Includes Metric Server | More information |
|-----------------------------|------------------------|------------------|
| AKS | Yes | |
| GKE | Yes | |
| EKS | No | [How to install the Metrics Server on EKS](https://docs.aws.amazon.com/eks/latest/userguide/metrics-server.html) |

## How to check if the Metrics Server is installed in your cluster?
There are multiple ways to check if your cluster is running the Metrics Server:

1. Run the following command and inspect the output
   ```
   kubectl get deployment metrics-server -n kube-system 
   ```

   If the Metrics Server is installed, the output is similar to the following example:

   ```
   NAME             READY   UP-TO-DATE   AVAILABLE   AGE
   metrics-server   1/1     1            1           25h
   ```

1. Run the following command and inspect the output

   ```
   kubectl get --raw "/apis/metrics.k8s.io/v1beta1/nodes"
   ```

   If the Metrics Server is installed, the output is similar to the following example

   ```
   {"kind":"NodeMetricsList","apiVersion":"metrics.k8s.io/v1beta1","metadata":{"selfLink":"/apis/metrics.k8s.io/v1beta1/nodes"},"items":[{"metadata":
   {"name":"cirrus12","selfLink":"/apis/metrics.k8s.io/v1beta1/nodes/cirrus12",
   "creationTimestamp":"2019-08-08T23:59:49Z"},"timestamp":"2019-08-08T23:59:08Z",
   "window":"30s","usage":{"cpu":"1738876098n","memory":"8406880Ki"}}]}
   ```