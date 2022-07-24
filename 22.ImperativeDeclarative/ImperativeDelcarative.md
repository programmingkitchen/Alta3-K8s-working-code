# Imperative vs. Declarative Management

kubectl get -f <filename>.<yaml|json> -o yaml


Before creating any more pods, let's clean up our environment. Delete all of the current pods and deployments. NOTE: The --all flag will not cause pods and deployments to be deleted from the current namespace. Do not confuse this with the --all-namespaces flag, which would target all namespaces except kube-system, kube-node-lease or kube-public.



kubectl delete pods,deploy --all

student@bchd:~/my-repos/Alta3-K8s-working-code$ kubectl get pods --all-namespaces
NAMESPACE     NAME                                       READY   STATUS    RESTARTS   AGE
default       my-nginx-cf54cdbf7-2cbjd                   1/1     Running   0          52m
default       my-nginx-cf54cdbf7-xgzxx                   1/1     Running   0          52m
default       portforwardpod                             1/1     Running   0          61m
default       simpleservice                              1/1     Running   0          89m
kube-system   calico-kube-controllers-659fb845bc-hkx7b   1/1     Running   0          13d
kube-system   calico-node-7cnhv                          1/1     Running   0          13d
kube-system   calico-node-jzmm4                          1/1     Running   0          13d
kube-system   kube-dns-688c69f57d-2szjw                  3/3     Running   0          13d
student@bchd:~/my-repos/Alta3-K8s-working-code$ 

student@bchd:~/my-repos/Alta3-K8s-working-code$ kubectl get deployments --all-namespaces
NAMESPACE     NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
default       my-nginx                  2/2     2            2           53m
kube-system   calico-kube-controllers   1/1     1            1           13d
kube-system   kube-dns                  1/1     1            1           13d
student@bchd:~/my-repos/Alta3-K8s-working-code$ 

student@bchd:~/my-repos/Alta3-K8s-working-code$ kubectl get pods --all-namespaces
NAMESPACE     NAME                                       READY   STATUS    RESTARTS   AGE
default       my-nginx-cf54cdbf7-2cbjd                   1/1     Running   0          52m
default       my-nginx-cf54cdbf7-xgzxx                   1/1     Running   0          52m
default       portforwardpod                             1/1     Running   0          61m
default       simpleservice                              1/1     Running   0          89m
kube-system   calico-kube-controllers-659fb845bc-hkx7b   1/1     Running   0          13d
kube-system   calico-node-7cnhv                          1/1     Running   0          13d
kube-system   calico-node-jzmm4                          1/1     Running   0          13d
kube-system   kube-dns-688c69f57d-2szjw                  3/3     Running   0          13d

student@bchd:~/my-repos/Alta3-K8s-working-code$ kubectl get deployments --all-namespaces
NAMESPACE     NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
default       my-nginx                  2/2     2            2           53m
kube-system   calico-kube-controllers   1/1     1            1           13d
kube-system   kube-dns                  1/1     1            1           13d

student@bchd:~/my-repos/Alta3-K8s-working-code$ kubectl delete pods,deploy --all
pod "my-nginx-cf54cdbf7-2cbjd" deleted
pod "my-nginx-cf54cdbf7-xgzxx" deleted
pod "portforwardpod" deleted
pod "simpleservice" deleted
deployment.apps "my-nginx" deleted

student@bchd:~/my-repos/Alta3-K8s-working-code$ kubectl get pods --all-namespaces
NAMESPACE     NAME                                       READY   STATUS    RESTARTS   AGE
kube-system   calico-kube-controllers-659fb845bc-hkx7b   1/1     Running   0          13d
kube-system   calico-node-7cnhv                          1/1     Running   0          13d
kube-system   calico-node-jzmm4                          1/1     Running   0          13d
kube-system   kube-dns-688c69f57d-2szjw                  3/3     Running   0          13d

student@bchd:~/my-repos/Alta3-K8s-working-code$ kubectl get deployments --all-namespaces
NAMESPACE     NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
kube-system   calico-kube-controllers   1/1     1            1           13d
kube-system   kube-dns                  1/1     1            1           13d
student@bchd:~/my-repos/Alta3-K8s-working-code$ 



student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl create -f nginx2.yaml 
pod/nginx created
student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl create -f nginx2.yaml 
Error from server (AlreadyExists): error when creating "nginx2.yaml": pods "nginx" already exists
student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ 



---
apiVersion: v1
kind: Pod  # this is the object to be created by k8s
metadata:
  name: nginx    # name of our pod
  labels:
     one: is-the-loneliest-number    # this is a label key:value
spec:
  containers:
  - name: nginx  # name of a container
    image: nginx:1.18.0     # name and version of image to use
    ports:
    - containerPort: 80

student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl get pods
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          43s
student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ 



---
apiVersion: v1
kind: Pod  # this is the object to be created by k8s
metadata:
  name: nginx    # name of our pod
  labels:
     one: is-the-loneliest-number    # this is a label key:value
spec:
  containers:
  - name: nginx  # name of a container
    image: nginx:1.18.0     # name and version of image to use
    ports:
    - containerPort: 80

student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl get pods
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          43s
student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ 



1 # Please edit the object below. Lines beginning with a '#' will be ignored,
  2 # and an empty file will abort the edit. If an error occurs while saving this file will be
  3 # reopened with the relevant failures.
  4 #
  5 apiVersion: v1
  6 kind: Pod
  7 metadata:
  8   creationTimestamp: "2022-07-24T18:00:31Z"
  9   labels:
 10     one: is-the-loneliest-number
 11   name: nginx
 12   namespace: default
 13   resourceVersion: "1544636"
 14   selfLink: /api/v1/namespaces/default/pods/nginx
 15   uid: 0161d729-f878-4efe-8425-b710386a7193
 16 spec:
 17   containers:
 18   - image: nginx:1.18.0
 19     imagePullPolicy: IfNotPresent
 20     name: nginx
 21     ports:
 22     - containerPort: 80
 23       protocol: TCP
 24     resources: {}
 25     terminationMessagePath: /dev/termination-log
 26     terminationMessagePolicy: File
 27     volumeMounts:
 28     - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
 29       name: kube-api-access-tlhmm
 30       readOnly: true
 31   dnsPolicy: ClusterFirst
 32   enableServiceLinks: true
 33   nodeName: node-2
 34   preemptionPolicy: PreemptLowerPriority
 35   priority: 0
 36   restartPolicy: Always
 37   schedulerName: default-scheduler
 38   securityContext: {}
 39   serviceAccount: default
 40   serviceAccountName: default
 41   terminationGracePeriodSeconds: 30
 42   tolerations:
 43   - effect: NoExecute
 44     key: node.kubernetes.io/not-ready
 45     operator: Exists
 46     tolerationSeconds: 300
 47   - effect: NoExecute
 48     key: node.kubernetes.io/unreachable
 49     operator: Exists
 50     tolerationSeconds: 300
 51   volumes:
 52   - name: kube-api-access-tlhmm
 53     projected:
 54       defaultMode: 420
 55       sources:
 56       - serviceAccountToken:
 57           expirationSeconds: 3607
 58           path: token
 59       - configMap:
 60           items:
 61           - key: ca.crt
 62             path: ca.crt
 63           name: kube-root-ca.crt
 64       - downwardAPI:
 65           items:
 66           - fieldRef:
 67               apiVersion: v1
 68               fieldPath: metadata.namespace
 69             path: namespace
 70 status:
 71   conditions:
 72   - lastProbeTime: null
 73     lastTransitionTime: "2022-07-24T18:00:31Z"
 74     status: "True"
 75     type: Initialized
 76   - lastProbeTime: null
 77     lastTransitionTime: "2022-07-24T18:00:37Z"
 78     status: "True"
 79     type: Ready
 80   - lastProbeTime: null
 81     lastTransitionTime: "2022-07-24T18:00:37Z"
 82     status: "True"
 83     type: ContainersReady
 84   - lastProbeTime: null
 85     lastTransitionTime: "2022-07-24T18:00:31Z"
 86     status: "True"
 87     type: PodScheduled
 88   containerStatuses:
 89   - containerID: containerd://036f88ef9942d358af3f07ae34cdd282303d3876f30881a850af15ee8a3c48a5
 90     image: docker.io/library/nginx:1.18.0
 91     imageID: docker.io/library/nginx@sha256:e90ac5331fe095cea01b121a3627174b2e33e06e83720e9a934c7b8ccc9c55a0
 92     lastState: {}
 93     name: nginx
 94     ready: true
 95     restartCount: 0
 96     started: true
 97     state:
 98       running:
 99         startedAt: "2022-07-24T18:00:36Z"
100   hostIP: 10.9.227.239
101   phase: Running
102   podIP: 192.168.247.11
103   podIPs:
104   - ip: 192.168.247.11
105   qosClass: BestEffort
106   startTime: "2022-07-24T18:00:31Z"


student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl delete pod nginx
pod "nginx" deleted
student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ 

student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl delete pod nginx
pod "nginx" deleted

student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl apply -f nginx2.yaml 
pod/nginx created

student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl apply -f nginx2.yaml 
pod/nginx unchanged


student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl get pods
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          22s
student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ 


student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl get pods --show-labels
NAME    READY   STATUS    RESTARTS   AGE    LABELS
nginx   1/1     Running   0          3m1s   one=is-the-loneliest-number
student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ 


WHAT IF WE DON'T EDIT TEH ACTUAL FILE BUT TRY TO APPLY A CHANGE TO A NEW FILE WITH THE SAME NAMES 
AND ONLY THE LABELS CHANGE

OR DO WE HAVE TO EDIT THE SAME FILE.  

**WE CAN HAVE MULTIPLE FILES**

student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl apply -f nginx2.yaml 
pod/nginx created

student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl delete -f nginx2.yaml ^C

student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl get pods --show-labels
NAME    READY   STATUS    RESTARTS   AGE   LABELS
nginx   1/1     Running   0          32s   one=is-the-loneliest-number

student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl apply -f nginx3.yaml 
pod/nginx configured

student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl get pods --show-labels
NAME    READY   STATUS    RESTARTS   AGE   LABELS
nginx   1/1     Running   0          54s   one=is-the-loneliest-number,two=can-be-as-bad-as-one
student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ 



## Using the run command

Run is imperative, no history of anything that was used to create it. 

student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl run pollywog --image=nginx:1.18.0  --port=80
pod/pollywog created

student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl get pods
NAME       READY   STATUS    RESTARTS   AGE
pollywog   1/1     Running   0          4s

student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl get deployments
No resources found in default namespace.
student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ 


kubectl run pollywog --image=nginx:1.18.0  --port=80

kubectl edit pods pollywog



student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl delete pod pollywog 
pod "pollywog" deleted
student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl get pods
No resources found in default namespace.
student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ 


---
apiVersion: v1
kind: Pod
metadata:
  name: nginx2   # update this value
spec:
  containers:
  - name: nginx2
    image: nginx:1.18.0
    ports:
    - containerPort: 80

kubectl get pods --show-labels
kubectl get pods -o wide



student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl apply -f nginx4.yaml
pod/nginx2 created

student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl get pods
NAME     READY   STATUS    RESTARTS   AGE
nginx2   1/1     Running   0          12s

student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl get pods -o wide
NAME     READY   STATUS    RESTARTS   AGE   IP               NODE     NOMINATED NODE   READINESS GATES
nginx2   1/1     Running   0          35s   192.168.247.18   node-2   <none>           <none>

student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl get pods --show-labels
NAME     READY   STATUS    RESTARTS   AGE   LABELS
nginx2   1/1     Running   0          84s   <none>
student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ 

student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl get pods nginx2 -o yaml | grep nodeName
  nodeName: node-2
student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ 

student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl get pods
NAME     READY   STATUS    RESTARTS   AGE
nginx2   1/1     Running   0          5m44s
student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ kubectl delete pods --all
pod "nginx2" deleted
student@bchd:~/my-repos/Alta3-K8s-working-code/22.ImperativeDeclarative$ 












