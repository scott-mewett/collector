# Minimum Requirements

## Introduction
This document provides information related to the minimum requirements for running RealTheory with your cluster

## RBAC
RBAC, role-based access control is a method of regulating access to computer or network resources based on the roles of individual users within your organization.

In order to successfully install the RealTheory collector within your cluster, you will require specific RBAC access; specifically you will require the ability to create a service account, cluster role, cluster role binding and a deployment.

## How do I check if I have the required access to install the collector?

If RBAC is enabled, you will require RBAC access to create a service account, cluster role, cluster role binding and a deployment.

1. To check if RBAC is enabled, execute the following command:

    ```sh
    kubectl api-versions
    ```

    If RBAC is enabled, you should see the following API version within the output
    ```sh
    rbac.authorization.k8s.io/v1
    ```

1. To check whether you can create a service account, execute the following command

    ```sh
    kubectl auth can-i create serviceaccounts --all-namespaces
    ```

    If the command responds with yes, you have the required access

1. To check whether you can create a cluster role, execute the following command

    ```sh
    kubectl auth can-i create clusterroles --all-namespaces
    ```

    If the command responds with yes, you have the required access

1. To check whether you can create a cluster role binding, execute the following command

    ```sh
    kubectl auth can-i create clusterrolebindings --all-namespaces
    ```

    If the command responds with yes, you have the required access

1. To check whether you can create a deployment, execute the following command

    ```sh
    kubectl auth can-i create deployments --all-namespaces
    ```

    If the command responds with yes, you have the required access