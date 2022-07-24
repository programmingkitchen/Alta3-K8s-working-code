# Debugging via kubectl port-forward

**Simple Service**
 https://github.com/mhausenblas/simpleservice


 student@bchd:~/my-repos/Alta3-K8s-working-code/21.DebuggingViaKubectlPortFw$ kubectl config get-contexts
CURRENT   NAME                       CLUSTER                    AUTHINFO   NAMESPACE
*         kubernetes-the-alta3-way   kubernetes-the-alta3-way   admin      default
          prod-context               kubernetes-the-alta3-way   admin      prod
          test-context               kubernetes-the-alta3-way   admin      test
student@bchd:~/my-repos/Alta3-K8s-working-code/21.DebuggingViaKubectlPortFw$ kubectl config use-context kubernetes-the-alta3-way
Switched to context "kubernetes-the-alta3-way".
student@bchd:~/my-repos/Alta3-K8s-working-code/21.DebuggingViaKubectlPortFw$ kubectl config get-contexts
CURRENT   NAME                       CLUSTER                    AUTHINFO   NAMESPACE
*         kubernetes-the-alta3-way   kubernetes-the-alta3-way   admin      default
          prod-context               kubernetes-the-alta3-way   admin      prod
          test-context               kubernetes-the-alta3-way   admin      test
student@bchd:~/my-repos/Alta3-K8s-working-code/21.DebuggingViaKubectlPortFw$ 


Tmux Split Horizontal
CTRL B + '
CTRL B + Up/Down

student@bchd:~/my-repos/Alta3-K8s-working-code/21.DebuggingViaKubectlPortFw$ kubectl apply -f simpleservice.yaml 
pod/simpleservice created
student@bchd:~/my-repos/Alta3-K8s-working-code/21.DebuggingViaKubectlPortFw$ kubectl get pods
NAME            READY   STATUS    RESTARTS   AGE
simpleservice   1/1     Running   0          5s
student@bchd:~/my-repos/Alta3-K8s-working-code/21.DebuggingViaKubectlPortFw$ 

student@bchd:~$ kubectl port-forward simpleservice 2224:9876 --address=0.0.0.0
Forwarding from 0.0.0.0:2224 -> 9876


student@bchd:~/my-repos/Alta3-K8s-working-code/21.DebuggingViaKubectlPortFw$ curl http://localhost:2224/env
{"version": "0.5.0", "env": "{'LANG': 'C.UTF-8', 'KUBERNETES_PORT_443_TCP_PROTO': 'tcp', 'GPG_KEY': 'C01E1CAD5EA2C4F0B8E3571504C367C218ADD4FF', 'PYTHON_VERSION': '2.7.13', 'PYTHON_PIP_VERSION': '9.0.1', 'KUBERNETES_SERVICE_HOST': '172.16.3.1', 'HOSTNAME': 'simpleservice', 'KUBERNETES_SERVICE_PORT_HTTPS': '443', 'REFRESHED_AT': '2017-04-24T13:50', 'KUBERNETES_PORT_443_TCP_ADDR': '172.16.3.1', 'KUBERNETES_PORT': 'tcp://172.16.3.1:443', 'PATH': '/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin', 'KUBERNETES_PORT_443_TCP_PORT': '443', 'KUBERNETES_PORT_443_TCP': 'tcp://172.16.3.1:443', 'HOME': '/root', 'KUBERNETES_SERVICE_PORT': '443'}"}
student@bchd:~/my-repos/Alta3-K8s-working-code/21.DebuggingViaKubectlPortFw$ 

student@bchd:~/my-repos/Alta3-K8s-working-code/21.DebuggingViaKubectlPortFw$ curl http://localhost:2224/health
{"healthy": true}
student@bchd:~/my-repos/Alta3-K8s-working-code/21.DebuggingViaKubectlPortFw$ 



Quick and Dirty Nginx

What is the container port for nginx
80

2224 -> 80


kubectl run portforwardpod --image=nginx:1.19.6

kubectl port-forward portforwardpod 2224:80 --address=0.0.0.0


student@bchd:~$ kubectl get pods
NAME             READY   STATUS    RESTARTS   AGE
portforwardpod   1/1     Running   0          3m17s
simpleservice    1/1     Running   0          31m


student@bchd:~/my-repos/Alta3-K8s-working-code/top-deployments$ kubectl get pods
NAME                       READY   STATUS    RESTARTS   AGE
my-nginx-cf54cdbf7-2cbjd   1/1     Running   0          2m39s
my-nginx-cf54cdbf7-xgzxx   1/1     Running   0          2m39s
portforwardpod             1/1     Running   0          11m
simpleservice              1/1     Running   0          39m
student@bchd:~/my-repos/Alta3-K8s-working-code/top-deployments$ 



Run a port-forward to this Pod! curl the Pod to confirm it is working. You can use whatever localhost port you like, but you MUST use the correct container port. Hint: Google for nginx port numbers!


kubectl port-forward simpleservice 2224:9876 --address=0.0.0.0

kubectl port-forward portforwardpod 2224:80 --address=0.0.0.0


student@bchd:~/my-repos/Alta3-K8s-working-code/top-deployments$ curl http://localhost:2224
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

student@bchd:~/my-repos/Alta3-K8s-working-code/top-deployments$ 


student@bchd:~$ kubectl get pods
NAME                       READY   STATUS    RESTARTS   AGE
my-nginx-cf54cdbf7-2cbjd   1/1     Running   0          78s
my-nginx-cf54cdbf7-xgzxx   1/1     Running   0          78s
portforwardpod             1/1     Running   0          9m44s
simpleservice              1/1     Running   0          38m
student@bchd:~$ kubectl port-forward my-nginx-cf54cdbf7-2cbjd 2224:80 --address=0.0.0.0
Forwarding from 0.0.0.0:2224 -> 80
Handling connection for 2224
^Cstudent@bchd:~$ kubectl port-forward portforwardpod 2224:80 --address=0.0.0.0
Forwarding from 0.0.0.0:2224 -> 80
Handling connection for 2224


