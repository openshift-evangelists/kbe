+++
title = "Namespaces"
subtitle = "Kubernetes namespaces by example"
date = "2017-04-26"
url = "/ns/"
+++

Namespaces provide for a scope of Kubernetes objects. You can think of it
as a workspace you're sharing with other users. Many objects such as pods and
services are namespaced, while some (like nodes) are not. As a developer you'd
usually simply use an assigned namespace, however admins may wish to manage them,
for example to set up access control or resource quotas.

Let's list all namespaces (note that the output will depend on the environment
you're using, I'm using [Minishift](/diy/)):

```bash
$ kubectl get ns
NAME              STATUS    AGE
default           Active    13d
kube-system       Active    13d
namingthings      Active    12d
openshift         Active    13d
openshift-infra   Active    13d
```

You can learn more about a namespace using the `describe` verb, for example:

```bash
$ kubectl describe ns default
Name:   default
Labels: <none>
Status: Active

No resource quota.

No resource limits.
```

Let's now create a new [namespace](https://github.com/mhausenblas/kbe/blob/master/specs/ns/ns.yaml)
called `test` now:

```bash
$ kubectl create -f https://raw.githubusercontent.com/mhausenblas/kbe/master/specs/ns/ns.yaml
namespace "test" created

$ kubectl get ns
NAME              STATUS    AGE
default           Active    13d
kube-system       Active    13d
namingthings      Active    12d
openshift         Active    13d
openshift-infra   Active    13d
test              Active    3s
```

To launch a [pod](https://github.com/mhausenblas/kbe/blob/master/specs/ns/pod.yaml) in
the newly created namespace `test`, do:

```bash
$ kubectl create --namespace=test -f https://raw.githubusercontent.com/mhausenblas/kbe/master/specs/ns/pod.yaml
```

Note that using above method the namespace becomes a runtime property, that is,
you can easily deploy the same pod or service, or RC, etc. into multiple
namespaces (for example: `dev` and `prod`). If you however prefer to hard-code the
namespace, you can define it directly in the `metadata` like so:

```
apiVersion: v1
kind: Pod
metadata:
  name: podintest
  namespace: test
```

To list namespaced objects such as our pod `podintest`, run following command as:

```bash
$ kubectl get pods --namespace=test
NAME        READY     STATUS    RESTARTS   AGE
podintest   1/1       Running   0          16s
```

You can remove the namespace (and everything inside) with:

```bash
$ kubectl delete ns test
```

If you're an admin, you might want to check out the [docs](https://kubernetes.io/docs/tasks/administer-cluster/namespaces/)
for more info how to handle namespaces.

[Previous](/envs) | [Next](/volumes)
