+++
title = "Jobs"
subtitle = "Kubernetes jobs by example"
date = "2020-03-19"
url = "/jobs/"
+++

A [job](https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/) in Kubernetes is a supervisor for pods carrying out batch processes, that is,
a process that runs for a certain time to completion, for example a calculation
or a backup operation.

Let's create a [job](https://github.com/openshift-evangelists/kbe/blob/master/specs/jobs/job.yaml)
called `countdown` that supervises a pod counting from 9 down to 1:

```bash
$ kubectl apply -f https://raw.githubusercontent.com/openshift-evangelists/kbe/master/specs/jobs/job.yaml
```

You can see the job and the pod it looks after like so:

```bash
$ kubectl get jobs
NAME                      DESIRED   SUCCESSFUL   AGE
countdown                 1         1            5s

$ kubectl get pods
NAME              READY   STATUS      RESTARTS   AGE
countdown-qkjx8   0/1     Completed   0          2m17s
```

To learn more about the status of the job, do:

```bash
$ kubectl describe jobs/countdown
Name:           countdown
Namespace:      default
Selector:       controller-uid=4960c8be-6a3f-11ea-84fd-0242ac11002a
Labels:         controller-uid=4960c8be-6a3f-11ea-84fd-0242ac11002a
                job-name=countdown
Annotations:    kubectl.kubernetes.io/last-applied-configuration:
                  {"apiVersion":"batch/v1","kind":"Job","metadata":{"annotations":{},"name":"countdown","namespace":"default"},"spec":{"template":{"metadata...
Parallelism:    1
Completions:    1
Start Time:     Fri, 20 Mar 2020 00:11:03 +0000
Completed At:   Fri, 20 Mar 2020 00:11:12 +0000
Duration:       9s
Pods Statuses:  0 Running / 1 Succeeded / 0 Failed
Pod Template:
  Labels:  controller-uid=4960c8be-6a3f-11ea-84fd-0242ac11002a
           job-name=countdown
  Containers:
   counter:
    Image:      centos:7
    Port:       <none>
    Host Port:  <none>
    Command:
      bin/bash
      -c
      for i in 9 8 7 6 5 4 3 2 1 ; do echo $i ; done
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Events:
  Type    Reason            Age    From            Message
  ----    ------            ----   ----            -------
  Normal  SuccessfulCreate  3m18s  job-controller  Created pod: countdown-qkjx8
```

And to see the output of the job via the pod it supervised, execute:

```bash
kubectl logs countdown-qkjx8
9
8
7
6
5
4
3
2
1
```

To clean up, use the `delete` verb on the job object which will remove all the
supervised pods:

```bash
$ kubectl delete job countdown
job "countdown" deleted
```

Note that there are also more advanced ways to use jobs, for example,
by utilizing a [work queue](https://kubernetes.io/docs/tasks/job/coarse-parallel-processing-work-queue/)
or scheduling the execution at a certain time via [cron jobs](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/).

[Previous](/logging) | [Next](/statefulset)
