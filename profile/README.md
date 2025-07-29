# Doggy App -- A behavioral tracking platform for dog trainers

## 1.0 Purpose

The purpose of the application is to provide dog trainers a platform on which they can record, and maintain notes on the dogs they are training. These notes are to comment on behavior and training progress, and are sharable across trainers, walkers, and carers we well as with the owners. (My sister is a Dog trainer and approached with the "I have a great idea", this is a meandering 2 years and 3 certifications later).

## 2.0 Table of Contents

**"CloudFormationArchitecture"** provisions the AWS resources to host the appliction; the VPC; the Kubernetes Cluster; and the bastion host to manage it. While also provisioning security groups, keys, IAM roles, and storage for these things to run effectively and securely. The startup script written in the EC2 instance does a lot of leg work it automatically installs, git (and clones the KubernetesArchitecture repo), AWS CLI V2, Python-10 (and needed libraries), Ansible (and needed modules), Kubectl, and EKS (which doesn't end up being needed), it also automatically creates the AWS configuration files needed to access AWS CLI, it sets the .kube config with the proper context of the doggy-kube-cluster, runs the ansbile script from the py-10 virtual environment creating the entire application environment, and then populates the .bashrc file with helpful commands that automatically sets the kube environment to the doggy-cluster, and sets the virtual environment to py-10, so that everything is ready to go everytime you log into ec2-user (you can only run the latest kube enabled version of Ansible with py-10, and the native ec2 python version is 3, so you need a virtual environment for py-10 that doeesn't interfere with the native logic). It also sets commands "setup" and "teardown" that run the ansible setup.yaml and ansile teardown.yaml files from any directory. It also creates a log file for the whole process in /var/log/user-data.log. It uses the set -e command to stop the script if any of the commands fail so that it is safer to run. 

**"KubernetesArchitecture"** orchestrates the containerised application, it will pull the images of the different microservices off of docker hub, network them together, and provide an nginx ingress controller through which the UI can be accessed, an autoscaler will also be deployed to dynamically scale this application. It also uses helm to create a monitoring suit that will collect and aggreagte logs and metrics using Prometheus, Loki and Grafana. An "Alert Analyzer" webhook has also been written with Python that will trigger on alerts from Promethius to query the logs from Loki, send them to OpenAI and recieve a LLM generated analysis, this analysis will be forwarded to a topic generated in AWS Simple Notification Service that is configured with an email address. All related IAM roles, permissions and AWS services are written and deployed through CloudFormation. It contains an Ansible playbook that will configure this whole environment with one command with indeptency and using the relevant modules whever possible, and lastly a second playbook which will tear it down so that the root stack of the CloudFormationAchitecture can be deleted from the AWS console, leaving no remnance.

**"Frontend", and "registry"**, are the microservices comprising the application logic.  
- "Frontend" is the GUI were users (dog trainers) will navigate and place their requests.  
- "registry" is where server side logic will be performed, eventually making requests to a DynamoDB database.

## 3.0 Deployment

The Architecture built foundationally on AWS CloudFormation so you can see how to deploy it in the CloudFormationArchitecture README.

## 4.0 CI/CD and testing

**"CloudFormationArchitecture"** - this has been deployed to AWS through CodePipeline. The buildspec.yml is viewable in the CloudFormationArchitecture, as well as the master.yaml which it pulls from. The Pipeline itself was built via the console, but in includes changesets (that allow for changes in code to be applyed via the rollback of only changed elements and their dependencies, not the rollback of the entire deployemnt), a webhook to the GitHub repository which triggers on every "git push", and a S3 bucket and ec2 where the the project is saved and then built for deployment by AWS CodeBuild.

## 5.0 Security considerations and best practices

Each section had their own security consideration that I delve into in their respective README's in the CloudFormationArchitecture repository and the KubernetesArchitecture repository.

## 5.0 Demo 

This is the full working application with all pods running. 

![Alt text](https://github.com/DoggyApp/.github/blob/main/Screenshot%20from%202025-07-14%2010-19-22.png)

This is the application running, you can see the URL is being served by an AWS load balancer. 

![Alt text](https://github.com/DoggyApp/.github/blob/main/Screenshot%20from%202025-07-14%2010-21-43.png) 

Here is the sign that the application is properly connecting to the back and becuase the API call too the backend from the "test" button recieved "working". 

![Alt text](https://github.com/DoggyApp/.github/blob/main/Screenshot%20from%202025-07-14%2010-22-38.png) 

Here is the Simple Notification Service spinning up correctly and serving a confirmation request to my email. This happens automatically on the start up of the environment, and it done by the ansible script. 

![Alt text](https://github.com/DoggyApp/.github/blob/main/Screenshot%20from%202025-07-14%2010-27-49.png) 

Here is the alert-analyzer correctly serving alerts through OpenAI, you can see that OpenAI correctly identified that the problem was that "Someone pressed the button" which is the hard coded exception that is called when you press the "test backed error" button on the webapp. 

![Alt text](https://github.com/DoggyApp/.github/blob/main/Screenshot%20from%202025-07-14%2011-12-47.png) 

Here you can see that client-side routing works, I typed in "dev-server" into the URL because that is a common URL that bots scanning the internet will try to hit, but it will redirect to the Angular "not-found" component given any URL that does not match an nginx ingress defined path. This was helpful becuase it would trigger email alerts with errors everytime this happened, so now the alerts are quieter, becuase they are handled in browser. (There is also rate limiting configured in the ingress that will hopefully stem some of these bad requests). 

![Alt text](https://github.com/DoggyApp/.github/blob/main/Screenshot%20from%202025-07-14%2011-50-40.png)

Here is Grafana serving as it should be, for security and to minimize points of contact with the interent I am port forwarding from my Managment bastion to my local computer, which is why it is served on localhost 3000 (instructions for port forwarding are in the KubernetesArchitecture README). You will be able to explore and see that the data source is correctly set up for Loki, and see alerts. 

![Alt text](https://github.com/DoggyApp/.github/blob/main/Screenshot%20from%202025-07-14%2011-05-33.png) 

Here you can see the CPU usage of the containers in my default namespace, this being the registry, webapp and ingress that are running at the moment. 

![Alt text](https://github.com/DoggyApp/.github/blob/main/Screenshot%20from%202025-07-14%2011-47-33.png)


## 6.0 Roadmap (in no general order...)

- Build a dedicated testing environment for the application on ElasticBeanstalk and intergrate a Jenknins pipeline for the frontend and backend, so that proper development can begin on the application itself.   
  > It needs the core features of the application built   
  > Also Development of the application shouldn't happen in the production environment both for cost and for security.
  > It would be ideal if I could also feature a staging environment where the application could automatically deploy to production after the tests have passed, I would be shocked if Jenkins couldn't this. 

- Deep dive into the monitoring suit...  
  > Collect logs and forward alerts not just from the frontend and backend but also from other namespaces and pods.  
  > Learn more about the different metrics and how to interpret them and use them to optimize.  
  > Practice these skills in a security conscious way to alert to potential vulernabilites and exploitations.

- Perform a suit of testing...  
  > Load testing to ensure that the architecture can properly handle stress.  
  > Deep dive into the various forms of testing that an application needs (the monitoring suit will be helpful here...)

- Bolster security...
  > Learn how to use Nessus to perform security scans.  
  > Learn more about how to defend a Kubernetes architecture, such as...  
  > * preventing communication across namespaces  
  > * hardening nodes  
  > * intergrating security tools  
  > * deep diving into kubernetes security...  
  > Delving more into application security and AWS security to make sure that the whole structure is secure...
  > * Implement AWS WAF and maybe CloudWatch, or see what I can do with Grafana and Loki.
  > * Implement AWS security scanning features and see how they compare to Nessus (using multiple scanning tools provides defence in depth). 

- Potentially see if I can run the nodes, or the bastion on Spot Instances to lower costs, at the moment it's just a demo project, and there is no perminant storage on it.  
  > Create a helper tool that will identify the best spots to run the nodes and bastion, and can automatically redeploy it if I'm ejected. The ansible script is indepotent so I don't have to worry about the bastion start up script creating any issues with the cluster.

- Intergrate permanent storage on the cluster and see if I can start a Postgres database.

- Rewrite the CloudFormation code in Terraform, just so I can pick it up.

- With the guidance of a robust monitoring system and Nessus, intergrate a payment system in case I decide to deploy something monetizeable. This is a longterm stretch ambition.  
  > Develop login with JPT tokens.  
  > Use the Nessus security scans to enure that I am PCI compliant.

## 7.0 License

This code is primarily for recruiters to evaluate my skills and because I find it fun, I don't imagine people would find much use in? But if anyone you would like to then contact me at idanzigm@gmail.com to ask permission.

---

## Appendix

### 1.0 Current knowedge base

- Managing and Troubleshooting PCs, Exams 220-1101 and 220-1102. Mike Meyers (A+ certified COPM001022717560)  
- CCNA 200-301 Offical Cert Guide. Wendell Odom (CCNA certified CSCO14594715)  
- AWS Cloud Practitioner certified NLG17CCC7EBQQZWV  
- LFS101x: Introduction to Linux. Linux Foundation 4dfe9e9668784cae9e0d59fdff01b35b
- Dive into Ansible - Beginner to expert in Ansible. James Spurin (Udemy).
- Kubernetes hands on - Deploy microservices to the AWS cloud. Richard Chesterwood. (Udemy) 
- Full stack java development boot camp at Revature (Springboot and Angular)  
  - Spring  
    > Unit and Intergratin testing in Springboot  
    > Spring MVC and Web  
  - Angular  
    > HTTPClient  
    > Components and services  
    > Unit testing  
    > HTML manipulation
  - Postgres
    > SQL joins
    > SQL Functions

### 1.1 Shout out 

Brett Gillett - Deploying EKS using CloudFormation - Session #1. Session#2 and Session #3 (YouTube). He got me started on this journey. 

### 2.0 Currently working on

- Security+ Study Guide. Mike Chappel and David Seidl.  
- Certified Kubernetes Administrator (CKA) Study Guide, In depth guidance. Benjamin Muschko.

### 3.0 Wishlist for this project

- Prometheus: Up & Running: Infrastructure and Application Performance Monitoring. Brian Brazil.  
- Kubernetes Best Practices. Brendan Burns, Eddie Villalba, Dave Strebel, Lachlan Evenson.
