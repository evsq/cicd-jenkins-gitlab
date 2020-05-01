# Install plugins: 
workflow-aggregator, gitlab-plugin, kubernetes

# Dynamic agents
You need to disable build master agents
Go to Manage Jenkins -> Manage Nodes and Clouds -> Configure (Click the gear icon associated with the master row) -> set # of executors to 0 -> Save


# Configuring kubernetes plugin

Go to Manage Jenkins -> Manage Nodes and Clouds -> Configure Clouds -> Add a new cloud (Kubernetes) -> Kubernetes cloud details ->

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

Containers -> Container template
"Name": jnlp (This name is neccesary, otherwise your agents will not work)
"Docker image": jenkins/jnlp-slave
"Working directory": /home/jenkins
"Command to run": Delete the value here
"Arguments to pass to the command": Delete the value here

Click "Save"


# Configuring Gitlab-Jenkins integration




