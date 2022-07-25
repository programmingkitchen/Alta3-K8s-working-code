# Understanding Security Contexts for Cluster Access Control

apiVersion: v1
kind: Pod
metadata:
  name: secured-cont
spec:
  securityContext:
    fsGroup: 2000
  containers:
  - name: secured-container
    image: busybox:1.35.0
    command: [ "sh", "-c", "sleep 1h" ]
    securityContext:
      runAsUser: 1000
      runAsGroup: 3000
      allowPrivilegeEscalation: false
    volumeMounts:
    - name: secured-volume
      mountPath: /data/secured
  volumes:
    - name: secured-volume
      emptyDir: {}


student@bchd:~/my-repos/Alta3-K8s-working-code/28.SecurityContexts$ kubectl get pods
No resources found in default namespace.

student@bchd:~/my-repos/Alta3-K8s-working-code/28.SecurityContexts$ kubectl apply -f secured_content.yaml 
pod/secured-cont created

student@bchd:~/my-repos/Alta3-K8s-working-code/28.SecurityContexts$ kubectl get pods secured-cont 
NAME           READY   STATUS    RESTARTS   AGE
secured-cont   1/1     Running   0          6s
student@bchd:~/my-repos/Alta3-K8s-working-code/28.SecurityContexts$ 


Log into the container

kubectl exec -it secured-cont -- sh


student@bchd:~$ kubectl exec -it secured-cont -- sh
/ $ ps
PID   USER     TIME  COMMAND
    1 1000      0:00 sleep 1h
    7 1000      0:00 sh
   13 1000      0:00 ps
/ $ 

The security context

  securityContext:
      runAsUser: 1000
      runAsGroup: 3000
      allowPrivilegeEscalation: false



securityContext:
    fsGroup: 2000

     volumeMounts:
    - name: secured-volume
      mountPath: /data/secured


/ $ cd /data
/data $ ls
secured
/data $ ls -l
total 4
drwxrwsrwx    2 root     2000          4096 Jul 25 12:17 secured
/data $ 

securityContext:
    fsGroup: 2000

     volumeMounts:
    - name: secured-volume
      mountPath: /data/secured


Inspect the file. This will show you that the user ID is 1000 (set by the runAsUser field), and the group ID of 2000 (set by the fsGroup field).



/data/secured $ echo 'security verified' > security-test.txt
/data/secured $ ls -la
total 12
drwxrwsrwx    2 root     2000          4096 Jul 25 12:28 .
drwxr-xr-x    3 root     root          4096 Jul 25 12:17 ..
-rw-r--r--    1 1000     2000            18 Jul 25 12:28 security-test.txt
/data/secured $ 

To assure that this pod does not allow for any root access, the group ID (gid) needs to not be 0. Although we have set the fsGroup to 2000, if we would ignore the runAsGroup field, the process still would be able to interact with root group files. That is why we set the runAsGroup to 3000. Verify that this is the case by running the following command.

/data/secured $ id
uid=1000 gid=3000 groups=2000
/data/secured $ 


student@bchd:~$ kubectl delete pods --all
pod "secured-cont" deleted
student@bchd:~$ kubectl get pods
No resources found in default namespace.
student@bchd:~$ 


