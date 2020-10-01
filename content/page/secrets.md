+++
title = "Secrets"
subtitle = "Kubernetes secrets by example"
date = "2019-02-28"
url = "/secrets/"
+++

You don't want sensitive information such as a database password or an
API key kept around in clear text. Secrets provide you with a mechanism
to use such information in a safe and reliable way with the following properties:

- Secrets are namespaced objects, that is, exist in the context of a namespace
- You can access them via a volume or an environment variable from a container running in a pod
- The secret data on nodes is stored in [tmpfs](https://www.kernel.org/doc/Documentation/filesystems/tmpfs.txt) volumes
- A per-secret size limit of 1MB exists
- The API server stores secrets as plaintext in etcd

Let's create a secret `apikey` that holds a (made-up) API key:

```bash
echo -n "A19fh68B001j" > ./apikey.txt
```
```bash
kubectl create secret generic apikey --from-file=./apikey.txt
```
```cat
secret "apikey" created
```
```bash
kubectl describe secrets/apikey
```
```cat
Name:           apikey
Namespace:      default
Labels:         <none>
Annotations:    <none>

Type:   Opaque

Data
====
apikey.txt:     12 bytes
```

Now let's use the secret in a [pod](https://github.com/openshift-evangelists/kbe/blob/main/specs/secrets/pod.yaml)
via a [volume](/volumes/):


```bash
kubectl apply -f https://raw.githubusercontent.com/openshift-evangelists/kbe/main/specs/secrets/pod.yaml
```

If we now exec into the container we see the secret mounted at `/tmp/apikey`:

```bash
kubectl exec -it consumesec -c shell -- bash
```
```bash
mount | grep apikey
```
```cat
tmpfs on /tmp/apikey type tmpfs (ro,relatime)
```
```bash
cat /tmp/apikey/apikey.txt
```
```cat
A19fh68B001j
```
return
```bash
exit
```

Note that for service accounts Kubernetes automatically creates secrets containing
credentials for accessing the API and modifies your pods to use this type of secret.

You can remove both the pod and the secret with:

```bash
kubectl delete pod/consumesec secret/apikey
```

[Previous](/volumes) | [Next](/logging)
