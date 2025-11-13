Beyond these files, a secret also needs to be created:
''kubectl create secret generic jenkins-creds --from-literal=ADMIN_PASSWORD=admin123''

For accessing service by url: '''http://service-name.namespace.svc.cluster.local:8080'''

For forwarding port: ''kubectl port-forward -n jenkins pod/jenkins-pod-name 8080:8080''


# To get started run "kubectl apply -k ."


Project info:

- The primary objective here is to setup a jenkins controller as a kubernetes deployment with configuration (uid/pwd) preloaded.
- This is achieved using jenkins jcasc - configuration as a code plugin.
- The other objective - achieved post setup is to ensure that kubernetes pods can act as agents/slaves for the controller.
- This is achieved using kubernetes plugin in jenkins.

# Setup
Once kubernetes is up, apply the manifest files (Eg using "kubectl apply -k . ". This sets up a jenkins user with the password mentioned in the secret.
Please note that the secret is being passed here directly in the manifest file which is not a recommended practice. 

You can connect to the controller on localhost:8080 as we have the ports mapped as such with service type as loadbalancer. 

1. After login go to plugins and install kubernetes plugin.
2. Go to manage jenkins -> clouds -> new cloud -> add a name 'kubernetes'
3. Since we are running master and agents on the same cluster, steps are going to be less. Go to configure cloud (kubernetes), update namespace and test connection. 
4. Once connection is tested and successful, update jenkins url to http://<service-name>.<namespace>.svc.cluster.local:8080
5. Create pod and container templates. For pod template add a label with key jenkins and value agent. 
6. For pod template create one- add name, namespace and label. Create a container template under the same pod- use the image "jenkins/inbound-agent:latest". 

Now once all this is completed, jobs can be run with a pod as agent, just mention the pod label with the matching label provided earlier. 