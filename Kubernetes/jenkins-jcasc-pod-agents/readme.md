Beyond these files, a secret also needs to be created:
''kubectl create secret generic jenkins-creds --from-literal=ADMIN_PASSWORD=admin123''

For accessing service by url: '''http://service-name.namespace.svc.cluster.local:8080'''

For forwarding port: ''kubectl port-forward -n jenkins pod/jenkins-pod-name 8080:8080''


##To get started run "kubectl apply -k ."
