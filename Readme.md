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
            kubectl set(change sepcific property) image(specify the property name) [ type of object ](pod|service|deployment)/[object name] [name of the container]=[new value to the property] (image in this case)

            example: kubectl set image deployment/client-deployment client=stephengrider/multi-client:v5

            To seee list of docker images inside k8s:
                - eval $(minikube docker-env)
                - minikube -p minikube docker-env --shell powershell | Invoke-Expression -- in windows powershell
## what happens when kubectl command executed
    kubectl command -> Master(kuber-master) -> VM
    inside VM
        - docker server -> docker client -> image cache

            
            
## Deployment object
    - we can update only image name of the running Pod. ideally pod is suitable for dev not for PROD
    - to update port, replicas or images of pod we can make use of Deployment object.


## why we need Service object? why not connect to pod directly through kube proxy?
    - for some reason already created pod is killed and started newly means, ip address generated for new pod witll be different than existing pod.
    - when we connect through service, through selector it matches the pod with label and connect through targetPort with all replicas. so, customer will not see any difference when pod get killed , newly generated or replicated.
    