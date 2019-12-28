
## Deploying MySQL cluster to Amazon EKS environment

### Notes 

* Be familiar with Kubernetes architecture and have a look [Amazon EKS pricing page](https://aws.amazon.com/eks/pricing/).
You will be billed for Amazon EKS cluster as master node $0.20 per hour apart from worker nodes. For worker nodes according to [EC2 pricing](https://aws.amazon.com/ec2/pricing/on-demand/) you will be charged
* In our deployment, we will run EKS on AWS using EC2.
* It is assumed that you are using any Linux distribution on your local environment

### Prerequisities 

* Create Amazon account and be familiar with AWS web console enviroment
* Install AWS CLI, eksctl. As starting point, please follow [Amazon EKS user guide](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html) to install AWS CLI and eksctl as explained.
* Install and Configure kubectl for Amazon EKS, please follow this [guide](  https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html)
* Install any MySQL GUI tool to access database via user interface(e.g MySQL Workbench etc.)


### 1. Create Administrator User and Group
Before configuring Your AWS CLI Credentials, you need to create IAM Admin User and Group as explained. Having Admin user will simplfy the EKS creation and deployment steps without drilling down to Amazon IAM user, role and policy structure. 
Follow this [tutorial](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html)
You should be able to create Administrator user smoohtly if you follow exactly the steps on above link using AWS web console.

### 2. Configure Your AWS CLI Credentials
Once you managed to create Administrator user succesfully, continue configuring your AWS CLI Credentials as explained on this [page](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html).

### 3. Create EKS cluster 
If command line tools were installed succesfully and admin user created, you would not expect any obstacle during the cluster creation step;

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
### 4. Do cluster healthcheck

Test your connectivity to the cluster ;

```
kubectl cluster-info
```
If no errors listed, you’re connected to the cluster. If you access multiple clusters with kubectl, be sure to verify that you’ve selected the correct cluster context:

```
kubectl config get-contexts
```

```
Output
CURRENT   NAME                    CLUSTER                      AUTHINFO                      NAMESPACE
*         Administrator@mysql-clust.us-east-2.eksctl.io      mysql-clust.us-east-2.eksctl.io                    Administrator@mysql-clust.us-east-2.eksctl.io      
          docker-for-desktop      docker-for-desktop-cluster   docker-for-desktop
```

### 5. Install Helm
Helm is the first application package manager running on top of Kubernetes. Using this package manager simplifies their management greatly. Helm helps you manage Kubernetes applications — Helm Charts helps you define, install, and upgrade even the most complex Kubernetes application. Helm comes with installation script that handles the installation process.Change to a writable directory and download the script from Helm’s GitHub repository and run the script. 

```
$ cd /tmp
$ curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > install-helm.sh
$ chmod u+x install-helm.sh
./install-helm.sh
```
```
Output
helm installed into /usr/local/bin/helm
Run 'helm init' to configure helm.
```
### 6. Continue with Tiller and update helm repo
Next we will continue with Tiler. Tiller is the service that actually communicates with the Kubernetes API to manage our Helm packages.

```
$ kubectl create serviceaccount -n kube-system tiller
serviceaccount/tiller created

$ kubectl create clusterrolebinding tiller-crule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
clusterrolebinding.rbac.authorization.k8s.io/tiller-crule created

$ kubectl --namespace kube-system patch deploy tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'
deployment.extensions/tiller-deploy patched

$ helm init --service-account tiller --wait

$HELM_HOME has been configured at /home/presslabs/.helm.
Tiller (the Helm server-side component) has been installed into your Kubernetes Cluster.
Please note: by default, Tiller is deployed with an insecure 'allow unauthenticated users' policy.
For more information on securing your installation see: https://docs.helm.sh/using_helm/#securing-your-helm-installation
Happy Helming!

$ helm repo add presslabs https://presslabs.github.io/charts
"presslabs" has been added to your repositories

$ helm repo update
Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
...Successfully got an update from the "presslabs" chart repository
Update Complete.

```

### 7. Install MySQL

```
$ helm install presslabs/mysql-operator --name mysql-operator
NAME:   mysql-operator
LAST DEPLOYED: Thu Dec 19 12:44:21 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ClusterRole
NAME            AGE
mysql-operator  1s

==> v1/ConfigMap
NAME                AGE
mysql-operator-orc  1s

==> v1/Pod(related)
NAME              AGE
mysql-operator-0  1s

==> v1/Secret
NAME                AGE
mysql-operator-orc  1s

==> v1/Service
NAME                  AGE
mysql-operator        1s
mysql-operator-0-svc  1s

==> v1/ServiceAccount
NAME            AGE
mysql-operator  1s

==> v1/StatefulSet
NAME            AGE
mysql-operator  1s

==> v1beta1/ClusterRoleBinding
NAME            AGE
mysql-operator  1s


NOTES:
You can create a new cluster by issuing:

cat <<EOF | kubectl apply -f-
apiVersion: mysql.presslabs.org/v1alpha1
kind: MysqlCluster
metadata:
  name: my-cluster
spec:
  replicas: 1
  secretName: my-cluster-secret
---
apiVersion: v1
kind: Secret
metadata:
  name: my-cluster-secret
type: Opaque
data:
  ROOT_PASSWORD: $(echo -n "not-so-secure" | base64)
EOF

```
As you realized , helm instal generated required .yaml files to execute. We need to execute resource definition for cluster formation and root password.

Before creating a cluster, we need create the MySQL cluser root user password.ROOT_PASSWORD is base64 encoded password that you would like to set for MySQL root user. Convert your secret password to base64 encoded string.Create a file named example-cluster-secret.yaml and copy into it the following YAML code :


```
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  # root password is required to be specified
  ROOT_PASSWORD: Zm9vYmFy
  
```

Then create file example-cluster.yaml and copy into it the following:

```
apiVersion: mysql.presslabs.org/v1alpha1
kind: MysqlCluster
metadata:
  name: my-cluster
spec:
  replicas: 2
  secretName: my-secret
```

Deploy the cluster by applying resource files in given order ;

```
$ kubectl apply -f example-cluster-secret.yaml

secret "my-secret" created

$ kubectl apply -f example-cluster.yaml

mysqlcluster.mysql.presslabs.org "my-cluster"  created
```

### 8. Deployment health check

```
$ kubectl get mysql
NAME         READY   REPLICAS   AGE
my-cluster   False   2          14s
```

```
$ kubectl describe mysql my-cluster
Name:         my-cluster
Namespace:    default
Labels:       <none>
Annotations:  kubectl.kubernetes.io/last-applied-configuration:
                {"apiVersion":"mysql.presslabs.org/v1alpha1","kind":"MysqlCluster","metadata":{"annotations":{},"name":"my-cluster","namespace":"default"}...
              mysql.presslabs.org/version: 300
API Version:  mysql.presslabs.org/v1alpha1
Kind:         MysqlCluster
Metadata:
  Creation Timestamp:  2019-12-19T10:08:19Z
  Finalizers:
    mysql.presslabs.org/registered-in-orchestrator
  Generation:        2
  Resource Version:  10759
  Self Link:         /apis/mysql.presslabs.org/v1alpha1/namespaces/default/mysqlclusters/my-cluster
  UID:               7b51db42-2247-11ea-bdbd-026e6849b7ba
Spec:
  Pod Spec:
    Resources:
  Replicas:     2
  Secret Name:  my-secret
  Volume Spec:
Status:
  Conditions:
    Last Transition Time:  2019-12-19T10:08:20Z
    Message:               cluster is in read only
    Reason:                ClusterReadOnlyTrue
    Status:                True
    Type:                  ReadOnly
    Last Transition Time:  2019-12-19T10:08:20Z
    Message:               StatefulSet is not ready
    Reason:                StatefulSetNotReady
    Status:                False
    Type:                  Ready
    Last Transition Time:  2019-12-19T10:08:20Z
    Message:               no pending ack
    Reason:                NoPendingFailoverAckExists
    Status:                False
    Type:                  PendingFailoverAck
Events:
  Type    Reason                         Age                From                     Message
  ----    ------                         ----               ----                     -------
  Normal  ConfigMapSyncSuccessfull       30s                controller.mysqlcluster  *v1.ConfigMap default/my-cluster-mysql created successfully
  Normal  OperatedSecretSyncSuccessfull  30s                controller.mysqlcluster  *v1.Secret default/my-cluster-mysql-operated created successfully
  Normal  MasterSVCSyncSuccessfull       30s                controller.mysqlcluster  *v1.Service default/my-cluster-mysql-master created successfully
  Normal  HealthySVCSyncSuccessfull      30s                controller.mysqlcluster  *v1.Service default/my-cluster-mysql created successfully
  Normal  StatefulSetSyncSuccessfull     30s                controller.mysqlcluster  *v1.StatefulSet default/my-cluster-mysql created successfully
  Normal  PDBSyncSuccessfull             30s                controller.mysqlcluster  *v1beta1.PodDisruptionBudget default/my-cluster-mysql created successfully
  Normal  StatefulSetSyncSuccessfull     29s (x3 over 30s)  controller.mysqlcluster  *v1.StatefulSet default/my-cluster-mysql updated successfully
  ```
### 7. Access MySQL database from your environment

  ```
$ kubectl get svc --all-namespaces
NAMESPACE     NAME                      TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
default       kubernetes                ClusterIP   10.100.0.1       <none>        443/TCP             121m
default       my-cluster-mysql          ClusterIP   10.100.99.201    <none>        3306/TCP            30m
default       my-cluster-mysql-master   ClusterIP   10.100.102.76    <none>        3306/TCP            30m
default       mysql                     ClusterIP   None             <none>        3306/TCP,9125/TCP   30m
default       mysql-operator            ClusterIP   10.100.28.240    <none>        80/TCP              54m
default       mysql-operator-0-svc      ClusterIP   10.100.65.222    <none>        80/TCP,10008/TCP    54m
kube-system   kube-dns                  ClusterIP   10.100.0.10      <none>        53/UDP,53/TCP       121m
kube-system   tiller-deploy             ClusterIP   10.100.203.163   <none>        44134/TCP           59m
          
$ kubectl get pods
NAME                 READY   STATUS    RESTARTS   AGE
my-cluster-mysql-0   4/4     Running   0          4m46s
my-cluster-mysql-1   4/4     Running   0          3m47s
mysql-operator-0     2/2     Running   0          28m

$ kubectl port-forward services/my-cluster-mysql 8806:3306
Forwarding from 127.0.0.1:8806 -> 3306
Forwarding from [::1]:8806 -> 3306
Handling connection for 8806
Handling connection for 8806
Handling connection for 8806
```

Now you can connect MySQL using loopback Ip and port 8806 from your local environmnet
