# RealTheory Collector

## Introduction
This document provides information on how to add the RealTheory collector to your Kubernetes cluster.

## Minimum Requirements

### Kubernetes
The RealTheory collector is currently supported on the following versions of Kubernetes:

- 1.24
- 1.23
- 1.22
- 1.21
- 1.20
- 1.19
- 1.18 - Best experienced with version 1.18.9 or later
- 1.17 - Best experienced with version 1.17.12 or later
- 1.16 - Best experienced with version 1.16.15 or later

For more information, please visit https://github.com/realtheory/collector/blob/main/docs/minimum-requirements/kubernetes-version.md

### Metrics Server
The RealTheory collector requires the Metrics Server to be installed on the Kubernetes cluster. The Metrics Server is an aggregator of resource usage data in your Kubernetes cluster. For more information on the Kubernetes Metrics Server, including installation instructions, please visit https://github.com/kubernetes-sigs/metrics-server

The Metrics Server is automatically installed with some managed versions of Kubernetes. The following table indicates which managed Kubernetes offerings include the Metrics Server as part of the standard offering.

| Managed Kubernetes Offering | Includes Metrics Server | More information |
|-----------------------------|-------------------------|------------------|
| AKS | Yes | |
| GKE | Yes | |
| EKS | No | [How to install the Metrics Server on EKS](https://docs.aws.amazon.com/eks/latest/userguide/metrics-server.html) |

For more information, please visit https://github.com/realtheory/collector/blob/main/docs/minimum-requirements/metrics-server.md

### RBAC
In order to successfully install the RealTheory collector within your cluster, you will require specific RBAC access; specifically you will require the ability to create a service account, cluster role, cluster role binding and a deployment.

For more information, please visit https://github.com/realtheory/collector/blob/main/docs/minimum-requirements/rbac.md

## Usage
```sh
kubectl apply -f <filename>
```
where `<filename>` is the path to the RealTheory Collector configuration document.

## Configuration Structure
The RealTheory Collector configuration document is made up of 5 sections, namely:

- Namespace
- ServiceAccount
- ClusterRole
- ClusterRoleBinding
- Deployment

### Namespace
This section is used to define a dedicated namespace for the RealTheory collector to run in.

### ServiceAccount
This section is used to define a service account, which provides an identity for the RealTheory collector.

### ClusterRole
This section defines a cluster role, which is a set of permissions that can be assigned to resources within a cluster. This cluster role grants read access (get, watch, and list) to kubernetes control plane and metrics APIs.

### ClusterRoleBinding
This section defines a cluster role binding, which grants the Service Account for the collector the ability to read objects across all namespaces.

### Deployment
This section defines a deployment to launch the RealTheory collector pod within the real-theory-system namespace with a service account identity.

## Sample artifact

```yaml
# The namespace used for the Collector.
apiVersion: v1
kind: Namespace
metadata:
  labels:
    name: real-theory-system
  name: real-theory-system
---
# The Service Account used for the Collector.
apiVersion: v1
kind: ServiceAccount
metadata:
  name: real-theory-collector
  namespace: real-theory-system
---
# The cluster role grants access to read any objects across all namespaces.
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: object-reader
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: [get, watch, list]
- apiGroups: ["apps"]
  resources: ["*"]
  verbs: [get, watch, list]
- apiGroups: [metrics.k8s.io]
  resources: ["*"]
  verbs: [get, watch, list]
---
# The cluster role binding allows the Service Account for the collector in the real-theory-system namespace to read objects in any namespace.
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: read-objects-global
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: object-reader
subjects:
- kind: ServiceAccount
  name: real-theory-collector
  namespace: real-theory-system
---
# The deployment creates a replica set to launch the Collector pod.
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: realtheorycollector
  name: realtheorycollector
  namespace: real-theory-system
spec:
  replicas: 1
  selector:
    matchLabels:
      app: realtheorycollector
  template:
    metadata:
      labels:
        app: realtheorycollector
      name: realtheorycollector
    spec:
      containers:
      - env:
        - name: THEORY_LOGGING_LEVEL
          value: Info
        - name: THEORY_LOGGING_FILE_PATH
          value: /theory/logs/collector.log
        - name: THEORY_SERVICES_NETWORK_K8SROUTERURL
          value: https://api-beta.realtheory.io/theory/api/v1/k8srouter
        - name: THEORY_SERVICES_AUTHENTICATION_USERTOKEN
          value: <USER_TOKEN> # *** REPLACE WITH USER TOKEN ISSUED FROM REALTHEORY ***
        - name: THEORY_SECURITY_AUTHENTICATION_TYPE
          value: InsideCluster
        image: realtheory/theory-k8s-collector
        imagePullPolicy: Always
        livenessProbe:
          failureThreshold: 3
          httpGet:
            path: /theory/api/v1/collector/health
            port: 80
            scheme: HTTP
          initialDelaySeconds: 10
          periodSeconds: 30
          timeoutSeconds: 3
        name: realtheorycollector
        ports:
        - containerPort: 80
        readinessProbe:
          failureThreshold: 3
          httpGet:
            path: /theory/api/v1/collector/health
            port: 80
            scheme: HTTP
          initialDelaySeconds: 10
          periodSeconds: 30
          timeoutSeconds: 3
        volumeMounts:
        - mountPath: /theory/logs
          name: logs-volume
      nodeSelector:
        beta.kubernetes.io/os: linux
      serviceAccountName: real-theory-collector
      volumes:
      - emptyDir: {}
        name: logs-volume
```

Note: In the above document, <USER_TOKEN> will be replaced by a security token issued by RealTheory.

### Customization

If you would like to provide a custom name for your cluster, please add the following *name* and *value* pair under the *spec > template > spec > containers > env* section

```yaml
  - name: THEORY_CLUSTER_NAME
    value: <CLUSTER_NAME>
```

where <CLUSTER_NAME> is the custom name for your cluster.

An example might look like:

```yaml
# The deployment creates a replica set to launch the Collector pod.
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: realtheorycollector
  name: realtheorycollector
  namespace: real-theory-system
spec:
  replicas: 1
  selector:
    matchLabels:
      app: realtheorycollector
  template:
    metadata:
      labels:
        app: realtheorycollector
      name: realtheorycollector
    spec:
      containers:
      - env:
        - name: THEORY_LOGGING_LEVEL
          value: Info
        - name: THEORY_LOGGING_FILE_PATH
          value: /theory/logs/collector.log
        - name: THEORY_SERVICES_NETWORK_K8SROUTERURL
          value: https://api-beta.realtheory.io/theory/api/v1/k8srouter
        - name: THEORY_SERVICES_AUTHENTICATION_USERTOKEN
          value: BAAAAFRlc3TH0J3HOOcj3ZKALneTbdon65AU8TZGiP7XHu5HarCnM2vcJolMqknGgeSXkY5AMXnNZpvi1acmBOCcE4rpkHlGxbovqv2Qs4dWOhwlpwnGgeSXkY5AMXnNZpvi1acmBOCcE4rpkHJ3HOOcj3ZKALne
        - name: THEORY_SECURITY_AUTHENTICATION_TYPE
          value: InsideCluster
        - name: THEORY_CLUSTER_NAME
          value: MyCluster
          ...
```

If you require a proxy server to connect to the Internet, add the following *name* and *value* pair under the *spec > template > spec > containers > env* section of the collector deployment manifest:

**HTTP_PROXY**: Defines the URL of the proxy server for HTTP requests to access the Internet.

**HTTPS_PROXY**: Defines the URL of the proxy server for HTTPS requests to access the Internet.

**NO_PROXY**: Defines a comma-separated list of host names that shouldn’t go through the proxy server to access the Internet.

Examples:

1. Proxy server with authentication using a username and password

   Usage:
   ```
   - name: HTTP_PROXY
     value: https://USERNAME:PASSWORD@ADDRESS:PORT/
   ```

   Example
   ```
   - name: HTTP_PROXY
     value: https://myusername:mypassword@47.91.179.52:443
   ```

1. Proxy server without authentication

   Usage:
   ```
   - name: HTTP_PROXY
     value: https://ADDRESS:PORT/
   ```

   Example:
   ```
   - name: HTTP_PROXY
     value: https://47.91.179.52:443
   ```

An example might look like:

```yaml
# The deployment creates a replica set to launch the Collector pod.
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: realtheorycollector
  name: realtheorycollector
  namespace: real-theory-system
spec:
  replicas: 1
  selector:
    matchLabels:
      app: realtheorycollector
  template:
    metadata:
      labels:
        app: realtheorycollector
      name: realtheorycollector
    spec:
      containers:
      - env:
        - name: THEORY_LOGGING_LEVEL
          value: Info
        - name: THEORY_LOGGING_FILE_PATH
          value: /theory/logs/collector.log
        - name: THEORY_SERVICES_NETWORK_K8SROUTERURL
          value: https://api-beta.realtheory.io/theory/api/v1/k8srouter
        - name: THEORY_SERVICES_AUTHENTICATION_USERTOKEN
          value: BAAAAFRlc3TH0J3HOOcj3ZKALneTbdon65AU8TZGiP7XHu5HarCnM2vcJolMqknGgeSXkY5AMXnNZpvi1acmBOCcE4rpkHlGxbovqv2Qs4dWOhwlpwnGgeSXkY5AMXnNZpvi1acmBOCcE4rpkHJ3HOOcj3ZKALne
        - name: THEORY_SECURITY_AUTHENTICATION_TYPE
          value: InsideCluster
        - name: HTTPS_PROXY
          value: https://myusername:mypassword@47.91.179.52:443
          ...
```