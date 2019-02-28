+++
title = "DIY"
subtitle = "Try out for yourself"
date = "2019-02-28"
url = "/diy/"
+++

If you want to try out the examples here yourself, you can use the same setup
I've got locally running on my machine:

1. Install [Minishift](https://docs.okd.io/latest/minishift/getting-started/installing.html)
1. Run `minishift start`
1. Configure OpenShift Client binary (`oc`) by running `eval $(minishift oc-env)`
1. Log in using `oc login -u system:admin` with password `admin`
1. Create a symlink from `oc` like so:  `ln -s $(which oc) /usr/local/bin/kubectl`

All examples have been carried out with Minishift version [v1.32.0](https://github.com/minishift/minishift/releases/tag/v1.32.0):

```bash
$ minishift version
minishift v1.32.0+009893b

$ kubectl version --short
Client Version: v1.12.2
Server Version: v1.11.0+d4cacc0
```

Alternatively, you can try out the Developer Preview of [OpenShift 4](https://try.openshift.com/) using the examples here.
