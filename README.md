## Deploying spring boot java application and MySql on Amazon EKS environment

Be familiar with Kubernetes architecture and have a look Amazon EKS pricing page.https://aws.amazon.com/eks/pricing/
In Short, you will be billed for Amazon EKS cluster as master node $0.20 per hour apart from worker nodes. For worker nodes according to EC2 pricing you will be charged
In our deployment, we will run EKS on AWS using either EC2.

Prerequisities 

Get a Docker account/id.
Download and install Docker Desktop
Enable Kubernetes from Preferences / Kubernate tab.
Make sure that Docker Desktop runs on your local environment

Get a github account.
Download and install git.
Follow this tutorial to login github from ssh-terminal 
https://kbroman.org/github_tutorial/pages/first_time.html
Make sure that you managed to log in to github from your terminal 

Install AWS CLI, eksctl.
As starting point, please follow Amazon EKS user guide to install AWS CLI and eksctl as explained
(https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html)

Create Administrator User and Group
Before configuring Your AWS CLI Credentials, you need to create IAM Admin User and Group as explained. Having Admin user will simplfy the EKS creation and deployment steps without drilling down to Amazon IAM user, role and policy structure. 
Follow this tutorial https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html
You should be able to create Administrator User smoohtly if you follow exactly the steps on above link.

Configure Your AWS CLI Credentials
Once you managed to create Administrator user succesfully, continue Configure Your AWS CLI Credentials as explained on this page. (https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html)


Clone sample project, update Maven pom.xml by adding Jib plugin.

Jib is a Maven plugin for building Docker images for Java applications.Simply you dont have to write DockerFile from the scratch, this Maven plugin generates it for the project structure and creates Docker Image. 
Here is the my sample plugin configuration I use for the demo deployment :

...
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
            ...


Build the java project and do containerizing application to Docker daemon.

The project we use for demo is fairly simply. It exposes Rest service and returns the static text message when it is called.
Required maven command is compile jib:dockerBuild.  Once the build completed , your docker container is ready to run. Before pushing it to docker hub, I advise running it on your local docker environment to eliminate errors. 

docker container run --name hello -p 8080:8080 -d kemalat/springboot-docker

r2d2:~ kemalatik$ docker container ls
CONTAINER ID        IMAGE                       COMMAND                  CREATED             STATUS              PORTS                    NAMES
b3ba77dd0d71        kemalat/springboot-docker   "java -server -cp /a…"   3 minutes ago       Up 3 minutes        0.0.0.0:8080->8080/tcp   hello
814bea5afff7        ae893c58d83f                "nginx -g 'daemon of…"   7 minutes ago       Up 7 minutes                     


