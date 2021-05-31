# apiSampleJava
Simple demo application used for the implementation of the DevOps cycle.

# Local development


For local development developers can use the Scaffold tool, this tool automates the deployment process with a simple pipeline defined in scaffold.yaml file. Change the image name variable to upload the container to dockerhub.

~~~
$ vi skaffold.yaml
~~~

Run the script and this script will automatically compile the Maven Project locally and deploy it using helm in the Kubernetes cluster configured by default on their computer.

~~~
$ ./watch.sh
~~~


# Documentation

(https://github.com/alazuga/apiSampleJava/tree/master/docs/SRE_Challenge.pdf)

This document explains the DevOps model proposed for the continuous development of applications, the deployment of applications, the architecture of the monitoring stack in HA and the application for collecting logs in Kubernetes clusters