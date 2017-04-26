+++
title = "Secrets"
subtitle = "Kubernetes secrets by example"
date = "2017-04-26"
url = "/secrets/"
+++

You don't want sensitive information such as a database password or an
API key keep around in clear text. Secrets provide you with a mechanism
to use such information in a safe and reliable way.

Let's create a secret `apikey` that holds a (made-up) API key:

```bash
$ echo -n "A19fh68B001j" > ./apikey.txt

$ kubectl create secret generic apikey --from-file=./apikey.txt
secret "apikey" created

$ kubectl describe secrets/apikey
Name:           apikey
Namespace:      default
Labels:         <none>
Annotations:    <none>

Type:   Opaque

Data
====
apikey.txt:     12 bytes
```

Now let's and use it in a [pod](https://github.com/mhausenblas/kbe/blob/master/specs/secrets/pod.yaml):

```bash
$ kubectl create -f https://raw.githubusercontent.com/mhausenblas/kbe/master/specs/secrets/pod.yaml
```

If we now exec into the container we see the secret mounted at `/tmp/apikey`:

```
$ kubectl exec consumesec -c shell -i -t -- bash
[root@consumesec /]# mount | grep apikey
tmpfs on /tmp/apikey type tmpfs (ro,relatime)
[root@consumesec /]# cat /tmp/apikey/apikey.txt
A19fh68B001j
```
