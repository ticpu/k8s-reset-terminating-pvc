# This project is a fork of k8s-reset-terminating-pv

This project has been adapted from [k8s-reset-terminating-pv](https://github.com/jianz/k8s-reset-terminating-pv) to work on persistent volume claims (pvc) instead of pv.

# k8s-reset-terminating-pvc

Reset persistent volume claim status from terminating back to bound. [Here are the details](https://jianz.github.io/posts/2020-08-25-reset-pv).

## Purpose

When delete a kubernetes persistent volume claim by accident, it may stuck in the terminating status due to `kubernetes.io/pv-protection` finalizer prevent it from being deleted. You can use this tool to reset its status back to bound.

## Installing

```shell
git clone git@github.com:ticpu/k8s-reset-terminating-pvc.git
cd k8s-reset-terminating-pvc
go build -o resetpvc
```

## Usage

```text
Usage:
  resetpvc [flags] <namespace/persistent volume claim name>

Flags:
      --etcd-ca        string   CA Certificate used by etcd (default "ca.crt")
      --etcd-cert      string   Public key used by etcd (default "etcd.crt")
      --etcd-key       string   Private key used by etcd (default "etcd.key")
      --etcd-host      string   The etcd domain name or IP (default "localhost")
      --etcd-port      int      The etcd port number (default 2379)
      --k8s-key-prefix string   The etcd key prefix for kubernetes resources. (default "registry")
  -h, --help                    help for resetpvc
```

For simplicity, you can name the etcd certificate ca.crt, etcd.crt, etcd.key, and put them in the same directory as the tool(resetpvc).

The tool by default connect to etcd using `localhost:2379`. You can forward the etcd port on the pod to the localhost:

```shell
kubectl port-forward pods/etcd-member-master0 2379:2379 -n etcd
```

`--k8s-key-prefix`: Default set to `registry` for the community version of kubernetes as it uses `/registry` as etcd key prefix, the key for persistent volume pv1 is `/registry/persistentvolumes/pv1`. Set to `kubernetes.io` for OpenShift as it uses `/kubernetes.io` as prefix and the key for pv1 is `/kubernetes.io/persistentvolumes/pv1`.

Example:

```shell
./resetpvc  my-namespace/my-pvc
```

## License

k8s-reset-terminating-pvc is released under the MIT license.
