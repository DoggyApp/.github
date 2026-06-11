# Doggy App -- A behavioral tracking platform for dog trainers

## 1.0 Purpose

The purpose of the application is to provide dog owners a platform on which they can record, and maintain notes on their dogs and share those note with trainers and other dog carers, as well as between other owners. These notes will be editable by professinoal dog carers creating a single point of truth with high quality information on the dogs needs and personality that is available to those who needed it. This information will evolve though time to match the growth of the dog. 

## 2.0 Table of Contents

**"CloudFormationArchitecture"** provisions the AWS resources to host the appliction; the VPC; the Kubernetes Cluster; and the bastion host to manage it. While also provisioning security groups, keys, IAM roles, and storage for these things to run effectively and securely. The startup script written in the EC2 instanceautomatically installs the environment and configuration files needed to administer the cluster effectively. 

**"KubernetesArchitecture"** Contains the configuration of the orchetration environment, including helm charts, and CloudFormation stacks containing necessary periferals. Prewritten Ansible scripts automate their deployment.

**"Frontend", "Owner-Service", "User-Service", "Organization-Service"**, are the microservices comprising the application logic.  
- "Frontend" is the GUI were users (dog trainers) will navigate and place their requests.  
- "Owner-Service" serves owners of dogs and contains their API needs. 
- "User-Service" services dog carers ideally thought of as employees of organizations and services their needs in hte API.
- "Organization-service" business running in the dog caring industry, are able to register employees but have their own needs that need to be met by their own API end points.

- Learn more in the READMEs of each respective repository. 

## 3.0 Network Diagram 
Edit: I moved the MGMT Bastion to be private subnet   
Edit: There is a single Node group that spans both availability zones 
Edit: Elasting Container Registry is also provisioned though it does not hook in directly to this architecture. 
Edit: An ALB is connected to the Bastion EC2 for access to the GitLab pipeline deploying the microservices. 
![Alt text](https://github.com/DoggyApp/CloudFormationArchitecture/blob/main/DoggyAppCFArchitectureDiagram.jpg)

## 4.0 Deployment

The Architecture is built foundationally on AWS CloudFormation so you can see how to deploy it in the CloudFormationArchitecture README.

## 5.0 CI/CD and testing

**"CloudFormationArchitecture"** - this has been deployed to AWS through CodePipeline. The buildspec.yml is viewable in the CloudFormationArchitecture, as well as the master.yaml which it pulls from. The Pipeline itself was built via the console, but in includes changesets (that allow for changes in code to be applyed via the rollback of only changed elements and their dependencies, not the rollback of the entire deployemnt), a webhook to the GitHub repository triggers on every "git push".

## 6.0 Security considerations and best practices

Each section had their own security consideration that I delve into in their respective README's in the CloudFormationArchitecture repository and the KubernetesArchitecture repository.

## 7.0 Demo 

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


## 8.0 Roadmap (in no general order...)

- Build a dedicated staging environment for the application on ElasticBeanstalk. The cost of hosting it is $350 a month a cheaper alternative until most of the work is out of the way would be ideal.

- Develop Helm charts for environment-specific config management.

- Develop a backup strategy and test a restore for the EFS database, using Flyway or LiquidBase. 

- Deep dive into the monitoring suit...  
  > Collect logs and forward alerts not just from the frontend and backend but also from other namespaces and pods.  
  > Learn more about the different metrics and how to interpret them and use them to optimize.  
  > Practice these skills in a security conscious way to alert to potential vulernabilites and exploitations.

- Write unit tests, I usually perform test driven development, but I wanted this appllication live as quickly as possible so that I could start job hunting. reviewing the code in a more disciploined way and writing unit, intergration and end to end testing is a process that I need to do. 

- Perform a suit of testing...  
  > Stress testing to ensure that the architecture can properly handle load.
  > Fuzz testing to find bugs and vulnerabilities. 
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

- Rewrite the CloudFormation code in Terraform, just so I can pick it up.

- With the guidance of a robust monitoring system and Nessus, intergrate a payment system in case I decide to deploy something monetizeable. This is a longterm stretch ambition.  
  > Develop login with JPT tokens.  
  > Use the Nessus security scans to enure that I am PCI compliant.

## 9.0 License

This code is primarily for recruiters to evaluate my skills and because I find it fun, I don't imagine people would find much use in? But if anyone you would like to then contact me at idanzigm@gmail.com to ask permission.

---

## Appendix

### 1.0 Current knowedge base

- Managing and Troubleshooting PCs, Exams 220-1101 and 220-1102. Mike Meyers (A+ certified COPM001022717560)  
- CCNA 200-301 Offical Cert Guide. Wendell Odom (CCNA certified CSCO14594715)
- Security+ Study Guide. Mike Chappel and David Seidl. (Security+ certified COMP001022717560)
- AWS Cloud Practitioner certified NLG17CCC7EBQQZWV  
- LFS101x: Introduction to Linux. Linux Foundation 4dfe9e9668784cae9e0d59fdff01b35b
- Dive into Ansible - Beginner to expert in Ansible. James Spurin (Udemy).
- Kubernetes hands on - Deploy microservices to the AWS cloud. Richard Chesterwood. (Udemy)
- The Elements of Computing Systems, build a modern computer from first principles (check out my NandToTetris repo)
- Operating Systems Three Easy Pieces, Remzi and Andrea Arpaci-Dusseau (part I - Virtualization)
- Python Crash Course, Eric Matthes
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

- User Story Mapping, Jeff Peterson 
- Mastering Kubernetes (Gigi Sayfan) 

### 3.0 Wishlist for this project

- Prometheus: Up & Running: Infrastructure and Application Performance Monitoring. Brian Brazil.  
- Kubernetes Best Practices. Brendan Burns, Eddie Villalba, Dave Strebel, Lachlan Evenson.
