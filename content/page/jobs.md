+++
title = "Jobs"
subtitle = "Kubernetes jobs by example"
date = "2017-04-27"
url = "/jobs/"
+++

A job is a supervisor for pods carrying out batch processes, that is,
a process that runs for a certain time to completion, for example a calculation
or a backup operation.

Let's create a [job](https://github.com/mhausenblas/kbe/blob/master/specs/jobs/job.yaml)
called `countdown` that supervises a pod counting from 9 down to 1:

```bash
$ kubectl create -f https://raw.githubusercontent.com/mhausenblas/kbe/master/specs/jobs/job.yaml
```

You can see the job and the pod it looks after like so:

```bash
$ kubectl get jobs
NAME                      DESIRED   SUCCESSFUL   AGE
countdown                 1         1            5s

$ kubectl get pods --show-all
NAME                            READY     STATUS      RESTARTS   AGE
countdown-lc80g                 0/1       Completed   0          16s
```

To learn more about the status of the job, do:

```bash
$ kubectl describe jobs/countdown
Name:           countdown
Namespace:      default
Image(s):       centos:7
Selector:       controller-uid=ff585b92-2b43-11e7-b44f-be3e8f4350ff
Parallelism:    1
Completions:    1
Start Time:     Thu, 27 Apr 2017 13:21:10 +0100
Labels:         controller-uid=ff585b92-2b43-11e7-b44f-be3e8f4350ff
                job-name=countdown
Pods Statuses:  0 Running / 1 Succeeded / 0 Failed
No volumes.
Events:
  FirstSeen     LastSeen        Count   From                    SubobjectPath   Type            Reason                  Message
  ---------     --------        -----   ----                    -------------   --------        ------                  -------
  2m            2m              1       {job-controller }                       Normal          SuccessfulCreate        Created pod: countdown-lc80g
```

And to see the output of the job via the pod it supervised, execute:

```bash
kubectl logs countdown-lc80g
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

[Previous](/logging) | [Next](/nodes)
