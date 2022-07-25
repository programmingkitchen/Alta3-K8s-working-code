# LivenessProbes and ReadinessProbes

Then this lab will teach how to configure containers to use LivenessProbes and ReadinessProbes and explore their use cases.

If a node isn't ready, there may be a problem with it. Nodes labeled as "ready" can accept pod deployment. However, if a node comes up as "not ready," there's the possibility of a problem... or the node simply hasn't finished initializing.

Similarly, containers can have ReadinessProbes inserted into them. This will help services determine if it should start to use the Pod or if it has to wait until all the containers inside of the Pod have become ready.

Also, LivenessProbes can be placed into containers as well. These are intended to assure that a container is still performing its essential tasks. If they are deemed not to be alive anymore, even though the container itself is still running, the LivenessProbe will cause a reboot of the container.


---
apiVersion: v1
kind: Pod
metadata:
  name: sise-lp
spec:
  containers:
  - name: sise
    image: mhausenblas/simpleservice:0.5.0
    ports:
    - containerPort: 9876
    livenessProbe:
      initialDelaySeconds: 2   # wait this long after restarting to do the first HTTP GET (default 0)
      periodSeconds: 5         # how often to probe (default is 1)
      timeoutSeconds: 1        # how long to wait until timeout occurs (default is 1)
      successThreshold: 1      # min consecutive successes to be considered "up" after a fail (default 1)
      failureThreshold: 3      # how many consecutive fails before pod is restarted (default 3)
      httpGet:                 # send HTTP GET to :9876/health/
        path: /health
        port: 9876

student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ kubectl apply -f sise-lp.yaml 
pod/sise-lp created
student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ kubectl get pods
NAME      READY   STATUS    RESTARTS   AGE
sise-lp   1/1     Running   0          15s
student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ 

student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ kubectl describe pods sise-lp | grep -i health
    Liveness:       http-get http://:9876/health delay=2s timeout=1s period=5s #success=1 #failure=3
student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ 

student@bchd:~$ kubectl port-forward sise-lp 2224:9876 --address=0.0.0.0
Forwarding from 0.0.0.0:2224 -> 9876
Handling connection for 2224

student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ curl localhost:2224/health
{"healthy": true}
student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ 

https://github.com/mhausenblas/simpleservice

Before moving further, review https://github.com/mhausenblas/simpleservice. Pay special attention to the section that reads as follows, 'HEALTH_MIN and HEALTH_MAX ... the min. and max. delay in milliseconds that the /health endpoint responds'


Also, take a look at the k8s documentation for liveness and readiness probes. This link has all of the settings available: https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/#configure-probes.


---
apiVersion: v1
kind: Pod       # resource to create
metadata:
  name: badpod  # name of pod to create
spec:
  containers:
  - name: sise  # name of container to create
    image: mhausenblas/simpleservice:0.5.0   # image from hub.docker.com
    ports:
    - containerPort: 9876
    env:                # injecting ENV vars to the image simpleservice causes behavior changes
    - name: HEALTH_MIN  #  the min. and max. delay in milliseconds that the /health endpoint responds
      value: "2000"     # in ms (wait a min of 2 seconds before responding to GET)
    - name: HEALTH_MAX  #  the min. and max. delay in milliseconds that the /health endpoint responds
      value: "4000"     # in ms (wait a max of 4 seconds before responding to GET)
    livenessProbe:
      initialDelaySeconds: 2   # wait this long after restarting to do the first HTTP GET (default 0)
      periodSeconds: 5         # how often to probe (default is 1)
      timeoutSeconds: 1        # how long to wait until timeout occurs (default is 1)
      successThreshold: 1      # min consecutive successes to be considered "up" after a fail (default 1)
      failureThreshold: 3      # how many consecutive fails before pod is restarted (default 3)
      httpGet:                 # send HTTP GET to :9876/health/
        path: /health
        port: 9876

student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ kubectl apply -f badpod.yaml
pod/badpod created
student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ 

Restart count of badpod


student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ kubectl get pods
NAME      READY   STATUS    RESTARTS      AGE
badpod    1/1     Running   1 (11s ago)   57s
sise-lp   1/1     Running   0             35m
student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ kubectl get pods
NAME      READY   STATUS    RESTARTS      AGE
badpod    1/1     Running   1 (46s ago)   92s
sise-lp   1/1     Running   0             36m
student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ kubectl get pods
NAME      READY   STATUS    RESTARTS      AGE
badpod    1/1     Running   2 (12s ago)   103s
sise-lp   1/1     Running   0             36m
student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ 

student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ grep HEALTH describe-badpod.txt 
      HEALTH_MIN:  2000
      HEALTH_MAX:  4000
student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ 


END STEP 14

Of course, a liveness probe on its own is not enough. We need more probes! Readiness probes describe when a container is ready to serve user requests. Containers that fail readiness checks are assumed to be booting and are removed from service load balancers. They are configured similarly to liveness probes.


---
apiVersion: v1
kind: Pod             # create this kind of resource
metadata:
  name: sise-rp         # name of the Pod (siMPLE seRVICE rEADINESSpROBE)
spec:
  containers:
  - name: sise   # name of the container
    image: mhausenblas/simpleservice:0.5.0
    ports:
    - name: web
      protocol: TCP
      containerPort: 9876     # port the container is listening on
    readinessProbe:
      initialDelaySeconds: 10   # wait this long after restarting to do the first HTTP GET (default 0)
      periodSeconds: 3          # how often to probe (default is 1)
      timeoutSeconds: 2         # how long to wait until timeout occurs (default is 1)
      successThreshold: 1       # min consecutive successes to be considered "up" after a fail (default 1)
      failureThreshold: 3       # how many consecutive fails before pod is restarted (default 3)
      httpGet:                  # use HTTP GET to test :9876/health/
        path: /health
        port: 9876


student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ kubectl get pods
NAME      READY   STATUS    RESTARTS        AGE
badpod    1/1     Running   187 (16s ago)   10h
sise-lp   1/1     Running   0               10h
sise-rp   1/1     Running   0               68s
student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ kubectl describe pods sise-rp | grep -i health
    Readiness:      http-get http://:9876/health delay=10s timeout=2s period=3s #success=1 #failure=3
student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ 


Simple Service 2

apiVersion: v1
kind: Pod
metadata:
  name: simpleservice2
  labels:
    name: simpleservice2
spec:
  containers:
  - name: simpleservice-web
    image: mhausenblas/simpleservice:0.5.0
    ports:
    - name: web
      containerPort: 9876
      protocol: TCP
    readinessProbe:
      periodSeconds: 2
      failureThreshold: 3
      successThreshold: 1
      httpGet:
        path: /health
        port: 9876

student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ kubectl get pods
NAME             READY   STATUS             RESTARTS        AGE
badpod           0/1     CrashLoopBackOff   189 (42s ago)   10h
simpleservice2   1/1     Running            0               60s
sise-lp          1/1     Running            0               10h
sise-rp          1/1     Running            0               8m59s
student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ 

student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ kubectl get pods
NAME             READY   STATUS             RESTARTS         AGE
badpod           0/1     CrashLoopBackOff   201 (5m7s ago)   10h
simpleservice2   1/1     Running            0                44m
sise-lp          1/1     Running            0                11h
sise-rp          1/1     Running            0                52m
student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ 

student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ kubectl get pod simpleservice2
NAME             READY   STATUS    RESTARTS   AGE
simpleservice2   1/1     Running   0          45m
student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ 

Port Forward

student@bchd:~$ kubectl port-forward simpleservice2 2224:9876 --address=0.0.0.0
Forwarding from 0.0.0.0:2224 -> 9876
Handling connection for 2224
Handling connection for 2224
Handling connection for 2224

Liveness, the echo just adds an extra line


student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ curl -i http://127.0.0.1:2224/health;echo
HTTP/1.1 200 OK
Date: Mon, 25 Jul 2022 12:01:52 GMT
Content-Length: 17
Etag: "b40026a9bea9f5096f4ef55d3d23d6730139ff5e"
Content-Type: application/json
Server: TornadoServer/4.3

{"healthy": true}

student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ curl -i http://127.0.0.1:2224/health;
HTTP/1.1 200 OK
Date: Mon, 25 Jul 2022 12:02:07 GMT
Content-Length: 17
Etag: "b40026a9bea9f5096f4ef55d3d23d6730139ff5e"
Content-Type: application/json
Server: TornadoServer/4.3

{"healthy": true}
student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ curl -i http://127.0.0.1:2224/health
HTTP/1.1 200 OK
Date: Mon, 25 Jul 2022 12:02:20 GMT
Content-Length: 17
Etag: "b40026a9bea9f5096f4ef55d3d23d6730139ff5e"
Content-Type: application/json
Server: TornadoServer/4.3

{"healthy": true}
student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ 


Cleanup

kubectl delete pods --all

student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ kubectl get pods
NAME             READY   STATUS             RESTARTS          AGE
badpod           0/1     CrashLoopBackOff   203 (2m43s ago)   11h
simpleservice2   1/1     Running            0                 49m
sise-lp          1/1     Running            0                 11h
sise-rp          1/1     Running            0                 57m

student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ kubectl get deployments
No resources found in default namespace.

student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ kubectl delete pods --all
pod "badpod" deleted
pod "simpleservice2" deleted
pod "sise-lp" deleted
pod "sise-rp" deleted

student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ kubectl get pods
No resources found in default namespace.
student@bchd:~/my-repos/Alta3-K8s-working-code/26.ProbesHealth$ 


Path to health

 readinessProbe:
      periodSeconds: 2
      failureThreshold: 3
      successThreshold: 1
      httpGet:
        path: /health
        port: 9876






