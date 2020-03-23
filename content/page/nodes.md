+++
title = "Nodes"
subtitle = "Kubernetes nodes by example"
date = "2020-03-23"
url = "/nodes/"
+++

In Kubernetes, nodes are the (virtual) machines where your workloads in shape of pods run. As a developer you typically don't deal with nodes directly, however as an admin
you might want to familiarize yourself with node [operations](https://kubernetes.io/docs/concepts/nodes/node/).

To list available nodes in your cluster (note that the output will depend on the environment
you're using. This example is using the [OpenShift Playground](/diy/)):

```bash
$ kubectl get nodes
NAME                 STATUS   ROLES           AGE    VERSION
crc-rk2fc-master-0   Ready    master,worker   102d   v1.14.6+888f9c630
```

One interesting task, from a developer point of view, is to make Kubernetes
schedule a pod on a certain node. For this, we first need to label the node
we want to target:

```bash
$ kubectl label nodes crc-rk2fc-master-0 shouldrun=here
node/crc-rk2fc-master-0 labeled
```

Now we can create a [pod](https://github.com/openshift-evangelists/kbe/blob/master/specs/nodes/pod.yaml)
that gets scheduled on the node with the label `shouldrun=here`:

```bash
$ kubectl apply -f https://raw.githubusercontent.com/openshift-evangelists/kbe/master/specs/nodes/pod.yaml

$ kubectl get pods --output=wide
NAME             READY   STATUS    RESTARTS   AGE     IP            NODE            NOMINATED NODE   READINESS GATES
onspecificnode   1/1     Running   0          2m31s   10.128.1.11   crc-rk2fc-master-0   <none>           <none>
```

To learn more about a specific node, `crc-rk2fc-master-0` in our case, do:

```bash
$ kubectl describe node crc-rk2fc-master-0
Name:               crc-rk2fc-master-0
Roles:              master,worker
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=crc-rk2fc-master-0
                    kubernetes.io/os=linux
                    node-role.kubernetes.io/master=
                    node-role.kubernetes.io/worker=
                    node.openshift.io/os_id=rhcos
                    shouldrun=here
Annotations:        machine.openshift.io/machine: openshift-machine-api/crc-rk2fc-master-0
                    machineconfiguration.openshift.io/currentConfig: rendered-master-757d2d73a4ba859a3508c78070169043
                    machineconfiguration.openshift.io/desiredConfig: rendered-master-757d2d73a4ba859a3508c78070169043
                    machineconfiguration.openshift.io/reason:
                    machineconfiguration.openshift.io/ssh: accessed
                    machineconfiguration.openshift.io/state: Done
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Thu, 12 Dec 2019 06:47:09 +0000
Taints:             <none>
Unschedulable:      false
Conditions:
  Type             Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----             ------  -----------------                 ------------------                ------                       -------
  MemoryPressure   False   Mon, 23 Mar 2020 18:20:39 +0000   Thu, 12 Dec 2019 06:47:08 +0000   KubeletHasSufficientMemory   kubelet has sufficient memoryavailable
  DiskPressure     False   Mon, 23 Mar 2020 18:20:39 +0000   Thu, 12 Dec 2019 06:47:08 +0000   KubeletHasNoDiskPressure     kubelet has no disk pressure
  PIDPressure      False   Mon, 23 Mar 2020 18:20:39 +0000   Thu, 12 Dec 2019 06:47:08 +0000   KubeletHasSufficientPID      kubelet has sufficient PID available
  Ready            True    Mon, 23 Mar 2020 18:20:39 +0000   Mon, 23 Mar 2020 17:56:48 +0000   KubeletReady                 kubelet is posting ready status
Addresses:
  InternalIP:  172.17.0.23
  Hostname:    crc-rk2fc-master-0
Capacity:
 cpu:            6
 hugepages-1Gi:  0
 hugepages-2Mi:  0
 memory:         16033876Ki
 pods:           250
Allocatable:
 cpu:            5500m
 hugepages-1Gi:  0
 hugepages-2Mi:  0
 memory:         15419476Ki
 pods:           250
System Info:
 Machine ID:                                             33c8de1eb5364c94b5e215e58eef30ac
 System UUID:                                            33c8de1eb5364c94b5e215e58eef30ac
 Boot ID:                                                f51f55d7-7702-48bc-bbc0-d68372e0fbf1
 Kernel Version:                                         4.18.0-147.0.3.el8_1.x86_64
 OS Image:                                               Red Hat Enterprise Linux CoreOS 42.81.20191203.0 (Ootpa)
 Operating System:                                       linux
 Architecture:                                           amd64
 Container Runtime Version:                              cri-o://1.14.11-0.24.dev.rhaos4.2.gitc41de67.el8
 Kubelet Version:                                        v1.14.6+888f9c630
 Kube-Proxy Version:                                     v1.14.6+888f9c630
Non-terminated Pods:                                     (46 in total)
  Namespace                                              Name                                               CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                                              ----                                               ------------  ----------  ---------------  -------------  ---
  default                                                onspecificnode                                               0 (0%)        0 (0%)      0 (0%)           0 (0%)         3m2s
  openshift-apiserver                                    
...
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource           Requests      Limits
  --------           --------      ------
  cpu                3010m (54%)   700m (12%)
  memory             8289Mi (55%)  687Mi (4%)
  ephemeral-storage  0 (0%)        0 (0%)
```

Note that there are more sophisticated methods than shown above, such as using affinity, to [assign pods to nodes](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) and depending on your use case, you might want to check those out as well.

[Previous](/ic) | [Next](/api)
