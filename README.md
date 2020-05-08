# knative-quarkus-openshift-demo
This project demonstrates the running of serverless workloads on OpenShift that scales up or scales to zero based on-demand.   

## Prerequisites/Requirements
* 1x running OpenShift cluster (Tested on v4.4)

## Instructions
1. [Setup OpenShift Serverless](#setup-openshift-serverless)
2. [Deploy the application](#deploy-the-application) 
3. [Running the demo](#running-the-demo)  

## Setup OpenShift Serverless
### Install OpenShift Serverless Operator
* On the web console, under *openshift-operators* namespace, Operators -> OperatorHub -> OpenShift Serverless Operator

### Create a new project called *knative-serving*
```
$ oc create new-project knative-serving
``` 

### Install Knative Serving
* On the web console, under *knative-serving* namespace, Operators -> Installed Operators -> Knative Serving -> Create Knative Serving

* Use the following yaml:
```
apiVersion: operator.knative.dev/v1alpha1
kind: KnativeServing
metadata:
  name: knative-serving
  namespace: knative-serving
spec: {}

```

### Install Knative command line (kn)
* Download the file from [here](https://mirror.openshift.com/pub/openshift-v4/clients/serverless/latest/)

* Unpack the file
```
$ tar -xf <file>
```

* Move the binary and make it executable
```
$ cp kn /usr/local/bin/
$ chmod +x /usr/local/bin/kn
```
## Deploy the application
* Create a new project
```
$ oc create new-project knative-quarkus-demo
```

* Use kn cli to deploy the application
```
kn service create helloworld --image image-registry.openshift-image-registry.svc:5000/quarkus-helloworld/quarkus-helloworld@sha256:82c9f2e2c22c1eb2d7869f4291ce1af6bfa2bbcd8cad027357b80682b0ca18b2
```
Note: Can build the image of the application first and upload to the registry. For simplicity, image SHA is used to avoid registry authentication.

## Running the demo
* Ensure that there is no pod running:

* Get the route and hit the endpoint to trigger the pod deployment
```
$ oc get routes 
NAME                                   URL                                                                                           READY   REASON
route.serving.knative.dev/helloworld   http://helloworld-knative-quarkus-demo.apps.cluster-ngjj-5027.ngjj-5027.example.opentlc.com   True 

$ curl http://helloworld-knative-quarkus-demo.apps.cluster-ngjj-5027.ngjj-5027.example.opentlc.com
Hello World!

$ oc get pods
NAME                                            READY   STATUS    RESTARTS   AGE
helloworld-yltkd-1-deployment-d5449d8cd-25hcz   2/2     Running   0          12s
```

* After awhile, you will see that the pod is scaled to zero.


