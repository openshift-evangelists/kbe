+++
title = "Health Checks"
subtitle = "Kubernetes health checks by example"
date = "2017-04-25"
url = "/healthz/"
+++

In order to verify if a container in a pod is in a state to serve traffic,
Kubernetes provides for a range of health checking mechanisms. We will
focus on HTTP health checks in the following. It is the responsibility
of the application developer to expose an endpoint that Kubernetes can
use to determine if the container is healthy.

Let's create a [pod](https://github.com/mhausenblas/kbe/blob/master/specs/healthz/pod.yaml)
that exposes an endpoint `health/`, responding with a HTTP `200` status code:

```bash
$ kubectl create -f https://raw.githubusercontent.com/mhausenblas/kbe/master/specs/healthz/pod.yaml
```

In the pod specification we've defined the following:

```
livenessProbe:
  initialDelaySeconds: 2
  periodSeconds: 5
  httpGet:
    path: /health
    port: 9876
```

Above means that Kubernetes will after 2 seconds check the `health/` endpoint
every 5 seconds.

If we now look at the pod we can see that it is considered healthy:

```bash
$ kubectl describe pod hc
Name:                   hc
Namespace:              default
Security Policy:        anyuid
Node:                   192.168.99.100/192.168.99.100
Start Time:             Tue, 25 Apr 2017 16:21:11 +0100
Labels:                 <none>
Status:                 Running
...
Events:
  FirstSeen     LastSeen        Count   From                            SubobjectPath           Type            Reason          Message
  ---------     --------        -----   ----                            -------------           --------        ------          -------
  3s            3s              1       {default-scheduler }                                    Normal          Scheduled       Successfully assigned hc to 192.168.99.100
  3s            3s              1       {kubelet 192.168.99.100}        spec.containers{sise}   Normal          Pulled          Container image "mhausenblas/simpleservice:0.5.0"
already present on machine
  3s            3s              1       {kubelet 192.168.99.100}        spec.containers{sise}   Normal          Created         Created container with docker id 8a628578d6ad; Security:[seccomp=unconfined]
  2s            2s              1       {kubelet 192.168.99.100}        spec.containers{sise}   Normal          Started         Started container with docker id 8a628578d6ad
```

Now we launch a [bad pod](https://github.com/mhausenblas/kbe/blob/master/specs/healthz/badpod.yaml),
that is, a pod that has a container that randomly (in the time range 1 to 4 sec)
does not return a 200 code:

```bash
$ kubectl create -f https://raw.githubusercontent.com/mhausenblas/kbe/master/specs/healthz/badpod.yaml
```

Looking at the events of the bad pod, we can see that the health check failed:

```bash
$ kubectl describe pod badpod
...
Events:
  FirstSeen     LastSeen        Count   From                            SubobjectPath           Type            Reason          Message
  ---------     --------        -----   ----                            -------------           --------        ------          -------
  1m            1m              1       {default-scheduler }                                    Normal          Scheduled       Successfully assigned badpod to 192.168.99.100
  1m            1m              1       {kubelet 192.168.99.100}        spec.containers{sise}   Normal          Created         Created container with docker id 7dd660f04945; Security:[seccomp=unconfined]
  1m            1m              1       {kubelet 192.168.99.100}        spec.containers{sise}   Normal          Started         Started container with docker id 7dd660f04945
  1m            23s             2       {kubelet 192.168.99.100}        spec.containers{sise}   Normal          Pulled          Container image "mhausenblas/simpleservice:0.5.0" already present on machine
  23s           23s             1       {kubelet 192.168.99.100}        spec.containers{sise}   Normal          Killing         Killing container with docker id 7dd660f04945: pod "badpod_default(53e5c06a-29cb-11e7-b44f-be3e8f4350ff)" container "sise" is unhealthy, it will be killed and re-created.
  23s           23s             1       {kubelet 192.168.99.100}        spec.containers{sise}   Normal          Created         Created container with docker id ec63dc3edfaa; Security:[seccomp=unconfined]
  23s           23s             1       {kubelet 192.168.99.100}        spec.containers{sise}   Normal          Started         Started container with docker id ec63dc3edfaa
  1m            18s             4       {kubelet 192.168.99.100}        spec.containers{sise}   Warning         Unhealthy       Liveness probe failed: Get http://172.17.0.4:9876/health: net/http: request canceled (Client.Timeout exceeded while awaiting headers)
```

This can also be verified as follows:

```bash
$ kubectl get pods
NAME                      READY     STATUS    RESTARTS   AGE
badpod                    1/1       Running   4          2m
hc                        1/1       Running   0          6m
```

From above you can see that the `badpod` had already been re-launched 4 times,
since the health check failed.
