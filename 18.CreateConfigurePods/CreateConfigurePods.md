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


