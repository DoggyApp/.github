# .github

Doggy App -- A behavioral tracking platform for dog trainers 

1.0 Purpose 

The purpose of the application is to provide dog trainers a platform on which they can record, and maintain notes on the dogs they are training. These notes are to comment on behavior and training progress, and are sharable across trainers, walkers, and carers we well as with the owners. (My sister is a Dog trainer and approached with the "I have a great idea", this is a meandering 2 years and 3 certifications later). 

2.0 Table of Contents  

"CloudFormationArchitecture" provisions the AWS resources to host the appliction; the VPC; the Kubernetes Cluster; and the bastion host to manage it. While also provisioning security groups and, keys, IAM roles, and storage for these things to run effectively and securely. 

"KubernetesArchitecture" orchestrates the containerised application, it will pull the images of the different microservices off of docker hub, network them together, and provide an nginx ingress controller through which the UI can be accessed, an autoscaler will also be deployed to dynamically scale this application. It also uses helm to create a monitoring suit that will collect and aggreagte logs and metrics using Prometheus, Loki and Grafana. An "Alert Analyzer" webhook has also been written with Python that will trigger on alerts from Promethius to query the logs from Loki, send them to OpenAI and recieve a LLM generated analysis, this analysis will be forwarded to a topic generated in AWS Simple Notification Service that is configured with an email address. All IAM roles, permissions and AWS services are written and deployed through CloudFormation. It contains an Ansible playbook that will configure this whole environment, and lastly a playbook which will tear it down so that the root stack of teh CloudFormationAchitecture can be deleted from the AWS console, leaving no remnance. 

"Frontend", and "registry", are the microservices comprising the application logic. 
- "Frontend" is the GUI were users (dog trainers) will navigate and place their requests.
- "registry" is where server side logic will be performed, eventually making requests to a DynamoDB database. 

3.0  Deployment 

The Architecture built foundationally on AWS CloudFormation so you can see how to deploy it in the CloudFormationArchitecture README. 

4.0 CI/CD and testing 

"CloudFormationArchitecture" - this has been deployed to AWS through CodePipeline. The buildspec.yml is viewable in the CloudFormationArchitecture, as well as the master.yaml which it pulls from. The Pipeline itself was built via the console, but in includes changesets (that allow for changes in code to be applyed via the rollback of only changed elements and their dependencies, not the rollback of the entire deployemnt), a webhook to the GitHub repository which triggers on every "git push", and a S3 bucket and ec2 where the the project is saved and then built for deployment by AWS CodeBuild. 

5.0 Security considerations and best practices 

Each section had their own security consideration that I delve into in their respective README's in the CloudFormationArchitecture repository and the KubernetesArchitecture repository. 

6.0 Roadmap (in this general order...) 

- Intergrate a pipeline for the frontend and backend using GitHub actions, so that proper development can begin on the application itself.
    > It needs the core features of the application built (though I'm more interested in the Kubernetes set up, a lot of this might end up being the last thing I do...)
- Deep dive into the monitoring suit...
    > Collect logs and forward alerts not just from the frontend and backend but also from other namespaces and pods. 
    > Learn more about the different metrics and how to interpret them and use them to optimize.
    > Practice these skills in a security conscious way to alert to potential vulernabilites and exploitations. 
- Perform a suit of testing... 
    > Load testing to ensure that the architecture can properly handle stress.
    > Deep dive into the various forms of testing that an application needs (the monitoring suit will be helpful here...)
- Bolster security... 
    > Learn more about how to defend a Kubernetes architecture, such as...
      * preventing communication across namespaces
      * hardening nodes
      * interegrating security tools
      * deep diving into kubernetes security...
    > Delving more into application security and AWS security to make sure that the whole structure is secure...
  - With the guidance of a robust monitoring system and application security, intergrate a payment system in case I decide to deploy something monetizeable.
        > Develop login with JPT tokens.
        > Everything else? 

7.0 License 

This code is primarily for recruiters to evaluate my skills and because I find it fun, I don't imagine people would find much use in? But if anyone you would like to then contact me at idanzigm@gmail.com to ask permission. 

Appendix 

1.0 Current knowedge base 

Managing and Troubleshooting PCs, Exams 220-1101 and 220-1102. Mike Meyers (A+ certified COPM001022717560) 
CCNA 200-301 Offical Cert Guide. Wendell Odom (CCNA certified CSCO14594715) 
(AWS Cloud Practitioner certified NLG17CCC7EBQQZWV) 
(LFS101x: Introduction to Linux. Linux Foundation 4dfe9e9668784cae9e0d59fdff01b35b) 
Full stack java development boot camp at Revature (Springboot and Angular) 
    - Spring 
        > Unit and Intergratin testing in Springboot 
        > Spring MVC and Web 
    - Angular 
        > HTTPCLient 
        > Components and services 
        > Unit testing 
        > HTML manipulation 

2.0 Currently working on 

Security+ Study Guide. Mike Chappel and David Seidl. 
Certified Kubernetes Administrator (CKA) Study Guide, In depth guidance. Benjamin Muschko. 

3.0 Wishlist for this project 
Prometheus: Up & Running: Infrastructure and Application Performance Monitoring. Brian Brazil. 
Kubernetes Best Practices. Brendan Burns, Eddie Villalba, Dave Strebel, Lachlan Evenson. 
