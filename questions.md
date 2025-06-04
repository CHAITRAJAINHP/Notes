Shell scripting:
1) Write a script to check if file exists in particular path and it is writable or not.
2) Shell script to print list of input of names given in a file.
3) Shell script to create a file with name as date in pattern yyyy-MM-dd.
4) Shell script to mount disk on a given filesystem.
5) Command to do word count, grep specific word, get only first 10 lines or last 10 lines, sed to count number of lines.


Jenkins:
Write a basic pipeline script.

Docker:
Write a dockerfile to create image of a python app.

Kubernetes:
Can you write manifest file to create persistent volume.
Can you write manifest file template for pod or deployment.

Terraform:
Write a terraform script to create ec2 instance by creating vpc and subnet, how we can get the output.

Ansible:
Write a sample ansible playbook to install git.


1) What are the main stages of a CI/CD pipeline?
2) What tools have you used for CI/CD, and why?
3) How does a Jenkins pipeline work? Explain declarative vs scripted pipelines.
4) How would you automate a deployment process using Jenkins (or GitLab CI, CircleCI)?
5) How do you resolve a Git merge conflict?
6) What is the difference between a container and a virtual machine?
7) How does Docker networking work?
8) How would you perform a rolling update in Kubernetes?
9) What tools have you used for monitoring?
10) How does Prometheus work with Grafana?
11) What cloud services have you used in your DevOps work?
12) Tell me about a time you had a failed deployment. What did you do? - what failed, how did you track it, what resolution steps you applied, how much
time it took
13) How do you ensure zero-downtime deployments?
14) How do you manage environment differences between staging and production?

RollBack Strategies in DevOps
Microservice Architecture
For each service there is separate dockerfile.
Main Pipeline  Flow:  Docker Image - Docker Hub - Saving the current manifest file in archive artifacts - Updating the Kubernetes Manifest file with the latest docker image (GitHub Repo) - Flux CD - is then doing the deployment.
Images I have published in docker hub : image previous to the latest one - Update manifest file with the image previous to the latest one.
Second Pipeline (Rollback Pipeline ): Service name should be an input 
Query the image previous image from the latest one - image previous to the latest one - Update manifest file with the image previous to the latest one.
Fetch the archive artifacts file - Update manifest file
1) jq should be installed on the Jenkins agent where the pipeline is getting executed.
2) Before updating the Kubernetes Mainfest file in Main Pipeline  Flow , can we store the image in a file and store it in archive artifacts in jenkins, we can again update the  mainfest file image tag with the one that we have stored.
