+++
title = "Namespaces"
subtitle = "Kubernetes namespaces by example"
date = "2020-03-23"
url = "/ns/"
+++

Namespaces provide a scope for Kubernetes resources, carving up your cluster in smaller units. You can think of it
as a workspace you're sharing with other users. Many resources such as pods and
services are namespaced, while some, for example, nodes are not namespaced (but cluster-wide). As a developer you'd usually use an assigned namespace, however admins may wish to manage them, for example to set up access control or resource quotas.

Let's list all namespaces (note that the output will depend on the environment
you're using, we're using the [OpenShift Playground](/diy/) here):

```bash
$ kubectl get ns
NAME                                                    STATUS   AGE
default                                                 Active   98d
kube-node-lease                                         Active   98d
kube-public                                             Active   98d
kube-system                                             Active   98d
openshift                                               Active   98d
openshift-apiserver                                     Active   98d
...
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

Let's now create a new [namespace](https://github.com/openshift-evangelists/kbe/blob/master/specs/ns/ns.yaml)
called `test` now:

```bash
$ kubectl apply -f https://raw.githubusercontent.com/openshift-evangelists/kbe/master/specs/ns/ns.yaml
namespace "test" created

 kubectl get ns
NAME                                                    STATUS   AGE
default                                                 Active   98d
kube-node-lease                                         Active   98d
kube-public                                             Active   98d
kube-system                                             Active   98d
openshift                                               Active   98d
openshift-apiserver                                     Active   98d
...
test                                                    Active   16s
```

Alternatively, we could have created the namespace using the `kubectl create namespace test` command.

To launch a [pod](https://github.com/openshift-evangelists/kbe/blob/master/specs/ns/pod.yaml) in
the newly created namespace `test`, do:

```bash
$ kubectl apply --namespace=test -f https://raw.githubusercontent.com/openshift-evangelists/kbe/master/specs/ns/pod.yaml
```

Note that using above method the namespace becomes a runtime property, that is,
you can deploy the same pod or service, etc. into multiple
namespaces (for example: `dev` and `prod`). Hard-coding the
namespace directly in the `metadata` section like shown in the following is possible but causes less flexibility when deploying your apps:

```
apiVersion: v1
kind: Pod
metadata:
  name: podintest
  namespace: test
```

To list namespaced objects such as our pod `podintest`, run the following command:

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
