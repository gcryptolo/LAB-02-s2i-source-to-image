# LAB-02-s2i-source-to-image
Second Lab to understand S2I on RedHat Openshift

## Source to Image (S2I) on OpenShift

Welcome to second lab, if you are here, you probably have already completed the first lab. In this lab, we will inspect S2I widely used in first lab in order to customize the build of simple web application and deploy it on OpenShift.
## Prerequisites
- OpenShift CLI (oc) installed and configured
- Access to an OpenShift Sandbox
- Basic knowledge of OpenShift and image build concepts
- Familiarity with Docker and containerization concepts
- GitHub account 
- LAB-01 completed 

## Lab Overview

In the first LAB, we used BuildConfig to build a simple web application using S2I. In this lab, we will create a custom S2I image and use it to build and deploy a web application on OpenShift. We will also explore the S2I process in detail and understand how it works under the hood.
When you push commit on GitHub, it will trigger a webhook that will start build on OpenShift. The build will use the default S2I script to build the web application and deploy it on OpenShift. All the build process is under the hood and you will not see it. You will only see the final result, which is a running web application on OpenShift.
But if we want to customize the build process, we need to understand how S2I works and how to create a custom S2I image. In this lab, we will create a custom S2I script that will be used to build a simple web application image. We will also explore the S2I process in detail and understand how it works.

. In this lab, we will create a custom S2I image that will be used to build the web application. We will also explore the S2I script from java21 default image and simply run:

```bash
mvn clean package
echo "Maven custom build Success"

```

In the log of the build process, you will see the output of the command. 


## What is S2I and how it work in Openshift?

S2I is an abstraction layer on top of Docker that allows you to build and deploy applications in a containerized environment. It provides a simple way to create and manage container images for your applications. S2I is designed to be used with OpenShift, but it can also be used with other container orchestration platforms.
In our case, we can add s2i scripts that override the default behavior of S2I inside Openshift. 
We can add our own scripts to customize the build process and add additional functionality to the S2I image. This allows us to create a custom S2I image that is tailored to our specific needs.
In the previus LAB Openshift use default S2I script to build the web application witch consists of the following steps:

1. **mvn clean package**  for build the sorce code
2. **cp app.jar /deployments/app.jar** for copy the jar file to the /deployments directory
3. **java -jar /deployments/app.jar** for run the application


You can see all process in build log tab of your build made in previous LAB.

Now we want to override this build adding our custom beavior. We will create a custom S2I script that will be used to build the web application. 

## Steps

1. **Create a new GitHub repository**: Create a new GitHub repository or use the one provided in the first LAB.If you create a new GitHub repository, clone it to your local machine and copy this repo content to your new repo.
2. **S2I script**: customize our build
3. **Push the changes to GitHub**: Push the changes to your GitHub repository. This will trigger a webhook that will start a build on OpenShift.
4. **Monitor the build**: Monitor the build process on OpenShift. You can view the build logs and see the progress of the build with your change.


### 1 Create a new GitHub repository
- Create a new GitHub repository or use the one provided in the first LAB. 
- If you are using the one provided in the first LAB add .s2i directory and copy the content of .s21 directory of this repo to your new repo directory.
- If you clone the repo you have to reproduce first the LAB-01 artifacts using script and YAML file in this repo. Remember change namespace with your project name (see LAB-01), there are all you need :-D.

### 2 S2I script

In order to customize the build we have to create a new directory called .s2i/bin in the root of our project. 
In this directory we will add our custom S2I scripts inside bin directory so Openshift discover automatically the scripts.


```bash
.
├── .s2i/
│   └── bin/
│       ├── assemble
│       ├── run
│       ├── usage
├── src/
├── pom.xml
```

The S2I scripts are used to customize the build process and add additional functionality to the S2I image. The S2I scripts are executed in the following order:
- **assemble**: This script is used to build the application. It is executed after the source code is copied to the image. The default behavior of S2I is to run the `mvn clean package` command to build the application. We will override this behavior by creating our own assemble script.
- **run**: This script is used to run the application. It is executed after the assemble script. The default behavior of S2I is to run the `java -jar target/*.jar` command to run the application. We will override this behavior by creating our own run script.
- **usage**: This script is used to display the usage information for the S2I image. It is executed when the S2I image is run without any arguments. The default behavior of S2I is to display the usage information for the S2I image. We will override this behavior by creating our own usage script.

Other possible scripts are:
- save-artifacts: This script is used to save the artifacts generated by the build process. It is executed after the assemble script. The default behavior of S2I is to save the target directory as an artifact.
- preassemble: This script is used to perform any pre-assembly tasks. It is executed before the assemble script. The default behavior of S2I is to do nothing.
- postassemble: This script is used to perform any post-assembly tasks. It is executed after the assemble script. The default behavior of S2I is to do nothing. 
- prestart: This script is used to perform any pre-start tasks. It is executed before the run script. The default behavior of S2I is to do nothing. 
- poststart: This script is used to perform any post-start tasks. It is executed after the run script. The default behavior of S2I is to do nothing.
- prestop: This script is used to perform any pre-stop tasks. It is executed before the S2I image is stopped. The default behavior of S2I is to do nothing. 
- poststop: This script is used to perform any post-stop tasks. It is executed after the S2I image is stopped. The default behavior of S2I is to do nothing. 


Let's check our **assemble** script:
```bash
#!/bin/bash
set -e

cd /tmp/src   # ! Important : Change to the source directory

echo "Maven custom build"
mvn clean package -DskipTests

echo "Copying jar file to /opt/app-root/src"
cp target/lab02-sample-hello-work-1.0.0.jar /deployments/app.jar

echo "Maven custom build Success"
```

Lets check our **run** script:
```bash

#!/bin/bash
set -e

echo "Start Spring Boot..."
exec java -jar /deployments/app.jar   # check this is where we copy the jar file.
```
**IMPORTANT**:

Change the permission of the scripts to make them executable. You can do this by running the following command in your terminal:

```
PS C:\Users\redhat\LAB-02-s2i-source-to-image> git add --chmod=+x .s2i/assemble
PS C:\Users\redhat\LAB-02-s2i-source-to-image> git add --chmod=+x .s2i/run
PS C:\Users\redhat\LAB-02-s2i-source-to-image> git add --chmod=+x .s2i/usage
```



Modify the scripts to suit your needs. For example, you can add some echo statements.
PS: pay attention on directory permission, using /deployments/ as directory to copy the jar file it works fine but if you change directory you need a permission.

### 3 Push the changes to GitHub

Now pushing the changes to your GitHub repository will trigger a webhook that will start a build on OpenShift. 

### 4 Monitor the build

Finally we can check the build process on OpenShift. You can view the build logs and see the progress of the build with your change.
You can also check the build logs in the OpenShift web console. Go to the Builds tab and select the build you want to monitor. You can view the build logs and see the progress of the build.

![img.png](doc%2Fimg%2Fimg.png)




## Conclusion
In this lab, we have learned how to create a custom S2I image and use it to build and deploy a web application on OpenShift. We have also explored the S2I process in detail and understood how it works under the hood. We have created a custom S2I script that will be used to build the web application. We have also explored the S2I process in detail and understood how it works under the hood.

**Remember** : Openshift do a lot of magic for you, but you need to understand how to this magic happen, one example is Deployment of an ImageStream, if you check the YAML of deployment you can see the image tag used for the pod, but how can Openshift detect a new image and update the tag of image? If you read the YAML there are an annotation to create a webhook that will trigger a new build when a new image is pushed to the image stream.
This is done by using the `imageChangeTrigger` annotation in the deployment configuration. This annotation tells OpenShift to watch for changes to the image stream and trigger a new build when a new image is pushed to the image stream.