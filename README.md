# LAB-02-s2i-source-to-image
Second Lab to understand S2I on RedHat Openshift

## Source to Image (S2I) on OpenShift

Welcome to second lab, if you are here, you probably have already completed the first lab. In this lab, we will be using inspect S2I widely used in first lab to customiza the build of simple web application and deploy it on OpenShift.
## Prerequisites
- OpenShift CLI (oc) installed and configured
- Access to an OpenShift Sandbox
- Basic knowledge of OpenShift and image build concepts
- Familiarity with Docker and containerization concepts
- GitHub account 
- LAB-01 completed 

## Lab Overview

In the first LAB, we used BuildConfig to build a simple web application using S2I. In this lab, we will create a custom S2I image and use it to build and deploy a web application on OpenShift. We will also explore the S2I process in detail and understand how it works under the hood.
When you push commit on GitHub, it will trigger a webhook that will starta build on OpenShift. The build will use the custom S2I image to build the web application and deploy it on OpenShift. All the build process is under the hood and you will not see it. You will only see the final result, which is a running web application on OpenShift.
But if we want to customize the build process, we need to understand how S2I works and how to create a custom S2I image. In this lab, we will create a custom S2I image that will be used to build a simple web application. We will also explore the S2I process in detail and understand how it works.

S2I is an abstraction layer on top of Docker that allows you to build and deploy applications in a containerized environment. It provides a simple way to create and manage container images for your applications. S2I is designed to be used with OpenShift, but it can also be used with other container orchestration platforms.
In our case, we can add s2i scripts that override the default behavior of S2I. We can add our own scripts to customize the build process and add additional functionality to the S2I image. This allows us to create a custom S2I image that is tailored to our specific needs.
In the previus LAB Openshift use default S2I image to build the web application. In this lab, we will create a custom S2I image that will be used to build the web application. We will also explore the S2I script from java21 default image and simply run:

```bash
mvn clean package
<other command needed>

```
You can see all process in build log tab of your build made in previus LAB.

Now we want to override this build adding our custom beavior. We will create a custom S2I image that will be used to build the web application. 

##Steps

1. **Create a new GitHub repository**: Create a new GitHub repository or use the one provided in the first LAB.If you create a new GitHub repository, clone it to your local machine and copy this repo content to your new repo.
2. **Customize S2I script**: customize our build
3. **Push the changes to GitHub**: Push the changes to your GitHub repository. This will trigger a webhook that will start a build on OpenShift.
4. **Monitor the build**: Monitor the build process on OpenShift. You can view the build logs and see the progress of the build with your change.


### 1 Create a new GitHub repository
- Create a new GitHub repository or use the one provided in the first LAB. If you are using the one provided in the first LAB add s2i directory and copy the content of s21 directory of this repo to your new repo directory.

### 2 Customize S2I script

