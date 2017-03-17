# Fluentd Daemonset for Kubernetes

Inspired from Fluentd's [Kubernetes Logging with Fluentd](http://docs.fluentd.org/v0.12/articles/kubernetes-fluentd#get-fluentd-daemonset-sources) and Weaveworks [Log Aggregation for Kubernetes with Loggly](https://www.weave.works/log-aggregation-kubernetes-loggly/) articles.

## Configuration

Make sure you grab a valid Loggly token from [the customer token page](https://www.loggly.com/docs/customer-token-authentication-token), and put it as `common.LOGGLY_TOKEN` secret in Kubernetes, or update the yaml accordingly.
