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
1. Install [oc](https://docs.openshift.org/latest/cli_reference/get_started_cli.html#installing-the-cli)
1. Log in using `oc login -u system:admin` with password `admin`
1. Create a symlink like so:  `ln -s oc kubectl`
