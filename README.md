# Fluentd Daemonset for Kubernetes

Inspired from Fluentd's [Kubernetes Logging with Fluentd](http://docs.fluentd.org/v0.12/articles/kubernetes-fluentd#get-fluentd-daemonset-sources) and Weaveworks [Log Aggregation for Kubernetes with Loggly](https://www.weave.works/log-aggregation-kubernetes-loggly/) articles.

## Configuration

Make sure you grab a valid Loggly token from [the customer token page](https://www.loggly.com/docs/customer-token-authentication-token). In the next step, I'll use LOCAL_LOGGLY_TOKEN var tas a placeholder for this value.

We'll save this token as an EC2 SSM Parameter Store SecureString, using [`psc`](https://github.com/smooch/parameter-store-client), the Parameter Store Client (`pip install psc` if you need it)

`psc set "staging.loggly.token" "$LOCAL_LOGGLY_TOKEN" -r us-east-1`

The `staging.loggly.token` value is the key of our secret, you can use whatever you like (as long as it meets the Parameter Store constraints), but change this, make sure the env var we inject in our pod is changed accordingly. (in the fluentd-loggly-cm.yaml template, or by adding some value to override this default)

Then we will create a configMap with both the `kubernetes.conf` file, and a `fluent.conf` template. Both these files will be populated in the /fluentd/etc/ folder of the pod container.


```
# Create the secret and the configmap
kubectl create -f fluentd-loggly-secret.yaml -f fluentd-loggly-cm.yaml
```

Now we only have to create the Daemonset:

A simple `kubectl create -f fluentd-loggly-ds.yaml --record` will get the daemonSet created.

Please note that rolling-updates are not yet supported for Daemonset updates, and you'll have to do it manually if you want to update anything. Rolling-update support for Daemonset should come in [Kubernetes 1.6](https://github.com/kubernetes/kubernetes/issues/22543).

Now you can check your components with

    kubectl get secret,cm,deploy,ds,pod -n kube-system -l k8s-app=fluentd -o yaml

You shoudl be able to see your logs soon in Loggly, and you can filter them with `tag:fluentd` if you kept the sample config.
