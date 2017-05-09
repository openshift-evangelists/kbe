+++
title = "Service Discovery"
subtitle = "Kubernetes service discovery by example"
date = "2017-05-09"
url = "/sd/"
+++

Service discovery is

Let's create an [RC](https://github.com/mhausenblas/kbe/blob/master/specs/sd/rc.yaml)
and a [service](https://github.com/mhausenblas/kbe/blob/master/specs/sd/svc.yaml)
along with it:

```bash
$ kubectl create -f https://raw.githubusercontent.com/mhausenblas/kbe/master/specs/sd/rc.yaml

$ kubectl create -f https://raw.githubusercontent.com/mhausenblas/kbe/master/specs/sd/svc.yaml
```

Create [jump pod](https://github.com/mhausenblas/kbe/blob/master/specs/sd/jumpod.yaml):

$ kubectl create -f https://raw.githubusercontent.com/mhausenblas/kbe/master/specs/sd/jumpod.yaml
```

Look up:

```bash
$ kubectl exec jumpod -c shell -i -t -- dig cluster.local
```
