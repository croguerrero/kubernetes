Install Jenkins with YAML files

This section describes how to use a set of YAML (Yet Another Markup Language) files to install Jenkins on a Kubernetes cluster. The YAML files are easily tracked, edited, and can be reused indefinitely.

Create Jenkins deployment file
Copy the contents here into your preferred text editor and create a jenkins-deployment.yaml file in the “jenkins” namespace we created in this section above.

This deployment file is defining a Deployment as indicated by the kind field.

The Deployment specifies a single replica. This ensures one and only one instance will be maintained by the Replication Controller in the event of failure.

The container image name is jenkins and version is 2.32.2

The list of ports specified within the spec are a list of ports to expose from the container on the Pods IP address.

Jenkins running on (http) port 8080.

The Pod exposes the port 8080 of the jenkins container.

The volumeMounts section of the file creates a Persistent Volume. This volume is mounted within the container at the path /var/jenkins_home and so modifications to data within /var/jenkins_home are written to the volume. The role of a persistent volume is to store basic Jenkins data and preserve it beyond the lifetime of a pod.

Exit and save the changes once you add the content to the Jenkins deployment file.

Deploy Jenkins
To create the deployment execute:

$ kubectl create -f jenkins-deployment.yaml -n jenkins

The command also instructs the system to install Jenkins within the jenkins namespace.

To validate that creating the deployment was successful you can invoke:

$ kubectl get deployments -n jenkins
Grant access to Jenkins service
We have a Jenkins instance deployed but it is still not accessible. The Jenkins Pod has been assigned an IP address that is internal to the Kubernetes cluster. It’s possible to log into the Kubernetes Node and access Jenkins from there but that’s not a very useful way to access the service.

To make Jenkins accessible outside the Kubernetes cluster the Pod needs to be exposed as a Service. A Service is an abstraction that exposes Jenkins to the wider network. It allows us to maintain a persistent connection to the pod regardless of the changes in the cluster. With a local deployment, this means creating a NodePort service type. A NodePort service type exposes a service on a port on each node in the cluster. The service is accessed through the Node IP address and the service nodePort. A simple service is defined here:

This service file is defining a Service as indicated by the kind field.

The Service is of type NodePort. Other options are ClusterIP (only accessible within the cluster) and LoadBalancer (IP address assigned by a cloud provider e.g. AWS Elastic IP).

The list of ports specified within the spec is a list of ports exposed by this service.

The port is the port that will be exposed by the service.

The target port is the port to access the Pods targeted by this service. A port name may also be specified.

The selector specifies the selection criteria for the Pods targeted by this service.

To create the service execute:

$ kubectl create -f jenkins-service.yaml -n jenkins

To validate that creating the service was successful you can run:

$ kubectl get services -n jenkins
NAME       TYPE        CLUSTER-IP       EXTERNAL-IP    PORT(S)           AGE
jenkins    NodePort    10.103.31.217    <none>         8080:32664/TCP    59s