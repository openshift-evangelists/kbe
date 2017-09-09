+++
title = "DIY"
subtitle = "Try out for yourself"
date = "2017-04-24"
url = "/diy/"
+++

If you want to try out the examples here yourself, you can use the same setup
I've got locally running on my machine:

1. Install [Minishift](https://docs.openshift.org/latest/minishift/getting-started/installing.html)
1. Run `minishift start`
1. Configure OpenShift Client binary (`oc`) by running `eval $(minishift oc-env)`
1. Log in using `oc login -u system:admin` with password `admin`
1. Create a symlink from `oc` like so:  `ln -s $(which oc) /usr/local/bin/kubectl`

All examples have been carried out with Minishift version [v1.7.0](https://github.com/minishift/minishift/releases/tag/v1.7.0) and OpenShift Client Binary (`oc`) version [v3.6.0](https://github.com/openshift/origin/releases/tag/v3.6.0) (i.e. Kubernetes [v1.6.1](https://github.com/kubernetes/kubernetes/releases/tag/v1.6.1))

```bash
$ minishift version
minishift v1.7.0+15491358

$ kubectl version
Client Version: version.Info{Major:"1", Minor:"6", GitVersion:"v1.6.1+5115d708d7", GitCommit:"fff65cf", GitTreeState:"clean", BuildDate:"2017-07-30T21:47:33Z", GoVersion:"go1.7.6", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"6", GitVersion:"v1.6.1+5115d708d7", GitCommit:"fff65cf", GitTreeState:"clean", BuildDate:"2017-08-01T06:24:02Z", GoVersion:"go1.7.6", Compiler:"gc", Platform:"linux/amd64"}
```
