# PodSecurityPolicy

## Setup

### TL;DR

```
kubectl delete clusterrole eks:podsecuritypolicy:privileged
kubectl delete clusterrolebinding eks:podsecuritypolicy:authenticated
kubectl apply -f awsnode.yaml
kubectl apply -f coredns.yaml
kubectl apply -f kubeproxy.yaml
kubectl apply -f ingress.yaml
kubectl apply -f restricted.yaml
kubectl delete psp eks.privileged
```

For better understanding, don't run this, instead, continue reading:

### Delete CR/CRB for Privileged

By default, EKS authenticated users have too much privileges. To delete:

```
kubectl delete clusterrole eks:podsecuritypolicy:privileged
kubectl delete clusterrolebinding eks:podsecuritypolicy:authenticated
```

### Create PSP/R/RB for EKS Related Pods

To install:

```
kubectl apply -f awsnode.yaml
kubectl apply -f coredns.yaml
kubectl apply -f kubeproxy.yaml
```

### Crete PSP/CR/CRB for Authenticated Users

For other pods or authenticated users, only restricted containers are allowed, in any namespace.

```
kubectl apply -f restricted.yaml
```

### Create PSP/R/RB for Ingress

Ingress controller needs diffrent sets of policies than, say, coredns:

```
kubectl apply -f ingress.yaml
```

### Delete Default PSP and CR/CRB

Finally, delete the EKS default PSP. Don't do this before other steps are done; otherwise you could not create pods when there is no PSP.

```
kubectl delete psp eks.privileged
```

## Roll Back to EKS Default PSP

In case you want to restore default (eks.privileged) policy, see:

https://docs.aws.amazon.com/eks/latest/userguide/pod-security-policy.html

## Testing

You can use `test.yaml` to test PSP. There are two images, one runs as root (USER 0), the other runs as user 1000.

## Todo

Another privileged policy but restricted to namespace only, for example, for kube-system (or ingress namespace if they need).
