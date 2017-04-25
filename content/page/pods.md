+++
title = "Pods"
subtitle = "Kubernetes pods by example"
date = "2017-04-24"
url = "/pods/"
+++

The basic unit of deployment in Kubernetes is a pod. A pod is a collection of
containers sharing a network and mount namespace with the guarantee that all of
the containers are scheduled on the same node.

To launch a pod using a [Docker image](https://github.com/mhausenblas/simpleservice)
that exposes a HTTP API at port `9876`, execute:

```bash
$ kubectl run sise --image=mhausenblas/simpleservice:0.5.0 --port=9876
```

We can now see that the pod is running:

```bash
$ kubectl get pods
NAME                      READY     STATUS    RESTARTS   AGE
sise-3210265840-k705b     1/1       Running   0          1m

$ kubectl describe pod sise-3210265840-k705b | grep IP:
IP:                     172.17.0.3
```

From within the cluster this pod is accessible via the pod IP `172.17.0.3`,
which we've learned from the `kubectl describe` command above:

```bash
[cluster] $ curl 172.17.0.3:9876/info
{"host": "172.17.0.3:9876", "version": "0.5.0", "from": "172.17.0.1"}
```

Note that `kubectl run` creates a deployment, so in order to get rid of the pod
you have to execute `kubectl delete deployment sise`.


Alternatively, a pod can be created from a configuration file, in this case
[pod.yaml](https://github.com/mhausenblas/kbe/blob/master/specs/pods/pod.yaml),
running the already known `simpleservice` image from above along with
a generic `CentOS` container:

```bash
$ kubectl create -f https://raw.githubusercontent.com/mhausenblas/kbe/master/specs/pods/pod.yaml

$ kubectl get pods
NAME                      READY     STATUS    RESTARTS   AGE
twocontainers             2/2       Running   0          7s
```

Now we can exec into the `CentOS` container and access the `simpleservice`
on localhost:

```bash
$ kubectl exec twocontainers -c shell -i -t -- bash
[root@twocontainers /]# curl localhost:9876/info
{"host": "localhost:9876", "version": "0.5.0", "from": "127.0.0.1"}
```

To sum up, launching one or more containers in Kubernetes is simple, however
doing it directly as shown above comes with a serious limitation: you have to
manually take care of keeping it running in case of a failure. A much better way
to supervise pods is to use [replication controllers](/rcs/).
