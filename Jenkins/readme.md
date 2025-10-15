## Main readme document for Jenkins sessions


Repos created for the same:

# Shared library 
https://github.com/hkm7/calculator-application-shared-library

# Calculator - python application (for multibranch and regular pipelines)
https://github.com/hkm7/calculator-application



## Other misc notes:
Notes
- Jenkins
- CI/CD
- Architecture
- Jenkinsfile
- Installation (docker)
- Sample pipelines
  
#################################################################

Jenkins installation:

#Install jdk17 & jdk21-jre
sudo apt-get install openjdk-17-jre openjdk-21-jre -y

# jenkins installation for ubuntu:
find at https://www.jenkins.io/doc/book/installing/linux/ 
```
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```

ngrok installation for ubuntu:

https://ngrok.com/downloads/linux

```
curl -sSL https://ngrok-agent.s3.amazonaws.com/ngrok.asc \
  | sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null \
  && echo "deb https://ngrok-agent.s3.amazonaws.com bookworm main" \
  | sudo tee /etc/apt/sources.list.d/ngrok.list \
  && sudo apt update \
  && sudo apt install ngrok
```

# **Repo:**
https://github.com/hkm7/jenkins-demo

Homework:
1. Setup a pipeline (both freestyle and regular)
2. Setup a repo containing the following:
  - Jenkinsfile with the pipeline code
  - A requirements.txt file for python containing 2 or 3 python packages
  - A python application app.py that uses these packages (use 'import <package_name')
3. Run the created pipeline (both freestyle and regular) to perform the following actions:
  - Install the python packages
  - Run the python application
  - display the output in console
  - Also add post-build steps to indicate whether success or failure with codes 200 for success and 400 for failure.

Session 3 HW:
Setup a multibranch pipeline for the python application build as discussed. 
((For a sample refer repo: Calculator application(https://github.com/hkm7/calculator-application)))
