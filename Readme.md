# Learn about Kubernetes
    - Kubernetes does not build image , instead it pulls already built image and start it as container(inside object called Pod)
    - Kubernetes contains
        - kubernetes cluster contains VM, VM runs node
        - Kubernetes node runs container based on the instructions received from kube-server-api
        - kubeProxy through which connection to the kubernestes cluster is established from outside world
        - kube-server-api manages the node. 
            - it takes config file, create a container specified as image in cofig file, 
            - number of replicas of created containers will be mainted by kube-server-api.
            - return current status of running containers
        - kubectl
            through which interactio happend from outside world to the kubernetes master
            kubectl apply -f [ filename ]
                apply means perform change , -f to point specific file
                 one can perform change in the .yml file (example change image) and execute above command, this will update existing pod by matching the name and Kind when found.
            kubectl get all
                see all object status
            kubectl get [ object ] (pods|service|deployment)
                see status of specific object
            kubectl describer [object type](pods|services) [object name](client-pod)
            kubectl delete -f [ filename ] - to delete an object
## Flow of this project setup
    browser -> kubeProxy -> conainer created with client-node-port.yml -> container created with client-pod.yml


## Access output through minikube(should be used only in dev)
    minikube ip - provides ip

    kubectl apply -f client-pod.yml - serves the output of the image

    kubectl apply -f client-node-port.yml -accpets the connection from outside world and pass it to container created with client-pod.yml

    http://minikubeip:NodePort(mentioned in client-node-port.yml)



### see output
install minikube:

https://loft.sh/blog/kubernetes-on-windows-6-life-saving-tools-and-tips/

powershell in admin mode:

install chocolatey

choco install minikube

start minikube:

C:\Users\admin>minikube start --driver=docker
W1230 19:39:42.829454    1892 main.go:291] Unable to resolve the current Docker CLI context "default": context "default": context not found: open C:\Users\admin\.docker\contexts\meta\37a8eec1ce19687d132fe29051dca629d164e2c4958ba141d5f4133a33f0688f\meta.json: The system cannot find the path specified.
* minikube v1.32.0 on Microsoft Windows 10 Pro 10.0.19045.3803 Build 19045.3803
* Using the docker driver based on user configuration
* Using Docker Desktop driver with root privileges
* Starting control plane node minikube in cluster minikube
* Pulling base image ...
* Downloading Kubernetes v1.28.3 preload ...
    > preloaded-images-k8s-v18-v1...:  403.35 MiB / 403.35 MiB  100.00% 3.81 Mi
    > gcr.io/k8s-minikube/kicbase...:  453.90 MiB / 453.90 MiB  100.00% 3.80 Mi
* Creating docker container (CPUs=2, Memory=4000MB) ...
* Preparing Kubernetes v1.28.3 on Docker 24.0.7 ...
  - Generating certificates and keys ...
  - Booting up control plane ...
  - Configuring RBAC rules ...
* Configuring bridge CNI (Container Networking Interface) ...
* Verifying Kubernetes components...
  - Using image gcr.io/k8s-minikube/storage-provisioner:v5
* Enabled addons: storage-provisioner, default-storageclass
* Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default

C:\Users\admin>
C:\Users\admin>
C:\Users\admin>kubectl get all
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   29s

C:\Users\admin>D:

D:\>cd Downloads\docker_udemy\exercises\kubernetes\simplek8s

D:\Downloads\docker_udemy\exercises\kubernetes\simplek8s>kubectl apply -f client-node-port.yml
service/client-node-port created

D:\Downloads\docker_udemy\exercises\kubernetes\simplek8s>kubectl apply -f client-pod.yml
pod/client-pod created

D:\Downloads\docker_udemy\exercises\kubernetes\simplek8s>kubectl get all
NAME             READY   STATUS              RESTARTS   AGE
pod/client-pod   0/1     ContainerCreating   0          6s

NAME                       TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/client-node-port   NodePort    10.105.39.34   <none>        3050:31515/TCP   11s
service/kubernetes         ClusterIP   10.96.0.1      <none>        443/TCP          73s

D:\Downloads\docker_udemy\exercises\kubernetes\simplek8s>minikube ip
W1230 19:45:54.329936   12868 main.go:291] Unable to resolve the current Docker CLI context "default": context "default": context not found: open C:\Users\admin\.docker\contexts\meta\37a8eec1ce19687d132fe29051dca629d164e2c4958ba141d5f4133a33f0688f\meta.json: The system cannot find the path specified.
192.168.49.2

D:\Downloads\docker_udemy\exercises\kubernetes\simplek8s>kubectl get all
NAME             READY   STATUS    RESTARTS   AGE
pod/client-pod   1/1     Running   0          3m37s

NAME                       TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/client-node-port   NodePort    10.105.39.34   <none>        3050:31515/TCP   3m42s
service/kubernetes         ClusterIP   10.96.0.1      <none>        443/TCP          4m44s

D:\Downloads\docker_udemy\exercises\kubernetes\simplek8s>docker context use default
default
Current context is now "default"

D:\Downloads\docker_udemy\exercises\kubernetes\simplek8s>minikube ip
192.168.49.2

D:\Downloads\docker_udemy\exercises\kubernetes\simplek8s>minikube service

X Exiting due to MK_USAGE: You must specify service name(s) or --all


D:\Downloads\docker_udemy\exercises\kubernetes\simplek8s>minikube service -all
Error: unknown shorthand flag: 'a' in -all
See 'minikube service --help' for usage.

D:\Downloads\docker_udemy\exercises\kubernetes\simplek8s>minikube service --all

|-----------|------------------|-------------|---------------------------|
| NAMESPACE |       NAME       | TARGET PORT |            URL            |
|-----------|------------------|-------------|---------------------------|
| default   | client-node-port |        3050 | http://192.168.49.2:31515 |
|-----------|------------------|-------------|---------------------------|
|-----------|------------|-------------|--------------|
| NAMESPACE |    NAME    | TARGET PORT |     URL      |
|-----------|------------|-------------|--------------|
| default   | kubernetes |             | No node port |
|-----------|------------|-------------|--------------|
* service default/kubernetes has no node port
* Starting tunnel for service client-node-port.
* Starting tunnel for service kubernetes.

|-----------|------------------|-------------|------------------------|
| NAMESPACE |       NAME       | TARGET PORT |          URL           |
|-----------|------------------|-------------|------------------------|
| default   | client-node-port |             | http://127.0.0.1:63947 |
| default   | kubernetes       |             | http://127.0.0.1:63950 |
|-----------|------------------|-------------|------------------------|
* Opening service default/client-node-port in default browser...
* Opening service default/kubernetes in default browser...
! Because you are using a Docker driver on windows, the terminal needs to be open to run it.

