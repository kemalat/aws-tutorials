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


### 1. Create Administrator User and Group
Before configuring Your AWS CLI Credentials, you need to create IAM Admin User and Group as explained. Having Admin user will simplfy the EKS creation and deployment steps without drilling down to Amazon IAM user, role and policy structure. 
Follow this [tutorial](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html)
You should be able to create Administrator user smoohtly if you follow exactly the steps on above link using AWS web console.

### 2. Configure Your AWS CLI Credentials
Once you managed to create Administrator user succesfully, continue configuring your AWS CLI Credentials as explained on this [page](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html).

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
docker container ls
CONTAINER ID        IMAGE                       COMMAND                  CREATED             STATUS              PORTS                    NAMES
b3ba77dd0d71        kemalat/springboot-docker   "java -server -cp /a…"   3 minutes ago       Up 3 minutes        0.0.0.0:8080->8080/tcp   hello
814bea5afff7        ae893c58d83f                "nginx -g 'daemon of…"   7 minutes ago       Up 7 minutes                     
```

It is good practice to stop and remove unused containers on your local docker environment after performing inital sanity tests.

docker container stop b3ba77dd0d71
docker rmi b3ba77dd0d71

docker push kemalat/springboot-docker:latest

Push the image to a Amazon Elastic Container Registry(ECR)

Authenticate your Docker client to your ECR registry. Start by running the get-login command:
aws ecr get-login --no-include-email
The get-login command returns a docker login command similar to the following:
docker login -u AWS -p [password_string] https://[aws_account_id].dkr.ecr.us-east-2.amazonaws.com



Delete Cluster
https://docs.aws.amazon.com/eks/latest/userguide/delete-cluster.html

