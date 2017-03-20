# Fluentd Daemonset for Kubernetes

Inspired from Fluentd's [Kubernetes Logging with Fluentd](http://docs.fluentd.org/v0.12/articles/kubernetes-fluentd#get-fluentd-daemonset-sources) and Weaveworks [Log Aggregation for Kubernetes with Loggly](https://www.weave.works/log-aggregation-kubernetes-loggly/) articles.

## Configuration

Make sure you grab a valid Loggly token from [the customer token page](https://www.loggly.com/docs/customer-token-authentication-token), then create the config like this (assuming the token is in the LOGGLY_TOKEN env var)

Then we will create a configMap with both the `kubernetes.conf` file, and a `fluent.conf` template. Both these files will be populated in the /fluentd/etc/ folder of the pod container.

We also create a fluentd secret with our Loggly token, and we'll sed the fluent.conf file to inject this secret on container start, just before to start fluentd.


```
# Prepare the secret
B64_LOGGLY_TOKEN=`echo $LOGGLY_TOKEN | base64`
B64_LOGGLY_TOKEN_ESCAPED="$( echo "${B64_LOGGLY_TOKEN}" | sed 's/[\\&*./+!]/\\&/g' )"
sed "s/LOGGLY_TOKEN/${B64_LOGGLY_TOKEN_ESCAPED}/g" fluentd-loggly-secret.template.yaml > fluentd-loggly-secret.yaml

# Create the secret and the configmap
kubectl create -f fluentd-loggly-secret.yaml -f fluentd-loggly-cm.yaml
```

Now we only have to create the Daemonset:

A simple `kubectl create -f fluentd-loggly-ds.yaml --record` will get the daemonSet created.

Please note that rolling-updates are not yet supported for Daemonset updates, and you'll have to do it manually if you want to update anything. Rolling-update support for Daemonset should come in [Kubernetes 1.6](https://github.com/kubernetes/kubernetes/issues/22543).

Now you can check your components with

    kubectl get secret,cm,deploy,ds,pod -n kube-system -l k8s-app=fluentd -o yaml

You shoudl be able to see your logs soon in Loggly, and you can filter them with `tag:fluentd` if you kept the sample config.
