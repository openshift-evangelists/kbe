+++
title = "Pods"
subtitle = "Kubernetes pods by example"
date = "2019-03-23"
url = "/pods/"
+++

A pod is a collection of containers sharing a network and mount namespace
and is the basic unit of deployment in Kubernetes. All containers in a pod
are scheduled on the same node.

To launch a pod using the container [image](https://quay.io/repository/openshiftlabs/simpleservice/)
`quay.io/openshiftlabs/simpleservice:0.5.0` and exposing a HTTP API on port `9876`, execute:

```bash
kubectl run sise --image=quay.io/openshiftlabs/simpleservice:0.5.0 --port=9876
```
```cat
pod/sise created
```

***Note: Deprecation Warning!***
Older releases of `kubectl` will produce a [deployment](/deployments/) resource as the result of the provided `kubectl run` example, while newer releases produce a single `pod` resource.  The example commands in this section should still work (assuming you substitute your own pod name) - but you'll need to run `kubectl delete deployment sise` at the end of this section to clean up.

Check to see if the pod is running:

```bash
kubectl get pods
```
```cat
NAME    READY     STATUS    RESTARTS   AGE
sise    1/1       Running   0          1m
```

If the above output returns a longer pod name, make sure to use it in the following examples (in place of `sise`)

This container image happens to include a copy of `curl`, which provides an additional way to verify that the primary webservice process is responding (over the local net at least):

```bash
kubectl exec sise -t -- curl -s localhost:9876/info
```
```cat
{"host": "localhost:9876", "version": "0.5.0", "from": "127.0.0.1"}
```

From within the cluster (e.g. via `kubectl exec` or [`oc rsh`](https://docs.openshift.com/container-platform/latest/cli_reference/openshift_cli/developer-cli-commands.html#rsh)) this pod will also be directly accessible via it's associated pod IP `172.17.0.3`

```bash
kubectl describe pod sise | grep IP:
```
```cat
IP:                     172.17.0.3
```

The kubernetes API provides an additional opportunity to connect directly to pods using `curl`:
```bash
export OPENSHIFT_API="https://$(kubectl config get-clusters | tail -n 1)"
export NAMESPACE="default"
export PODNAME="sise"
curl -s -k -H"Authorization: Bearer $(oc whoami -t)" \
$OPENSHIFT_API/api/v1/namespaces/$NAMESPACE/pods/$PODNAME/proxy/info
```

Cleanup:
```bash
kubectl delete pod,deployment sise
```

#### Using configuration file

You can also create a pod from a configuration file.
In this case the [pod](https://github.com/openshift-evangelists/kbe/blob/main/specs/pods/pod.yaml) is
running the already known `simpleservice` image from above along with
a generic `CentOS` container:

```bash
kubectl apply -f https://raw.githubusercontent.com/openshift-evangelists/kbe/main/specs/pods/pod.yaml
```
```cat
pod/twocontainers created
```

```bash
kubectl get pods
```
```cat
NAME                      READY     STATUS    RESTARTS   AGE
twocontainers             2/2       Running   0          7s
```

Containers that share a pod are able to communicate using local networking. 

This example demonstrates how to exec into a sidecar `shell` container to access and inspect the `sise` container via `localhost`:

```bash
kubectl exec twocontainers -t -- curl -s localhost:9876/info
```
```cat
{"host": "localhost:9876", "version": "0.5.0", "from": "127.0.0.1"}
```

Define the `resources` attribute to influence how much CPU and/or RAM a
container in a [pod](https://github.com/openshift-evangelists/kbe/blob/main/specs/pods/constraint-pod.yaml) can use (here: `64MB` of RAM and `0.5` CPUs):

```bash
kubectl create -f https://raw.githubusercontent.com/openshift-evangelists/kbe/main/specs/pods/constraint-pod.yaml
```
```cat
pod/constraintpod created
```

```bash
kubectl describe pod constraintpod
```
```cat
...
Containers:
  sise:
    ...
    Limits:
      cpu:      500m
      memory:   64Mi
    Requests:
      cpu:      500m
      memory:   64Mi
...
```

Learn more about resource constraints in Kubernetes via the docs [here](https://kubernetes.io/docs/tasks/configure-pod-container/assign-cpu-ram-container/)
and [here](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/).

To clean up and remove all the remaining pods, try:

```bash
kubectl delete pod twocontainers
```
```bash
kubectl delete pod constraintpod
```

To sum up, launching one or more containers (together) in Kubernetes is simple,
however doing it directly as shown above comes with a serious limitation: you have to
manually take care of keeping them running in case of a failure. A better way
to supervise pods is to use [deployments](/deployments), giving you much more control over the life cycle, including rolling out a new version.

[Next](/labels)
