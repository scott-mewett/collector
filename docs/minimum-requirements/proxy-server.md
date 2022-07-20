# Minimum Requirements

## Introduction
This document provides information related to the minimum requirements for running RealTheory with your cluster

## Proxy Server

A proxy server is a server application that acts as an intermediary between a client requesting a resource and the server providing that resource. Instead of connecting directly to a server that can fulfill a requested resource, such as a web page, the client directs the request to the proxy server, which evaluates the request and performs the required network transactions.

The RealTheory collector supports using a proxy server to connect to the Internet.

## How do I check if my cluster requires a proxy server?

The best way to check if your cluster requires a proxy server to connect to the Internet is to check with your network administrator.

## How do I enable proxy server support?
The RealTheory collector supports using a proxy server to connect to the Internet using the following environment values:

1. HTTP_PROXY - Defines the URL of the proxy server for HTTP requests to access the Internet.
1. HTTPS_PROXY - Defines the URL of the proxy server for HTTPS requests to access the Internet.
1. NO_PROXY - Defines the host names that shouldn’t go through the proxy server to access the Internet.

If you require a proxy server to connect to the Internet, add the following *name* and *value* pair under the *spec > template > spec > containers > env* section of the collector deployment manifest.

1. Proxy server with authentication using a username and password

   Usage:
   ```
   - name: HTTPS_PROXY
     value: https://USERNAME:PASSWORD@ADDRESS:PORT/
   ```

   Example
   ```
   - name: HTTPS_PROXY
     value: https://myusername:mypassword@47.91.179.52:443
   ```

1. Proxy server without authentication

   Usage:
   ```
   - name: HTTPS_PROXY
     value: https://ADDRESS:PORT/
   ```

   Example:
   ```
   - name: HTTPS_PROXY
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