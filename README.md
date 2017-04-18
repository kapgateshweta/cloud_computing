# cloud_computing

Steps to run kubernets and node.js along with scaling feature

1.Create project inside GCE

2.Start the google cloud shell
Output: 
Welcome to Cloud Shell! Type "help" to get started.

3.gcloud auth list
It will display the active accounts associated with cloud
Output:
kapgateshweta@kubernet-164923:~$ gcloud auth list
Credentialed Accounts:
 - kapgateshweta@gmail.com ACTIVE
To set the active account, run:
    $ gcloud config set account `ACCOUNT`
    
4.gcloud config list project
it will list all the projects available 
Output:
kapgateshweta@kubernet-164923:~$ gcloud config list project
[core]
project = kubernet-164923
Your active configuration is: [cloudshell-6690]

5. gcloud config set project <Project-id>
It sets the project with respective ID
Output:
kapgateshweta@kubernet-164923:~$ gcloud config set project kubernet-164923
Updated property [core/project].

6. Create server.js

Output:
var http = require('http');
var handleRequest = function(request, response){
response.writeHead(200);
response.end("Hello World in the class of computing!!");
}
var www = http.createServer(handleRequest);
www.listen(8080);

7. See the web preview by logging into
Output:
https://8080-dot-2345765-dot-devshell.appspot.com/?authuser=0

8. Create Docker container image
Output:
FROM node:6.9.2
EXPOSE 8080
COPY server.js .
CMD node server.js


9. Build the image using : docker build -t gcr.io/PROJECT_ID/hello-node:v1 .
kapgateshweta@kubernet-164923:~$ docker build -t gcr.io/kubernet-164923/hello-node:v1 .                                                                                                               
Sending build context to Docker daemon 114.2 kB
Step 1 : FROM node:6.9.2
6.9.2: Pulling from library/node
75a822cd7888: Pull complete 
57de64c72267: Pull complete 
4306be1e8943: Pull complete 
871436ab7225: Pull complete 
0110c26a367a: Pull complete 
1f04fe713f1b: Pull complete 
ac7c0b5fb553: Pull complete 
Digest: sha256:2e95be60faf429d6c97d928c762cb36f1940f4456ce4bd33fbdc34de94a5e043
Status: Downloaded newer image for node:6.9.2
 ---> faaadb4aaf9b
Step 2 : EXPOSE 8080
 ---> Running in 3ee886dbc73e
 ---> d8602feefd1c
Removing intermediate container 3ee886dbc73e
Step 3 : COPY server.js .
 ---> e1a4172b324b
Removing intermediate container 814842338c12
Step 4 : CMD node server.js
 ---> Running in 377947db969f
 ---> 1eab48b007a9
Removing intermediate container 377947db969f
Successfully built 1eab48b007a9


10. Once it completes, the image can be tested using port 8080 from newly created container using:
docker run -d -p 8080:8080 gcr.io/PROJECT_ID/hello-node:v1
Output:
kapgateshweta@kubernet-164923:~$ docker run -d -p 8080:8080 gcr.io/kubernet-164923/hello-node:v1
e17cb07cfbc15311b0b9b6bf51ab1a46e790ff69f387832ecddfb4478dab4c24
kapgateshweta@kubernet-164923:~$ 

11.Curl the script on localhost
Output:
kapgateshweta@kubernet-164923:~$ curl http://localhost:8080
Hello World in the class of computing!!kapgateshweta@kubernet-164923:~$ 

12. Stop the docker 
Hello World in the class of computing!!kapgateshweta@kubernet-164923:~$ docker ps
CONTAINER ID        IMAGE                                  COMMAND                  CREATED             STATUS              PORTS                    NAMES
e17cb07cfbc1        gcr.io/kubernet-164923/hello-node:v1   "/bin/sh -c 'node ser"   2 minutes ago       Up 2 minutes        0.0.0.0:8080->8080/tcp   stoic_hopper
kapgateshweta@kubernet-164923:~$ 

13. Stop the docker using the container ID  
kapgateshweta@kubernet-164923:~$ docker stop e17cb07cfbc1
e17cb07cfbc1

14. Push the docker to google container registry 
gcloud docker -- push gcr.io/PROJECT_ID/hello-node:v1
Output:
kapgateshweta@kubernet-164923:~$ gcloud docker -- push gcr.io/kubernet-164923/hello-node:v1
The push refers to a repository [gcr.io/kubernet-164923/hello-node]
071cd0c727ee: Pushed 
381c97ba7dc3: Pushed 
604c78617f34: Pushed 
fa18e5ffd316: Pushed 
0a5e2b2ddeaa: Pushed 
53c779688d06: Pushed 
60a0858edcd5: Pushed 
b6ca02dfe5e6: Pushed 
v1: digest: sha256:952de1552c6b4ab82de376820bed45876746cb8c08810f430c7bf9aa6d4bac5e size: 2002


15. Create cluster
gcloud container clusters create hello-world \
                --num-nodes 2 \
                --machine-type n1-standard-1 \
                --zone us-central1-c
Output:
Created [https://container.googleapis.com/v1/projects/kubernet-164923/zones/us-central1-c/clusters/hello-world].
kubeconfig entry generated for hello-world.
NAME         ZONE           MASTER_VERSION  MASTER_IP      MACHINE_TYPE   NODE_VERSION  NUM_NODES  STATUS
hello-world  us-central1-c  1.5.6           35.184.98.220  n1-standard-1  1.5.6         2          RUNNING



16. Create pod (A kubernetes pod is a group of containers, tied together for the purposes of administration and networking. It can contain a single container or multiple)
kubectl run hello-node \
    --image=gcr.io/PROJECT_ID/hello-node:v1 \
    --port=8080
Output:
kapgateshweta@kubernet-164923:~$ kubectl run hello-node     --image=gcr.io/kubernet-164923/hello-node:v1     --port=8080
deployment "hello-node" created


17. View the deployments
kubectl get deployments
Output:
kapgateshweta@kubernet-164923:~$ kubectl get deployments
NAME         DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
hello-node   1         1         1            1           2m

18. View the pod created by the deployment
kubectl get pods
Output:
kapgateshweta@kubernet-164923:~$ kubectl get pods
NAME                         READY     STATUS    RESTARTS   AGE
hello-node-391301646-vzmnp   1/1       Running   0          2m

19. The pod is only accessible by its internal IP within the cluster. In order to make the hello-node container accessible from outside the kubernetes virtual network, we have to expose the pod as a kubernetes service.
kubectl expose deployment hello-node --type="LoadBalancer"
Output:
kapgateshweta@kubernet-164923:~$ kubectl expose deployment hello-node --type="LoadBalancer"
service "hello-node" exposed

20. kubectl get services :To find the publicly-accessible IP address of the service
Output:
kapgateshweta@kubernet-164923:~$ kubectl get services
NAME         CLUSTER-IP      EXTERNAL-IP      PORT(S)          AGE
hello-node   10.43.240.178   35.184.186.111   8080:31169/TCP   3m
kubernetes   10.43.240.1     <none>           443/TCP          11m

21. we should now be able to reach the service by pointing the browser to this address: http://<EXTERNAL_IP>:8080
http://35.184.186.111:8080/

22. One of the powerful features offered by Kubernetes is how easy it is to scale the application.
kubectl scale deployment hello-node --replicas=4
Output:
kapgateshweta@kubernet-164923:~$ kubectl scale deployment hello-node --replicas=4
deployment "hello-node" scaled

23. to se the updated deployment : kubectl get deployment
Output:
kapgateshweta@kubernet-164923:~$ kubectl get deployment
NAME         DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
hello-node   4         4         4            3           7m


24. kubectl get pods to list all the pods
Output:
kapgateshweta@kubernet-164923:~$ kubectl get pods 
NAME                         READY     STATUS              RESTARTS   AGE
hello-node-391301646-41ps4   0/1       ContainerCreating   0          34s
hello-node-391301646-rpqq9   1/1       Running             0          34s
hello-node-391301646-t8vmv   1/1       Running             0          34s
hello-node-391301646-vzmnp   1/1       Running             0          8m
