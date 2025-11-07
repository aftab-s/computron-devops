Beyond these files, a secret also needs to be created:
''kubectl create secret generic jenkins-creds --from-literal=ADMIN_PASSWORD=admin123''

http://<service-name>.<namespace>.svc.cluster.local:8080
