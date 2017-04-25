+++
title = "Services"
subtitle = "Kubernetes services by example"
date = "2017-04-25"
url = "/services/"
+++

Using this RC and this SVC.

```bash
$ kubectl get pods
NAME         READY     STATUS    RESTARTS   AGE
sise-bkvhs   1/1       Running   0          9m

$ kubectl describe pod sise-bkvhs
Name:                   sise-bkvhs
Namespace:              namingthings
Security Policy:        restricted
Node:                   192.168.99.100/192.168.99.100
Start Time:             Tue, 18 Apr 2017 09:45:22 +0100
Labels:                 app=sise
Status:                 Running
IP:                     172.17.0.2
Controllers:            ReplicationController/sise
...
```

Now, from within the K8S cluster:

```bash
$ curl 172.17.0.2:9876/endpoint0
{"host": "172.17.0.2:9876", "version": "0.4.0", "result": "all is well"}
```

And service:

```bash
$ kubectl get svc
NAME            CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
simpleservice   172.30.40.155   <none>        80/TCP    1m

$ kubectl describe svc simpleservice
Name:                   simpleservice
Namespace:              namingthings
Labels:                 name=simpleservice
Selector:               app=sise
Type:                   ClusterIP
IP:                     172.30.40.155
Port:                   <unset> 80/TCP
Endpoints:              172.17.0.2:9876
Session Affinity:       None
No events.
```

And from within the cluster:

```bash
$ curl 172.30.40.155:80/endpoint0
{"host": "172.30.40.155", "version": "0.4.0", "result": "all is well"}
```

Looking at the routing table in the cluster:

```bash
$ sudo iptables-save | grep simpleservice
-A KUBE-SEP-ASIN52LB5SMYF6KR -s 172.17.0.2/32 -m comment --comment "namingthings/simpleservice:" -j KUBE-MARK-MASQ
-A KUBE-SEP-ASIN52LB5SMYF6KR -p tcp -m comment --comment "namingthings/simpleservice:" -m tcp -j DNAT --to-destination 172.17.0.2:9876
-A KUBE-SERVICES -d 172.30.40.155/32 -p tcp -m comment --comment "namingthings/simpleservice: cluster IP" -m tcp --dport 80 -j KUBE-SVC-IKIIGXZ2IBFIBYI6
-A KUBE-SVC-IKIIGXZ2IBFIBYI6 -m comment --comment "namingthings/simpleservice:" -j KUBE-SEP-ASIN52LB5SMYF6KR
```

Now let's add a second pod to the service:

```bash
$ kubectl scale --replicas=2 rc/sise
replicationcontroller "sise" scaled
$ kubectl get pods
NAME         READY     STATUS    RESTARTS   AGE
sise-bkvhs   1/1       Running   0          11m
sise-vncz4   1/1       Running   0          8s
```

And check the routing again:

```bash
$ sudo iptables-save | grep simpleservice
-A KUBE-SEP-ASIN52LB5SMYF6KR -s 172.17.0.2/32 -m comment --comment "namingthings/simpleservice:" -j KUBE-MARK-MASQ
-A KUBE-SEP-ASIN52LB5SMYF6KR -p tcp -m comment --comment "namingthings/simpleservice:" -m tcp -j DNAT --to-destination 172.17.0.2:9876
-A KUBE-SEP-RP53IYKEFRDLQANZ -s 172.17.0.4/32 -m comment --comment "namingthings/simpleservice:" -j KUBE-MARK-MASQ
-A KUBE-SEP-RP53IYKEFRDLQANZ -p tcp -m comment --comment "namingthings/simpleservice:" -m tcp -j DNAT --to-destination 172.17.0.4:9876
-A KUBE-SERVICES -d 172.30.40.155/32 -p tcp -m comment --comment "namingthings/simpleservice: cluster IP" -m tcp --dport 80 -j KUBE-SVC-IKIIGXZ2IBFIBYI6
-A KUBE-SVC-IKIIGXZ2IBFIBYI6 -m comment --comment "namingthings/simpleservice:" -m statistic --mode random --probability 0.50000000000 -j KUBE-SEP-ASIN52LB5SMYF6KR
-A KUBE-SVC-IKIIGXZ2IBFIBYI6 -m comment --comment "namingthings/simpleservice:" -j KUBE-SEP-RP53IYKEFRDLQANZ
```

Summary, figure setup and discussion on advanced services (scaling) and further reading
