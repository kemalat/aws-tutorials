## Deploying java application to Amazon EKS environment

### Notes 

* Be familiar with Kubernetes architecture and have a look [Amazon EKS pricing page](https://aws.amazon.com/eks/pricing/).
In Short, you will be billed for Amazon EKS cluster as master node $0.20 per hour apart from worker nodes. For worker nodes according to [EC2 pricing](https://aws.amazon.com/ec2/pricing/on-demand/) you will be charged
* In our deployment, we will run EKS on AWS using EC2.
* It is assumed that you are using any Linux distribution on your local environment

### Prerequisities 

* Get a Docker account/id.
* Download and install Docker Desktop
* Do Docker login from your terminal
* Enable Kubernetes from Preferences / Kubernate tab.
* Make sure that Docker Desktop runs on your local environment
* Get a github account.
* Download and install git.
* Follow this [tutorial](https://kbroman.org/github_tutorial/pages/first_time.html) to login github from ssh-terminal 
* Make sure that you managed to log in to github from your terminal 
* Create Amazon account and be familiar with AWS web console enviroment
* Install AWS CLI, eksctl.As starting point, please follow [Amazon EKS user guide](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html) to install AWS CLI and eksctl as explained.
* Be familiar with any Java IDE(e.g. Eclipse, Intellij Idea) and have sufficient knowledge to use Maven
* Install `curl if you don't have on your Linux distro using


### 1. Create Administrator User and Group
Before configuring Your AWS CLI Credentials, you need to create IAM Admin User and Group as explained. Having Admin user will simplfy the EKS creation and deployment steps without drilling down to Amazon IAM user, role and policy structure. 
Follow this [tutorial](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html)
You should be able to create Administrator user smoohtly if you follow exactly the steps on above link using AWS web console.

### 2. Configure Your AWS CLI Credentials
Once you managed to create Administrator user succesfully, continue configuring your AWS CLI Credentials as explained on this [page](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html).

- You may need to add Python binaries to your current PATH to run aws cli from all folders.
```bash
export PATH=~/Library/Python/3.7/bin:$PATH
```
### 3. Checkout demo project
[Spring Boot Docker guide](https://spring.io/guides/gs/spring-boot-docker/) published by Pivotal is enough for performing the demo Kubernetes deployment because it is not requiring any MySQL database. Deploying MySQL to Kubernetes enviroment is the subject of another tutorial. The project we use for demo is fairly simply. It exposes Rest service and returns the static text message when it is called.

### 4. Use Google Jib Maven Plugin and build Docker Image
Clone sample project as explained in [Spring Boot Docker guide](https://spring.io/guides/gs/spring-boot-docker/). Update Maven pom.xml by adding Jib plugin directives. Jib is a Maven plugin for building Docker images for Java applications.Simply you dont have to write DockerFile from the scratch, this Maven plugin generates it for the project structure and creates Docker Image. Here is the my sample plugin configuration I use for the demo deployment :

```
            <plugin>
                <groupId>com.google.cloud.tools</groupId>
                <artifactId>jib-maven-plugin</artifactId>
                <version>1.6.1</version>
                <configuration>
                    <from>
                        <image>openjdk:11.0.4-jre-slim</image>
                    </from>
                    <to>
                        <image>kemalat/${project.artifactId}:latest</image>
                        <!-- optional: create a tag based on the git commit id (via the git-commit-id plugin): -->
                        <tags>
                            <tag>${git.commit.id}</tag>
                        </tags>
                    </to>
                    <container>
                        <jvmFlags>
                            <jvmFlag>-server</jvmFlag>
                        </jvmFlags>
                    </container>
                </configuration>
                <executions>
                    <execution>
                        <id>build-and-push-docker-image</id>
                        <phase>package</phase>
                        <goals>
                            <goal>build</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
```

Build the java project and do containerizing application to Docker daemon.
```
mvn compile jib:dockerBuild.  
```
### 5. Run Docker Container, test and push to Docker Hub

Once the build completed , your docker container is ready to run. Before pushing it to docker hub, it is better to run on your local docker environment to eliminate unexpected errors or time delays due to several pushes. 
```
$ docker container run --name hello -p 8080:8080 -d kemalat/springboot-docker
```
```
$ docker container ls
CONTAINER ID        IMAGE                       COMMAND                  CREATED             STATUS              PORTS                    NAMES
b3ba77dd0d71        kemalat/springboot-docker   "java -server -cp /a…"   3 minutes ago       Up 3 minutes        0.0.0.0:8080->8080/tcp   hello
814bea5afff7        ae893c58d83f                "nginx -g 'daemon of…"   7 minutes ago       Up 7 minutes                     
```
Let's test the application running as Docker container
```
$ curl http://localhost:8080
Hello Docker World
```
Continue with pushing the image to Docker Hub ;

```
$ docker push kemalat/springboot-docker:latest
```
It is good practice to stop and remove unused containers on your local docker environment after performing inital sanity tests and pushing to Docker Hub

```
$ docker container stop b3ba77dd0d71
$ docker rmi b3ba77dd0d71
```
### 6. Creating EKS cluster 
If command line tools were installed succesfully, you would not expect any obstacle during the cluster creation step;

```
eksctl create cluster \
--name prod \
--version 1.14 \
--region us-west-2 \
--nodegroup-name standard-workers \
--node-type t3.medium \
--nodes 3 \
--nodes-min 1 \
--nodes-max 4 \
--managed
```
Expected output is 

```
[ℹ]  eksctl version
[ℹ]  using region us-west-2
[ℹ]  setting availability zones to [us-west-2a us-west-2c us-west-2b]
[ℹ]  subnets for us-west-2a - public:192.168.0.0/19 private:192.168.96.0/19
[ℹ]  subnets for us-west-2c - public:192.168.32.0/19 private:192.168.128.0/19
[ℹ]  subnets for us-west-2b - public:192.168.64.0/19 private:192.168.160.0/19
[ℹ]  using Kubernetes version 1.14
[ℹ]  creating EKS cluster "prod" in "us-west-2" region
[ℹ]  will create 2 separate CloudFormation stacks for cluster itself and the initial managed nodegroup
[ℹ]  if you encounter any issues, check CloudFormation console or try 'eksctl utils describe-stacks --region=us-west-2 --cluster=prod'
[ℹ]  CloudWatch logging will not be enabled for cluster "prod" in "us-west-2"
[ℹ]  you can enable it with 'eksctl utils update-cluster-logging --region=us-west-2 --cluster=prod'
[ℹ]  Kubernetes API endpoint access will use default of {publicAccess=true, privateAccess=false} for cluster "prod" in "us-west-2"
[ℹ]  2 sequential tasks: { create cluster control plane "prod", create managed nodegroup "standard-workers" }
[ℹ]  building cluster stack "eksctl-prod-cluster"
[ℹ]  deploying stack "eksctl-prod-cluster"
[ℹ]  deploying stack "eksctl-prod-nodegroup-standard-workers"
[✔]  all EKS cluster resources for "prod" have been created
[✔]  saved kubeconfig as "/Users/ericn/.kube/config"
[ℹ]  nodegroup "standard-workers" has 3 node(s)
[ℹ]  node "ip-192-168-29-149.us-west-2.compute.internal" is ready
[ℹ]  node "ip-192-168-48-14.us-west-2.compute.internal" is ready
[ℹ]  node "ip-192-168-92-183.us-west-2.compute.internal" is ready
[ℹ]  waiting for at least 1 node(s) to become ready in "standard-workers"
[ℹ]  nodegroup "standard-workers" has 3 node(s)
[ℹ]  node "ip-192-168-29-149.us-west-2.compute.internal" is ready
[ℹ]  node "ip-192-168-48-14.us-west-2.compute.internal" is ready
[ℹ]  node "ip-192-168-92-183.us-west-2.compute.internal" is ready
[ℹ]  kubectl command should work with "/Users/ericn/.kube/config", try 'kubectl get nodes'
[✔]  EKS cluster "prod" in "us-west-2" region is ready
```

### 7. Push the image to a Amazon Elastic Container Registry(ECR)

Pushing the images to a registry allows the Kubernetes cluster to create pods by using your container images. The registry that you use is called Amazon Elastic Container Registry (ECR). Now start with authenticating your Docker client to your ECR registry. 

```
$ aws ecr get-login --no-include-email
```
The get-login command returns a docker login command similar to the following:
```
docker login -u AWS -p [password_string] https://[aws_account_id].dkr.ecr.us-east-2.amazonaws.com
```
> Note: The [aws_account_id] is a unique 12-digit ID that is assigned to every AWS account. You will notice this ID in the output from various commands because AWS uses it to differentiate your resources from other accounts.

Execute get-login command output to perform authentication to Amazon ECR

```
$ docker login -u AWS -p [password_string] https://[aws_account_id].dkr.ecr.us-east-2.amazonaws.com
```
Create a repository to store the springboot-docker image:
```
$ aws ecr create-repository --repository-name kemalat/springboot-docker
```
Similar output as shown below will be returned from ECR ;

```
{
    "repository": {
        "repositoryArn": "arn:aws:ecr:us-east-2:999999999999:repository/kemalat/springboot-docker",
        "registryId": "999999999999",
        "repositoryName": "kemalat/springboot-docker",
        "repositoryUri": "999999999999.dkr.ecr.us-east-2.amazonaws.com/kemalat/springboot-docker",
        "createdAt": 1576494906.0,
        "imageTagMutability": "MUTABLE",
        "imageScanningConfiguration": {
            "scanOnPush": false
        }
    }
}
```
Tag your container images with the relevant data about your registry and push to ECR ;

```
$ docker tag kemalat/springboot-docker:latest 999999999999.dkr.ecr.us-east-2.amazonaws.com/kemalat/springboot:latest
```

```
$ docker push 999999999999.dkr.ecr.us-east-2.amazonaws.com/kemalat/gs-spring-boot-docker:latest
```
### 8. Deploying the microservices to worker nodes

Deploying container image to Kubernetes done using Kubernetes resource definition. Kubernetes resource definition is a yaml file that contains a description of all your deployments, services, or any other resources that you want to deploy. All resources can also be updated or deleted from the cluster by using the same yaml file that you used to deploy them. 

Here is the resource definiton(kubernetes.yaml) I used for deploying and starting the demo service ;
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: springboot-docker-deployment
  labels:
    app: springboot-docker
spec:
  selector:
    matchLabels:
      app: springboot-docker
  template:
    metadata:
      labels:
        app: springboot-docker
    spec:
      containers:
      - name: springboot-docker-container
        image: 999999999999.dkr.ecr.us-east-2.amazonaws.com/kemalat/springboot-docker:latest
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: springboot-docker-service
spec:
  type: NodePort
  selector:
    app: springboot-docker
  ports:
  - protocol: TCP
    port: 8080
    targetPort: 8080
    nodePort: 31000
    
    
```
Now we can run the following command to deploy the resources as defined in kubernetes.yaml as shown above:
```
$ kubectl apply -f kubernetes.yaml
```

When the service are deployed, run the following command to check the status of pod:
```
$ kubectl get pods
```

If our pod is healthy and running, we see an output similar to the following:
```
NAME                                    READY     STATUS    RESTARTS   AGE
springboot-docker-deployment-7d8cc5dc87-sxvdh      1/1       Running   0          30s
```
We can inspect the application log. Pass name of pod to kubectl logs. For example;

```
kubectl logs springboot-docker-deployment-7d8cc5dc87-sxvdh
```
In order to retrive cluster IP;
```
kubectl get service springboot-docker-service
```
```
NAME                        TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
springboot-docker-service   NodePort   10.100.221.131   <none>        8080:31000/TCP   8h
```

> Note: Our service type is NodePort, The ClusterIP can be accessed by other Pods in the same cluster, but can not be accessed externally outside of the cluster. For NodePort type service ClusterIP is also created automatically which takes the route from the NodePort. The NodePort Service is exposed externally on the available worker nodes at port 31000 for our deployment according to our configuration. That's why we chose NodePort type service to access our web service outside from internal network.

### 9. Accessing the microservice from outside(NodePort service)

In order to access our web service, we need to get external IPs of our worker nodes. We will get the external ip in the output of the following command and send our HTTP requests to that IP.

```
$ kubectl get nodes -o wide |  awk {'print $1" " $2 " " $7'} | column -t
```

```
NAME                                          STATUS  EXTERNAL-IP
ip-192-168-18-253.us-east-2.compute.internal  Ready   18.223.117.241
ip-192-168-60-223.us-east-2.compute.internal  Ready   3.15.200.248
ip-192-168-67-181.us-east-2.compute.internal  Ready   3.135.186.165
```
Before we test accessing NodeIP:NodePort from an outside cluster, we need to modify the security group of the nodes to allow incoming traffic through the port 31000. Now you need to login to the AWS console and find the nodes, edit its security group by adding one more inbound rule. 

Go to EC2 console, select Security Groups, find `eksctl-yourClusterName-nodegroup-standard-workers` and add custom TCP rule as shown in the picture.(Clicking Actions -> ).

After you are finished adding the inbound rule to the security group, you might need to wait a few minutes before you try to access springboot-docker-service microservice. 

```
$ curl http://18.223.117.241:31000
Hello Docker World
```

### 10. Accessing the microservice from outside(As LoadBalancer service)
In a cloud environment like AWS, creating an external load balancer is better than creating a NodePort for exposing Services. Because only one URL will be published to outside and web service requests will be distributed among worker nodes by load balancer to achieve optimum load distribution.

We can create a load balancer Service by using the same template except changing the type to “LoadBalancer”. Save as your  resource definiton(kubernetes.yaml) as loadbalancer.yaml an update as shown below

```
apiVersion: v1
kind: Service
metadata:
  name: springboot-docker-service
spec:
  type: LoadBalancer
  selector:
    app: springboot-docker
  ports:
  - protocol: TCP
    port: 8080
    targetPort: 8080
    
```
Now we can then create a load balancer Service.

```
$ kubectl delete service springboot-docker-service
service "springboot-docker-service" deleted
$ kubectl create -f loadbalancer.yaml
kemalat/springboot-docker-service created
```
Get information about “springboot-docker-service” to learn loadbalancer URL address;

```
$ kubectl get kemalat/springboot-docker-service |  awk {'print $1" " $2 " " $4 " " $5'} | column -t
NAME       TYPE          EXTERNAL-IP                                                             PORT(S)
myservice  LoadBalancer  adb010d2006b511e99d7702abbb5e7b9-679524454.us-east-2.elb.amazonaws.com  80:31000/TCP    
```    
We can test if we can access the ELB externally.

```    
$ curl -silent adb010d2006b511e99d7702abbb5e7b9-679524454.us-east-2.elb.amazonaws.com:8080
Hello Docker World
```    

### 11. Deleting Cluster to avoid unnecessary costs

After you are done, do not forget to follow directives given [here](https://docs.aws.amazon.com/eks/latest/userguide/delete-cluster.html) to delete all resources of AWS EKS cluster

