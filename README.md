# Install plugins: 
workflow-aggregator, gitlab-plugin, kubernetes, docker-workflow

# Dynamic agents
You need to disable build master agents
Go to Manage Jenkins -> Manage Nodes -> master (Click the gear icon associated with the master row) -> set # of executors to 0 -> Save


# Configuring kubernetes plugin

Go to Manage Jenkins -> Configure System -> Cloud -> Add a new cloud (Kubernetes) -> Kubernetes cloud details ->

"Kubernetes URL field": enter ip:port from command "kubectl cluster-info | grep master"

"Credentials": Click "Add" and select Jenkins -> Select Kind "Secret text" and enter decoded service account token from command "kubectl get secret $(kubectl get sa jenkins -n cicd -o jsonpath="{.secrets[].name}") -n cicd -o jsonpath={.data.token} | base64 --decode"
Click "Test Connection"
If you faced error like "Jenkins -> 403 No valid crumb was included in the request"
Go to "Configure Global Security" -> CSRF Protection -> Select "Enable proxy compatibility"

"Jenkins URL": http://jenkins:8080
"Jenkins tunnel": jenkins-slave:50000


Click "Pod Templates"
"Name": jenkins-slave
Click "Pod Templates details"
"Namespace": cicd
"Labels": jenkins-slave
"Usage": Use this node as much as possible

Raw yaml for the Pod
Copy manifest from file jenkins/pod-template.yaml

Click "Save"


# Configuring Gitlab-Jenkins integration

Jenkins 

Create New Pipeline

New Item -> Enter an item name: gitlab -> Pipeline -> OK

Configure Pipeline

Go to Build Triggers section -> Click "Build when a change is pushed to GitLab. GitLab webhook URL: http://jenkins.cicd/project/gitlab" -> Advanced -> Secret token (Click Generate) -> Copy that token 


GitLab

You need to create 2 repo:
1 repo with your code
2 repo with your Jenkinsfile

Add user Jenkins to both repos

Create new user "Jenkins" -> Go to your Repository -> Settings -> Members -> Add user "Jenkins" with "Reporter" role

Configure Webhook

Go to your "code" Repository -> Settings -> Webhooks -> add URL: http://jenkins.cicd/project/gitlab -> add copied Secret Token from Jenkins -> Click to disable SSL verification -> Add Webhook

Test Webhook

Project Hooks (1)
http://jenkins.cicd/project/gitlab
Push Events SSL Verification: disabled

Click Test -> Push events -> You'll see a http code 200 if you did everything right (Hook executed successfully: HTTP 200)

Jenkins

Configure Pipeline

Go to Pipeline section -> Select Definition "Pipeline script from SCM" -> SCM: Git -> Copy your "jenkins" repo to "Repository URL" (in my case http://gitlab.cicd/root/jenkins.git) -> "Credentials" -> Add -> Type your jenkins username and password "Kind: Username with password" ID: gitlab-user Description: gitlab-user -> Save