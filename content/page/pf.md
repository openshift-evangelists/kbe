+++
title = "Port Forward"
subtitle = "Kubernetes port forward by example"
date = "2019-02-12"
url = "/pf/"
+++

In the context of developing apps on Kubernetes it is often useful to quickly access a service from your local environment without exposing it using, for example, a load balancer or an ingress resource. In this case you can use [port forwarding](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/).

Let's create an [app](https://github.com/mhausenblas/kbe/blob/master/specs/pf/app.yaml) consisting of a deployment and a service called `simpleservice`, serving on port `80`:

```bash
$ kubectl apply -f https://raw.githubusercontent.com/openshift-evangelists/kbe/master/specs/pf/app.yaml
```

Let's say want to access the `simpleservice` service from the local environment, say, your laptop, on port `8080`. So we forward the traffic as follows:

```bash
$ kubectl port-forward service/simpleservice 8080:80
Forwarding from 127.0.0.1:8080 -> 9876
Forwarding from [::1]:8080 -> 9876
```

We can see from above that the traffic gets eventually routed through the service to the pod serving on port `9876`.

Now we can invoke the service locally like so (using a separate terminal session):

```bash
$ curl localhost:8080/info
{"host": "localhost:8080", "version": "0.5.0", "from": "127.0.0.1"}
```

Remember that port forwarding is not meant for production traffic but for development and experimentation.

[Previous](/sd) | [Next](/healthz)
