# Kubernetes Cheat Sheet

## Finding out Information

kubectl get services
kubectl get services --all-namespaces
kubectl get all --all-namespaces
kubectl get secrets

kubectl get all --all-namespaces | grep calico-kube-controllers


**Namespaces & Contexts**
 ```kubectl get namspaces```

We don't have a dev and prod namespace

 student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ kubectl get namespaces
NAME              STATUS   AGE
default           Active   13d
kube-node-lease   Active   13d
kube-public       Active   13d
kube-system       Active   13d
 
 Get the contexts

``` kubectl config get-contexts ```

student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ kubectl config get-contexts
CURRENT   NAME                       CLUSTER                    AUTHINFO   NAMESPACE
*         kubernetes-the-alta3-way   kubernetes-the-alta3-way   admin      default
          prod-context               kubernetes-the-alta3-way   admin      prod
          test-context               kubernetes-the-alta3-way   admin      test

Set the context
```kubectl config set-context dev-context --namespace=dev```

Use the context
```kubectl config use-context dev-context```
```kubectl config kubernetest-the-alta3-way```

**Deployments**
 kubectl get deployments

 **Pods**
 kubectl get pods --all-namespaces -o wide