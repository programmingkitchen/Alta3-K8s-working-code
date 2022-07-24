# Create and Configure Pods

**Description**
You have already launched Pods, however, you have yet to actually write any Pod Manifests. 
These Pod manifests are what declaratively define all of the information necessary 
to launch a Pod. The kubectl command is used to create many different 
Kubernetes resource objects, not just Pods.




student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ ls
CreateConfigurePods.md

student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ ls ../setup/create-and-configure-basic-pods/
setup.sh  teardown.sh  test.sh

student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ cp -rp  ../setup/create-and-configure-basic-pods/ .

student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ ls -l
total 8
-rw-rw-r-- 1 student student  339 Jul 24 13:53 CreateConfigurePods.md
drwxrwxr-x 2 student student 4096 Jul  7 15:06 create-and-configure-basic-pods
student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ 

student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods/create-and-configure-basic-pods$ cat setup.sh 
#!/bin/bash
set -euo pipefail

**This also starts a log**

There is a weird teardown log created with lots of files.
student@bchd:/tmp/teardown.create-and-configure-basic-pods.gZ7n$


kubectl config use-context kubernetes-the-alta3-way

student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods/create-and-configure-basic-pods$ setup create-and-configure-basic-pods
[+] Cleaning cluster
[+] Preparing create-and-configure-basic-pods
[+] Setup complete
student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods/create-and-configure-basic-pods$ 


student@bchd:/tmp$ cat 182307e628f_create-and-configure-basic-pods_setup.log
Switched to context "kubernetes-the-alta3-way".
student@bchd:/tmp$ 

student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ kubectl config get-contexts
CURRENT   NAME                       CLUSTER                    AUTHINFO   NAMESPACE
*         kubernetes-the-alta3-way   kubernetes-the-alta3-way   admin      default
          prod-context               kubernetes-the-alta3-way   admin      prod
          test-context               kubernetes-the-alta3-way   admin      test
student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ 

This is what setup does.  There are no changes to running the initial steps.
student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods/create-and-configure-basic-pods$ cat setup.sh 
#!/bin/bash
set -euo pipefail

kubectl config use-context kubernetes-the-alta3-way


student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ kubectl config use-context kubernetes-the-alta3-way
Switched to context "kubernetes-the-alta3-way".
student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ kubectl config get-contexts
CURRENT   NAME                       CLUSTER                    AUTHINFO   NAMESPACE
*         kubernetes-the-alta3-way   kubernetes-the-alta3-way   admin      default
          prod-context               kubernetes-the-alta3-way   admin      prod
          test-context               kubernetes-the-alta3-way   admin      test
student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ 

A pod manifest must contain certain keys. Since this is YAML, these keys can be in any order, but the classic approach is alphabetical order- this approach is convenient because alphabetical order makes sense for reading the values.

apiVersion:
kind:
metadata:
spec:

apiVersion: is a required key field. It defines the Kubernetes API you are using.
kind: For this lab, the value field will be either Pod or Deployment.
metadata: A required key that helps uniquely identify the object. Subkeys are name, uid, labels, and namespace.
spec: Specifies the desired state of an object. If a spec is deleted, the object will be purged from the system.
status: PURELY FYI - A system generated value that shows the actual state of the object at the current time.

kubectl api-resources

## Simple Service Empty

vim ~/mycode/yaml/simpleservice-empty.yaml


student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ kubectl apply -f simpleservice.yaml 
pod/simpleservice created
student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ 

student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ kubectl get pod
NAME            READY   STATUS    RESTARTS   AGE
simpleservice   1/1     Running   0          75s
student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ 

student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ kubectl describe pod simpleservice 
Name:         simpleservice
Namespace:    default
Priority:     0
Node:         node-2/10.9.227.239
Start Time:   Sun, 24 Jul 2022 14:22:50 +0000
Labels:       name=simpleservice-web
Annotations:  <none>
Status:       Running
IP:           192.168.247.5
IPs:
  IP:  192.168.247.5
Containers:
  simpleservice-web:
    Container ID:   containerd://7eebbfc697182eb08fd754eb2ff78556e7026e11c3e16a1261e3d28b82d6b4c5
    Image:          mhausenblas/simpleservice:0.5.0
    Image ID:       docker.io/mhausenblas/simpleservice@sha256:33f589ea71cc8a0e37a2f4b17c2e3c207a69c2df525aa62ef5451b75638fd966
    Port:           9876/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Sun, 24 Jul 2022 14:23:10 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-hcvrf (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-hcvrf:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  106s  default-scheduler  Successfully assigned default/simpleservice to node-2
  Normal  Pulling    106s  kubelet            Pulling image "mhausenblas/simpleservice:0.5.0"
  Normal  Pulled     86s   kubelet            Successfully pulled image "mhausenblas/simpleservice:0.5.0" in 19.926340445s
  Normal  Created    86s   kubelet            Created container simpleservice-web
  Normal  Started    86s   kubelet            Started container simpleservice-web
student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ 


student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ kubectl apply -f webby.yaml 
pod/myweb01 created
student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ 


student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ kubectl get pods
NAME            READY   STATUS    RESTARTS   AGE
myweb01         1/1     Running   0          27s
simpleservice   1/1     Running   0          72m
student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ 


student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ kubectl get pods
NAME            READY   STATUS    RESTARTS   AGE
myweb01         1/1     Running   0          2m8s
simpleservice   1/1     Running   0          74m
student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ kubectl delete -f webby.yaml 
pod "myweb01" deleted
student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ kubectl delete -f simpleservice.yaml 
pod "simpleservice" deleted

student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ 
student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ kubectl get pods
No resources found in default namespace.
student@bchd:~/my-repos/Alta3-K8s-working-code/18.CreateConfigurePods$ 
