# Familiarize with Kubernetes Lab

How many nodes are part of the cluster?
```
1
```
What is the version of Kubernetes running on the nodes ?
```
kubectl get nodes
```
What is the flavor and version of Operating System on which the Kubernetes nodes are running?
```
kubectl get nodes -o wide
```
# Kubernetes Pods

How many pods exist on the system?
In the current(default) namespace.
```
kubectl get pods --no-headers | wc -l
```
Create a new pod using the nginx image.
```
kubectl run nginx-pod --image=nginx
```
How many pods are created now?
Note: We have created a few more pods. So please check again in the current(default) namespace.
```
kubectl get pods | grep ^newpods-
```
Which nodes are these pods placed on?
You must look at all the pods in detail to figure this out.
```
for pod in $(kubectl get pods --no-headers | awk '/^newpods-/{print $1}'); do
  node=$(kubectl get pod $pod -o jsonpath='{.spec.nodeName}')
  echo "Pod: $pod is on Node: $node"
done
```
We just created a new pod named webapp. How many containers are part of the webapp pod?
Note: Ignore the state of the pod for now.
```
kubectl get pod webapp -o jsonpath='{.spec.containers[*].name}' | wc -w
```
What images are used in the new webapp pod?
You must look at all the pods in detail to figure this out.
```
kubectl get pod webapp -o jsonpath='{.spec.containers[*].image}'
```
What is the state of the container agentx in the pod webapp?
Wait for it to finish the ContainerCreating state
```
kubectl get pod webapp -o jsonpath="{.status.containerStatuses[?(@.name=='agentx')].state}"
```
Why do you think the container agentx in pod webapp is in error?
Try to figure it out from the events section of the pod.
```
A Docker image with the name doesn't exte
```
What does the READY column in the output of the kubectl get pods command indicate?
```
Running container in POD/Total container in POD
```
Delete the webapp Pod.
Once deleted, wait for the pod to fully terminate.
```
kubectl delete pod webapp
```
Create a new pod with the name redis and the image redis123.
Use a pod-definition YAML file. And yes the image name is wrong!
Firstly type:
```
nano redis-pod.yaml
```
Secondly enter this in the file:
```
apiVersion: v1
kind: Pod
metadata:
  name: redis
spec:
  containers:
  - name: redis
    image: redis123
```
then type the command:
```
kubectl apply -f redis-pod.yaml
```
Now change the image on this pod to redis.
Once done, the pod should be in a running state.
```
edit back the file : `redis-pod.yaml` change the image from `redis123` to `redis`
```
 Delete the old (broken) pod
```
kubectl delete pod redis
```
Recreate the pod with the correct image
```
kubectl apply -f redis-pod.yaml
```
# Kubernetes ReplicaSets

How many PODs exist on the system?
In the current(default) namespace.
```
kubectl get pods --no-headers | wc -l
```
How many PODs are DESIRED in the new-replica-set?
```
kubectl get rs new-replica-set
```
What is the image used to create the pods in the new-replica-set?
```
kubectl get rs new-replica-set -o jsonpath='{.spec.template.spec.containers[*].image}'
```
How many PODs are READY in the new-replica-set?
```
kubectl get rs new-replica-set

```
Why do you think the PODs are not ready?
```
The image doesn't exist
```
Delete any one of the 4 PODs.
```
kubectl delete pod <pod_name>
```
Why are there still 4 PODs, even after you deleted one?
```
Due to the ReplicaSet which ensure that the desir POD is copied
```
Create a ReplicaSet using the replicaset-definition-1.yaml file located at /root/.
There is an issue with the file, so try to fix it
change`apiVersion: v1` to `apiVersion: apps/v1`

Fix the issue in the replicaset-definition-2.yaml file and create a ReplicaSet using it.
This file is located at /root/.
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: replicaset-2
spec:
  replicas: 2
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend   # <-- fixed to match selector
    spec:
      containers:
      - name: nginx
        image: nginx
```
Delete the two newly created ReplicaSets - replicaset-1 and replicaset-2
```
kubectl delete rs replicaset-1 replicaset-2
```
Fix the original replica set new-replica-set to use the correct busybox image.
Either delete and recreate the ReplicaSet or update the existing ReplicaSet and then delete all PODs, so new ones with the correct image will be created.
If you opt to delete the ReplicaSet and recreate it, please refer to the file named new-replica-set.yaml, which is saved in the /root/ directory for your convenience.

just change the `image: busybox777` to `image: busybox`

Scale the ReplicaSet to 5 PODs.
Use kubectl scale command or edit the replicaset using kubectl edit replicaset.
```
kubectl scale replicaset new-replica-set --replicas=5
```

# Kubernetes Rolling & Rollback Updates

We have deployed a simple web application. Inspect the PODs and the Services
Wait for the application to fully deploy and view the application using the link called Webapp Portal above your terminal.

Check the Pods status
```
kubectl get pods
```
Check Services running
```
kubectl get svc
```
Inspect the deployment in the default namespace and identify the number of PODs deployed by it
```
kubectl get deployments
```
What container image is used to deploy the applications?
```
kubectl get deployment <deployment-name> -o jsonpath='{.spec.template.spec.containers[*].image}'
```
Inspect the deployment and identify the current strategy
```
kubectl get deployment <deployment-name> -o jsonpath='{.spec.strategy.type}'
```
If you were to upgrade the application now what would happen?
```
PODs are update few at a time
```
Let us try that. Upgrade the application by setting the image on the deployment to kodekloud/webapp-color:v2
Do not delete and re-create the deployment. Only set the new image name for the existin
Firstly look at the container image associated with the POD 
```
kubectl get deployment <deployment-name> -o jsonpath='{.spec.template.spec.containers[*].image}'
```
Secondly verify the container image running under the deployment-name by: 
```
kubectl describe deployment <deployment-name>
```
Then, enter the commande:
```
kubectl set image deployment/<deployment-name> <container-name>=kodekloud/webapp-color:v2
```
Run the script curl-test.sh again. Notice the requests now hit both the old and newer versions (If checked immediately). However none of them fail.
Execute the script at /root/curl-test.sh
```
bash /root/curl-test.sh
```
Up to how many PODs can be down for upgrade at a time

Consider the current strategy settings and number of PODs - 4
```
maxUnavailable = 25% → 0.25 × 4 = 1 pod 
```
Change the deployment strategy to Recreate
Delete and re-create the deployment if necessary. Only update the strategy type for the existing deployment.

Change the strategy to `Recreate` by
```
kubectl patch deployment frontend --type=json -p='[
  {"op": "replace", "path": "/spec/strategy/type", "value": "Recreate"},
  {"op": "remove", "path": "/spec/strategy/rollingUpdate"}
]'
```
Upgrade the application by setting the image on the deployment to kodekloud/webapp-color:v3
Do not delete and re-create the deployment. Only set the new image name for the existing deployment.
Identify the container_name:
```
kubectl get deployment frontend -o jsonpath='{.spec.template.spec.containers[*].name}'
```
Then, set the new image:
```
kubectl set image deployment/frontend <container_name>=kodekloud/webapp-color:v3
```
# Kubernetes Deployments

What is the image used to create the pods in the new deployment?
```
kubectl get deployment <deployment-name> -o jsonpath='{.spec.template.spec.containers[*].image}'
```
Why do you think the deployment is not ready?
`
the image doesn't exist
`
Create a new Deployment using the deployment-definition-1.yaml file located at /root/.
There is an issue with the file, so try to fix it
`verify the syntax first then enter`
```
kubectl apply -f /root/deployment-definition-1.yaml
```

Create a new Deployment with the below attributes using your own deployment definition file.
Name: httpd-frontend;
Replicas: 3;
Image: httpd:2.4-alpine
enter: 
```
nano deployment-httpd-frontend.yaml
```
then edit the file with the following content
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd-frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: httpd-frontend
  template:
    metadata:
      labels:
        app: httpd-frontend
    spec:
      containers:
      - name: httpd
        image: httpd:2.4-alpine
        ports:
        - containerPort: 80
```


# Kubernetes Services

What is the default service created by Kubernates?
```
kubernates
```
What is the type of the default kubernetes service?
```
ClusterIP
```
What is the targetPort configured on the kubernetes service?
```
kubectl get service kubernetes -o yaml
```
How many labels are configured on the kubernetes service?
```
kubectl get service <service-name> --show-labels
```
How many Endpoints are attached on the kubernetes service?
```
kubectl get endpoints kubernetes
```
How many Deployments exist on the system now?
In the current(default) namespace
```
kubectl get deployments --no-headers | wc -l
```
What is the image used to create the pods in the deployment?
look for the deployment name 
```
kubectl get deployment
```
then:
```
kubectl get deployment <deployment-name> -o jsonpath='{.spec.template.spec.containers[*].image}'
```
Create a new service to access the web application using the service-definition-1.yaml file.
Name: webapp-service
Type: NodePort
targetPort: 8080
port: 8080
nodePort: 30080
selector:
  name: simple-webapp

edit the file by : `nano service-definition-1.yaml`
then: 
```
kubectl apply -f /root/service-definition-1.yaml
```
























































































