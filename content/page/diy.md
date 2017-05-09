+++
title = "DIY"
subtitle = "Try out for yourself"
date = "2017-04-24"
url = "/diy/"
+++

If you want to try out the examples here yourself, you can use the same setup
I've got locally running on my machine:

1. Install [Minishift](https://docs.openshift.org/latest/minishift/getting-started/installing.html)
1. Install [oc](https://docs.openshift.org/latest/cli_reference/get_started_cli.html#installing-the-cli)
1. Run `minishift start`
1. Log in using `oc login -u system:admin` with password `admin`
1. Create a symlink from `oc` like so:  `ln -s oc kubectl`

All examples have been carried out with the following version, that is, [oc 1.4.1](https://github.com/openshift/origin/releases/tag/v1.4.1) and [Minishift 1.0.0-rc.1](https://github.com/minishift/minishift/releases/tag/v1.0.0-rc.1) (i.e., Kubernetes 1.5):

```bash
$ minishift version
Minishift version: 1.0.0-rc.1

$ kubectl version
Client Version: version.Info{Major:"1", Minor:"4", GitVersion:"v1.4.0+776c994", GitCommit:"a9e9cf3", GitTreeState:"clean", BuildDate:"2017-01-24T15:12:46Z", GoVersion:"go1.7.4", Compiler:"gc", Platform:"darwin/amd64"}
Server Version: version.Info{Major:"1", Minor:"5", GitVersion:"v1.5.2+43a9be4", GitCommit:"43a9be4", GitTreeState:"clean", BuildDate:"2017-03-09T19:51:29Z", GoVersion:"go1.7.4", Compiler:"gc", Platform:"linux/amd64"}
```
