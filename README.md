# Fluentd Daemonset for Kubernetes

Inspired from Fluentd's [Kubernetes Logging with Fluentd](http://docs.fluentd.org/v0.12/articles/kubernetes-fluentd#get-fluentd-daemonset-sources) and Weaveworks [Log Aggregation for Kubernetes with Loggly](https://www.weave.works/log-aggregation-kubernetes-loggly/) articles.

## Configuration

Make sure you grab a valid Loggly token from [the customer token page](https://www.loggly.com/docs/customer-token-authentication-token), then create the config like this (assuming the token is in the LOGGLY_TOKEN env var)

```
sed "s/LOGGLY_TEMPLATE/$(sed "s/LOGGLY_TOKEN/$LOGGLY_TOKEN/g" fluent.template.conf | base64)/g" fluentd-loggly-secret.template.yaml > fluentd-loggly-secret.yaml

kubectl create -f fluentd-loggly-secret.yaml
```

## Installation

Then a simple `kubectl create -f fluentd-loggly-ds.yaml --record` will get the daemonSet created.