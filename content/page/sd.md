+++
title = "Service Discovery"
subtitle = "Kubernetes service discovery by example"
date = "2019-02-27"
url = "/sd/"
+++

Service discovery is the process of figuring out how to connect to a [service](/services/).
While there is a service discovery option based on [environment variables](https://kubernetes.io/docs/concepts/services-networking/connect-applications-service/#environment-variables) available,
the DNS-based service discovery is preferable. Note that [Kube DNS is a cluster add-on](https://github.com/kubernetes/kubernetes/blob/master/cluster/addons/dns/kube-dns/README.md), which means that it may need to installed, configured, or enabled in order to function correctly.

Let's create a [service](https://github.com/openshift-evangelists/kbe/blob/main/specs/sd/svc.yaml) named
`thesvc` and an [RC](https://github.com/openshift-evangelists/kbe/blob/main/specs/sd/rc.yaml) supervising
some pods along with it:

```bash
kubectl apply -f https://raw.githubusercontent.com/openshift-evangelists/kbe/main/specs/sd/rc.yaml
```

```bash
kubectl apply -f https://raw.githubusercontent.com/openshift-evangelists/kbe/main/specs/sd/svc.yaml
```

Now we want to connect to the `thesvc` service from within the cluster, say, from another service.
To simulate this, we create a [jump pod](https://github.com/openshift-evangelists/kbe/blob/main/specs/sd/jumpod.yaml)
in the same namespace (`default`, since we didn't specify anything else):

```bash
kubectl apply -f https://raw.githubusercontent.com/openshift-evangelists/kbe/main/specs/sd/jumpod.yaml
```

The DNS add-on will make sure that our service `thesvc` is available via the FQDN
`thesvc.default.svc.cluster.local` from other pods in the cluster. Let's try it out:

```bash
kubectl exec -it jumpod -c shell -- ping thesvc.default.svc.cluster.local
```
```cat
PING thesvc.default.svc.cluster.local (172.30.251.137) 56(84) bytes of data.
...
```

Send a break signal (Ctrl-C) to close the connection
```bash
^C
```

The answer to the `ping` tells us that the service is available via the cluster
IP `172.30.251.137`. We can directly connect to and consume the service (in the same namespace) like so:

```bash
kubectl exec -it jumpod -c shell -- curl http://thesvc/info
```
```cat
{"host": "thesvc", "version": "0.5.0", "from": "172.17.0.5"}
```

Note that the IP address `172.17.0.5` above is the cluster-internal IP address
of the jump pod.

To access a service that is deployed in a different namespace than the one you're
accessing it from, use a FQDN in the form `$SVC.$NAMESPACE.svc.cluster.local`.

Let's see how that works by creating:

1. a [namespace](https://github.com/openshift-evangelists/kbe/blob/main/specs/sd/other-ns.yaml) `other`
1. a [service](https://github.com/openshift-evangelists/kbe/blob/main/specs/sd/other-svc.yaml) `thesvc` in namespace `other`
1. an [RC](https://github.com/openshift-evangelists/kbe/blob/main/specs/sd/other-rc.yaml) supervising the pods, also in namespace `other`

If you're not familiar with namespaces, check out the [namespace examples](/ns/) first.

```bash
kubectl apply -f https://raw.githubusercontent.com/openshift-evangelists/kbe/main/specs/sd/other-ns.yaml
```
```bash
kubectl apply -f https://raw.githubusercontent.com/openshift-evangelists/kbe/main/specs/sd/other-rc.yaml
```
```bash
kubectl apply -f https://raw.githubusercontent.com/openshift-evangelists/kbe/main/specs/sd/other-svc.yaml
```

We're now in the position to consume the service `thesvc` in namespace `other` from the
`default` namespace (again via the jump pod):

```bash
kubectl exec -it jumpod -c shell -- curl http://thesvc.other/info
```
```cat
{"host": "thesvc.other", "version": "0.5.0", "from": "172.17.0.5"}
```

Summing up, DNS-based service discovery provides a flexible and generic way to
connect to services across the cluster.

You can destroy all the resources created with:

```bash
kubectl delete pods jumpod
```
```bash
kubectl delete svc thesvc
```
```bash
kubectl delete rc rcsise
```
```bash
kubectl delete ns other
```

Keep in mind that removing a namespace will destroy every resource inside.

[Previous](/services) | [Next](/pf)
