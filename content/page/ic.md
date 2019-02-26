+++
title = "Init Containers"
subtitle = "Kubernetes init containers by example"
date = "2019-02-26"
url = "/ic/"
+++

It's sometimes necessary to prepare a container running in a pod. For example, you might want to wait for a service being available, want to configure things at runtime, or init some data in a database. In all of these cases, [init containers](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) are useful. Note that Kubernetes will execute all init containers (and they must all exit successfully) before the main container(s) are executed. 

So let's create an [deployment](https://github.com/openshift-evangelists/kbe/blob/master/specs/ic/deploy.yaml) consisting of an init container that writes a message into a file at `/ic/this` and the main (long-running) container reading out this file, then:

```bash
$ kubectl apply -f https://raw.githubusercontent.com/openshift-evangelists/kbe/master/specs/ic/deploy.yaml
```

Now we can check the output of the main container:

```bash
$ kubectl get deploy,po
NAME                              DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/ic-deploy   1         1         1            1           11m

NAME                            READY   STATUS    RESTARTS   AGE
pod/ic-deploy-bf75cbf87-8zmrb   1/1     Running   0          59s

$ kubectl logs ic-deploy-bf75cbf87-8zmrb -f
INIT_DONE
INIT_DONE
INIT_DONE
INIT_DONE
INIT_DONE
^C
```

If you want to learn more about init containers and related topics, check out the blog post [Kubernetes: A Pod’s Life](https://blog.openshift.com/kubernetes-pods-life/).


[Previous](/statefulset) | [Next](/nodes)
