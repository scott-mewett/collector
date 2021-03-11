# RealTheory Collector

## 1. Introduction
This document provides information on how to add the RealTheory collector to your cluster.

## 2. Usage
```sh
kubectl apply -f <filename>
```
where <filename> is the path to the RealTheory Collector configuration document.

## 3. Configuration Structure
The RealTheory Collector configuration document is made up of 5 sections, namely:

  1. Namespace
  1. ServiceAccount
  1. ClusterRole
  1. ClusterRoleBinding
  1. Deployment

### 1. Namespace
This section is used to define a dedciated namespace for the RealTheory collector to run in.

### 2. ServiceAccount
This section is used to define a service account, which provides an identity for the RealTheory collector.

### 3. ClusterRole
This section defines a cluster role, which is a set of permissions that can be assigned to resources within a cluster. This cluster role grants read access (get, watch and list) to kubernetes control plane and metrics APIs.

### 4. ClusterRoleBinding
This section defines a cluster role binding, which grants the Service Account for collector in the real-theory-system namespace to read objects across all namespaces

### 5. Deployment
This section defines a deployment to launch the RealTheory collector pod within the real-theory-system namespace with a service account identity.

## 4. Sample artifact

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
- apiGroups: [metrics.k8s.io]
  resources: ["*"]
  verbs: [get, watch, list]
---
# The cluster role binding allows the Service Account for collector in the real-theory-system namespace to read objects in any namespace.
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

Note: In the above document, <USER_TOKEN> should be replaced by a security token issued by RealTheory.