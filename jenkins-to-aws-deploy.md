## Deployment to EC2 Auto Scaling Group by means of Jenkins and AWS CodeDeploy

We will use Jenkins plugin to deploy our builds automatically with AWS CodeDeploy. Our tasks will be;
* Create AWS CodeCommit repository
* Add files to the CodeCommit repository
* Install Jenkins and the Jenkins plugin
* Configure the plugin to create a deployment when changes are committed to an AWS CodeCommit repository.

### Notes 

### Prerequisities 
* Git client should be installed on your local working environment. 


### 1. Create Administrator User and Group
You need to create IAM Admin User and Group for Administrator access. Follow this [tutorial](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html)
You should be able to create Administrator user smoohtly if you follow exactly the steps on above link using AWS web console. 
Once you create Administrator user, we can continue with [Setup for HTTPS Users Using Git Credentials](https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-gc.html)

### 2. Generate Access Keys
Access Keys are neede to provided programatic access EC2 instances and resources during the cloud formation proces. You should generate Access Keys for non-root Adminstrator user. 
* Log in to your AWS Management Console.
* Click on your user name at the top right of the page.
* Click on the Security Credentials link from the drop-down menu.
* Find the Access Credentials section, and copy the latest Access Key ID.
* If you do not know the secret key , you should new Access Key and note the Key ID and Secret Key

### 3. Setup for HTTPS Users Using Git Credentials
Follow the [tutorial](https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-gc.html) till the Step 4 and then switch the AWS CodeCommit Web Console to crete repository. In order to login AWS CodeCommit Git Repository we need to have HTTPS Clone URL for the our remote repo.

### 4. Create AWS CodeCommit repository
Follow the [tutorial](https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-migrate-repository-existing.html#how-to-migrate-existing-create). You need to follow up Step 1: Create a CodeCommit Repository and Step 2: Clone the Repository and Push to the CodeCommit Repository. 

### 5. Add existing project to remote Git Repository
The first time you connect, you are prompted for the user name and password for the repository. Depending on the configuration of your local computer. You can follow this [tutorial](https://help.github.com/en/github/importing-your-projects-to-github/adding-an-existing-project-to-github-using-the-command-line) for pushing the your existing project to remote repo

```
$ cd sample-app
$ git init
$ git add .
$ git commit -m "First commit"
[master (root-commit) 3a135e6] First commit
 13 files changed, 157 insertions(+)
 create mode 100644 README.md
 create mode 100755 appspec.yml
 create mode 100755 buildspec.yml
 create mode 100755 requirements.txt
 create mode 100755 scripts/mkdir.sh
 ....
$ git remote add origin https://git-codecommit.us-east-1.amazonaws.com/v1/repos/sample-app
# Sets the new remote
$ git remote -v
# Verifies the new remote URL
Push the changes in your local repository to GitHub.
$ git push -u origin master
# Pushes the changes in your local repository up to the remote repositor
```

### 6. Create a Jenkins server and AWS CodeDeploy environment using AWS CloudFormation
We will use one of sample CloudFormation template to create required stack for our pipeline. The link for initiating cloud formation :

[Launch Stack](https://eu-central-1.console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/quickcreate?templateURL=https://blog-for-codedeploy.s3.eu-central-1.amazonaws.com/CodeDeployTemplate.json) 

You can also [download](https://blog-for-codedeploy.s3.eu-central-1.amazonaws.com/CodeDeployTemplate.json) the AWS Clouformation template. Data strucutre details of JSON should be another topic since it requires diving the AWS Cloud Platform in details. 

### 6. Follow Amazon tutorial from this point
Now you are ready to execute directives listed on this Amazon [tutorial](https://aws.amazon.com/blogs/devops/setting-up-a-ci-cd-pipeline-by-integrating-jenkins-with-aws-codebuild-and-aws-codedeploy/)

### 7. Jenkins Console log

```
Started by user Kemal Atik
Running as SYSTEM
Building in workspace /var/lib/jenkins/workspace/CodeDeployApp
[WS-CLEANUP] Deleting project workspace...
[WS-CLEANUP] Deferred wipeout is used...
[WS-CLEANUP] Done
using credential 6fe0a0df-6270-4219-a829-4cb64a541156
Cloning the remote Git repository
Cloning repository https://git-codecommit.us-east-1.amazonaws.com/v1/repos/sample-app
 > git init /var/lib/jenkins/workspace/CodeDeployApp # timeout=10
Fetching upstream changes from https://git-codecommit.us-east-1.amazonaws.com/v1/repos/sample-app
 > git --version # timeout=10
using GIT_ASKPASS to set credentials 
 > git fetch --tags --force --progress -- https://git-codecommit.us-east-1.amazonaws.com/v1/repos/sample-app +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git config remote.origin.url https://git-codecommit.us-east-1.amazonaws.com/v1/repos/sample-app # timeout=10
 > git config --add remote.origin.fetch +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git config remote.origin.url https://git-codecommit.us-east-1.amazonaws.com/v1/repos/sample-app # timeout=10
Fetching upstream changes from https://git-codecommit.us-east-1.amazonaws.com/v1/repos/sample-app
using GIT_ASKPASS to set credentials 
 > git fetch --tags --force --progress -- https://git-codecommit.us-east-1.amazonaws.com/v1/repos/sample-app +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git rev-parse refs/remotes/origin/master^{commit} # timeout=10
 > git rev-parse refs/remotes/origin/origin/master^{commit} # timeout=10
Checking out Revision 3a135e666ed948331140ebc48854d288a4e1e9ce (refs/remotes/origin/master)
 > git config core.sparsecheckout # timeout=10
 > git checkout -f 3a135e666ed948331140ebc48854d288a4e1e9ce # timeout=10
Commit message: "First commit"
 > git rev-list --no-walk 3a135e666ed948331140ebc48854d288a4e1e9ce # timeout=10
[AWS CodeBuild Plugin] Using given AWS access and secret key for authorization
[AWS CodeBuild Plugin] Zipping directory to upload to S3: /var/lib/jenkins/workspace/CodeDeployApp
[AWS CodeBuild Plugin] Uploading to S3 at location jenkins-deploy-codedeploybucket-pkh6sc9u2d0z/test. MD5 checksum is /cy+Aowg+FeffkPXX3vQWw==
[AWS CodeBuild Plugin] S3 object version id for uploaded source is RoxM5ljXSXvQd2Endoc9IKzlvgPguXsl
[AWS CodeBuild Plugin] Starting build with 
	> project name: build-project
	> source version: RoxM5ljXSXvQd2Endoc9IKzlvgPguXsl
[AWS CodeBuild Plugin] Build id: build-project:17095967-bf65-41fd-957d-5bfe2f71d1e1
[AWS CodeBuild Plugin] CodeBuild dashboard: https://eu-central-1.console.aws.amazon.com/codesuite/codebuild/projects/build-project/build/build-project:17095967-bf65-41fd-957d-5bfe2f71d1e1/log?region=eu-central-1
[AWS CodeBuild Plugin] CloudWatch dashboard: https://console.aws.amazon.com/cloudwatch/home?region=eu-central-1#logEvent:group=/aws/codebuild/build-project;stream=17095967-bf65-41fd-957d-5bfe2f71d1e1
[AWS CodeBuild Plugin] 2020/01/26 21:50:24 Waiting for agent ping
[AWS CodeBuild Plugin] 2020/01/26 21:50:26 Waiting for DOWNLOAD_SOURCE
[AWS CodeBuild Plugin] 2020/01/26 21:50:26 Phase is DOWNLOAD_SOURCE
[AWS CodeBuild Plugin] 2020/01/26 21:50:26 CODEBUILD_SRC_DIR=/codebuild/output/src613254054/src
[AWS CodeBuild Plugin] 2020/01/26 21:50:26 YAML location is /codebuild/output/src613254054/src/buildspec.yml
[AWS CodeBuild Plugin] 2020/01/26 21:50:26 Processing environment variables
[AWS CodeBuild Plugin] 2020/01/26 21:50:26 Moving to directory /codebuild/output/src613254054/src
[AWS CodeBuild Plugin] 2020/01/26 21:50:26 Registering with agent
[AWS CodeBuild Plugin] 2020/01/26 21:50:26 Phases found in YAML: 3
[AWS CodeBuild Plugin] 2020/01/26 21:50:26  INSTALL: 1 commands
[AWS CodeBuild Plugin] 2020/01/26 21:50:26  BUILD: 3 commands
[AWS CodeBuild Plugin] 2020/01/26 21:50:26  POST_BUILD: 1 commands
[AWS CodeBuild Plugin] 2020/01/26 21:50:26 Phase complete: DOWNLOAD_SOURCE State: SUCCEEDED
[AWS CodeBuild Plugin] 2020/01/26 21:50:26 Phase context status code:  Message: 
[AWS CodeBuild Plugin] 2020/01/26 21:50:26 Entering phase INSTALL
[AWS CodeBuild Plugin] 2020/01/26 21:50:26 Running command python -m pip install Flask
[AWS CodeBuild Plugin] Collecting Flask
[AWS CodeBuild Plugin] /usr/local/lib/python2.7/dist-packages/pip/_vendor/urllib3/util/ssl_.py:339: SNIMissingWarning: An HTTPS request has been made, but the SNI (Subject Name Indication) extension to TLS is not available on this platform. This may cause the server to present an incorrect TLS certificate, which can cause validation failures. You can upgrade to a newer version of Python to solve this. For more information, see https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings
[AWS CodeBuild Plugin]   SNIMissingWarning
[AWS CodeBuild Plugin] /usr/local/lib/python2.7/dist-packages/pip/_vendor/urllib3/util/ssl_.py:137: InsecurePlatformWarning: A true SSLContext object is not available. This prevents urllib3 from configuring SSL appropriately and may cause certain SSL connections to fail. You can upgrade to a newer version of Python to solve this. For more information, see https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings
[AWS CodeBuild Plugin]   InsecurePlatformWarning
[AWS CodeBuild Plugin] /usr/local/lib/python2.7/dist-packages/pip/_vendor/urllib3/util/ssl_.py:137: InsecurePlatformWarning: A true SSLContext object is not available. This prevents urllib3 from configuring SSL appropriately and may cause certain SSL connections to fail. You can upgrade to a newer version of Python to solve this. For more information, see https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings
[AWS CodeBuild Plugin]   InsecurePlatformWarning
[AWS CodeBuild Plugin] /usr/local/lib/python2.7/dist-packages/pip/_vendor/urllib3/util/ssl_.py:137: InsecurePlatformWarning: A true SSLContext object is not available. This prevents urllib3 from configuring SSL appropriately and may cause certain SSL connections to fail. You can upgrade to a newer version of Python to solve this. For more information, see https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings
[AWS CodeBuild Plugin]   InsecurePlatformWarning
[AWS CodeBuild Plugin]   Downloading https://files.pythonhosted.org/packages/9b/93/628509b8d5dc749656a9641f4caf13540e2cdec85276964ff8f43bbb1d3b/Flask-1.1.1-py2.py3-none-any.whl (94kB)
[AWS CodeBuild Plugin] Collecting Werkzeug>=0.15 (from Flask)
[AWS CodeBuild Plugin] /usr/local/lib/python2.7/dist-packages/pip/_vendor/urllib3/util/ssl_.py:137: InsecurePlatformWarning: A true SSLContext object is not available. This prevents urllib3 from configuring SSL appropriately and may cause certain SSL connections to fail. You can upgrade to a newer version of Python to solve this. For more information, see https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings
[AWS CodeBuild Plugin]   InsecurePlatformWarning
[AWS CodeBuild Plugin]   Downloading https://files.pythonhosted.org/packages/ce/42/3aeda98f96e85fd26180534d36570e4d18108d62ae36f87694b476b83d6f/Werkzeug-0.16.0-py2.py3-none-any.whl (327kB)
[AWS CodeBuild Plugin] Collecting itsdangerous>=0.24 (from Flask)
[AWS CodeBuild Plugin] /usr/local/lib/python2.7/dist-packages/pip/_vendor/urllib3/util/ssl_.py:137: InsecurePlatformWarning: A true SSLContext object is not available. This prevents urllib3 from configuring SSL appropriately and may cause certain SSL connections to fail. You can upgrade to a newer version of Python to solve this. For more information, see https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings
[AWS CodeBuild Plugin]   InsecurePlatformWarning
[AWS CodeBuild Plugin]   Downloading https://files.pythonhosted.org/packages/76/ae/44b03b253d6fade317f32c24d100b3b35c2239807046a4c953c7b89fa49e/itsdangerous-1.1.0-py2.py3-none-any.whl
[AWS CodeBuild Plugin] Collecting click>=5.1 (from Flask)
[AWS CodeBuild Plugin] /usr/local/lib/python2.7/dist-packages/pip/_vendor/urllib3/util/ssl_.py:137: InsecurePlatformWarning: A true SSLContext object is not available. This prevents urllib3 from configuring SSL appropriately and may cause certain SSL connections to fail. You can upgrade to a newer version of Python to solve this. For more information, see https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings
[AWS CodeBuild Plugin]   InsecurePlatformWarning
[AWS CodeBuild Plugin]   Downloading https://files.pythonhosted.org/packages/fa/37/45185cb5abbc30d7257104c434fe0b07e5a195a6847506c074527aa599ec/Click-7.0-py2.py3-none-any.whl (81kB)
[AWS CodeBuild Plugin] Collecting Jinja2>=2.10.1 (from Flask)
[AWS CodeBuild Plugin] /usr/local/lib/python2.7/dist-packages/pip/_vendor/urllib3/util/ssl_.py:137: InsecurePlatformWarning: A true SSLContext object is not available. This prevents urllib3 from configuring SSL appropriately and may cause certain SSL connections to fail. You can upgrade to a newer version of Python to solve this. For more information, see https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings
[AWS CodeBuild Plugin]   InsecurePlatformWarning
[AWS CodeBuild Plugin]   Downloading https://files.pythonhosted.org/packages/65/e0/eb35e762802015cab1ccee04e8a277b03f1d8e53da3ec3106882ec42558b/Jinja2-2.10.3-py2.py3-none-any.whl (125kB)
[AWS CodeBuild Plugin] Collecting MarkupSafe>=0.23 (from Jinja2>=2.10.1->Flask)
[AWS CodeBuild Plugin] /usr/local/lib/python2.7/dist-packages/pip/_vendor/urllib3/util/ssl_.py:137: InsecurePlatformWarning: A true SSLContext object is not available. This prevents urllib3 from configuring SSL appropriately and may cause certain SSL connections to fail. You can upgrade to a newer version of Python to solve this. For more information, see https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings
[AWS CodeBuild Plugin]   InsecurePlatformWarning
[AWS CodeBuild Plugin]   Downloading https://files.pythonhosted.org/packages/fb/40/f3adb7cf24a8012813c5edb20329eb22d5d8e2a0ecf73d21d6b85865da11/MarkupSafe-1.1.1-cp27-cp27mu-manylinux1_x86_64.whl
[AWS CodeBuild Plugin] Installing collected packages: Werkzeug, itsdangerous, click, MarkupSafe, Jinja2, Flask
[AWS CodeBuild Plugin] Successfully installed Flask-1.1.1 Jinja2-2.10.3 MarkupSafe-1.1.1 Werkzeug-0.16.0 click-7.0 itsdangerous-1.1.0
[AWS CodeBuild Plugin] /usr/local/lib/python2.7/dist-packages/pip/_vendor/urllib3/util/ssl_.py:137: InsecurePlatformWarning: A true SSLContext object is not available. This prevents urllib3 from configuring SSL appropriately and may cause certain SSL connections to fail. You can upgrade to a newer version of Python to solve this. For more information, see https://urllib3.readthedocs.io/en/latest/advanced-usage.html#ssl-warnings
[AWS CodeBuild Plugin]   InsecurePlatformWarning
[AWS CodeBuild Plugin] You are using pip version 9.0.3, however version 20.0.2 is available.
[AWS CodeBuild Plugin] You should consider upgrading via the 'pip install --upgrade pip' command.
[AWS CodeBuild Plugin] 
[AWS CodeBuild Plugin] 2020/01/26 21:50:28 Phase complete: INSTALL State: SUCCEEDED
[AWS CodeBuild Plugin] 2020/01/26 21:50:28 Phase context status code:  Message: 
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Entering phase PRE_BUILD
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Phase complete: PRE_BUILD State: SUCCEEDED
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Phase context status code:  Message: 
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Entering phase BUILD
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Running command echo Build started on `date`
[AWS CodeBuild Plugin] Build started on Sun Jan 26 21:50:29 UTC 2020
[AWS CodeBuild Plugin] 
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Running command echo Compiling the Python code...
[AWS CodeBuild Plugin] Compiling the Python code...
[AWS CodeBuild Plugin] 
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Running command python test_app.py
[AWS CodeBuild Plugin] ..
[AWS CodeBuild Plugin] ----------------------------------------------------------------------
[AWS CodeBuild Plugin] Ran 2 tests in 0.011s
[AWS CodeBuild Plugin] 
[AWS CodeBuild Plugin] OK
[AWS CodeBuild Plugin] 
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Phase complete: BUILD State: SUCCEEDED
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Phase context status code:  Message: 
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Entering phase POST_BUILD
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Running command echo Build completed on `date`
[AWS CodeBuild Plugin] Build completed on Sun Jan 26 21:50:29 UTC 2020
[AWS CodeBuild Plugin] 
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Phase complete: POST_BUILD State: SUCCEEDED
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Phase context status code:  Message: 
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Expanding base directory path: .
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Assembling file list
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Expanding .
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Expanding file paths for base directory .
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Assembling file list
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Expanding web.py
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Expanding appspec.yml
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Expanding templates/layout.html
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Expanding templates/test.html
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Expanding scripts/mkdir.sh
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Expanding scripts/start_flask.sh
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Expanding scripts/stop_flask1.sh
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Expanding scripts/stop_flask.py
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Found 8 file(s)
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Phase complete: UPLOAD_ARTIFACTS State: SUCCEEDED
[AWS CodeBuild Plugin] 2020/01/26 21:50:29 Phase context status code:  Message: 
Build step 'AWS CodeBuild' changed build result to SUCCESS
File Delete Operation:
/var/lib/jenkins/workspace/CodeDeployApp/README.md deleting....
Success.
/var/lib/jenkins/workspace/CodeDeployApp/appspec.yml deleting....
Success.
/var/lib/jenkins/workspace/CodeDeployApp/buildspec.yml deleting....
Success.
/var/lib/jenkins/workspace/CodeDeployApp/requirements.txt deleting....
Success.
/var/lib/jenkins/workspace/CodeDeployApp/test_app.py deleting....
Success.
/var/lib/jenkins/workspace/CodeDeployApp/web.py deleting....
Success.
HttpMethod: GET
URL: http://s3-eu-central-1.amazonaws.com/jenkins-deploy-codedeploybucket-pkh6sc9u2d0z/codebuild-artifact.zip
Sending request to url: http://s3-eu-central-1.amazonaws.com/jenkins-deploy-codedeploybucket-pkh6sc9u2d0z/codebuild-artifact.zip
Response Code: HTTP/1.1 200 OK
Success code from [100‥399]
Saving response body to /var/lib/jenkins/workspace/CodeDeployApp/codebuild-artifact.zip
Unzip File Operation:
Unzipping codebuild-artifact.zip to /var/lib/jenkins/workspace/CodeDeployApp
Unzip completed.
File Delete Operation:
/var/lib/jenkins/workspace/CodeDeployApp/codebuild-artifact.zip deleting....
Success.
Zipping files into /tmp/#6-3014934060827764852.zip
Uploading zip to s3://jenkins-deploy-codedeploybucket-pkh6sc9u2d0z/#6-3014934060827764852.zip
Registering revision for application 'jenkins-deploy-DemoApplication-1W9OF84OG45V'
Creating deployment with revision at {RevisionType: S3,S3Location: {Bucket: jenkins-deploy-codedeploybucket-pkh6sc9u2d0z,Key: #6-3014934060827764852.zip,BundleType: zip,ETag: c896dfe7687efd8ba2cb83a8905f765a},}
Finished: SUCCESS
```



