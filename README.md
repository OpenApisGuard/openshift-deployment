#Openshift online 3 local war file deployment to Tomcat and MySQL docker containers

## Overview
This tutorial will walk you through on how to deploy WAR file from your local machine to Openshift online 3 and setup Tomcat and MySQL services in separate Kubernetes pods

## Deploy WAR file to Tomcat docker in Openshift online 3
### Prerequisites:
1. Sign up and login to Openshift online 3
2. Create a project
3. Install oc client locally
4. Login to Openshift online 3 with email and password
* oc login https://api.starter-us-COAST-NUM.openshift.com:443

### Create MySQL service
It will be better to create MySQL service first before creating Tomcat service so we will have MySQL database connection info in advance
1. Follow UI instruction to create MySQL service.  You can manually type in user name and password or let Openshift to auto generate them.  You can view your MySQL credentials in the web console under Resources -> Secrets -> <MYSQL_SERVICE_NAME>
2. For import DDL and data, mysqldump is not working but you can run SQL statements either in MySQL service terminal by clicking the Kubernetes pod icon -> terminal or open shell terminal locally by following <Open terminal locally> in Misc section listed below

### Deployment steps:
1. Create a new Tomcat service <SERVICE_NAME>
* oc new-build jboss-webserver30-tomcat8-openshift --name=<SERVICE_NAME> --binary=true
2. On local macahine
* Modify JDBC database connection URL and credentials in your property files and build the WAR file <helloWorld.war>
* Create new directories <PROJ_DIR_NAME>/deployments
* Rename <helloWorld.war> to ROOT.war and put it under <PROJ_DIR_NAME>/deployments/ROOT.war
* cd <PROJ_DIR_NAME>
3. Create docker image 
* oc start-build <SERVICE_NAME> --from-dir=. --follow=true --wait=true
4. Create Openshift application
* oc new-app <SERVICE_NAME>
5. Create a route so that you will have a public URL to access your app
* oc expose service <SERVICE_NAME>
6. After Kubernetes pod is created and Tomcat docker container started, you can view logs or go to online terminal by clicking on the pod image
7. It will take a minute or less for the route (public URL) to be accessible

* Optional: if you want to re-deploy your project using the same <SERVICE_NAME>
1. oc delete service <SERVICE_NAME>
2. oc delete buildconfigs.build.openshift.io <SERVICE_NAME>
3. oc delete imagestream <SERVICE_NAME>
4. oc delete route <SERVICE_NAME>

* Optional: Kubernetes has an easy way to let you set and change properties by using configmap
1. Create configmap from local properties file
oc create configmap config --from-file=application.properties

### Misc
* Open terminal locally
1. oc login https://api.starter-us-COAST-NUM.openshift.com:443
2. oc get pod
3. oc exec -it <POD_NAME> sh
