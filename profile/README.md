## Hi there 👋

1.0 Purpose 
The purpose of the application is to provide dog trainers a platform on which they can record, and maintain notes on the dogs they are training. These notes are to comment on behavior and training progress. 

2.0 System  
"CloudFormationArchitecture" provisions the AWS resources to host the appliction, such as the VPC, Kubernetes Cluster, and a management bastion to control it from. 

"KubernetesArchitecture" orchestrates the containerised application, it will pull the images of the different microservices 
off of docker hub, organize routing, handle scaling of nodes as well as pods, and host the Load Balancer for public access. It also features a monitorig suit for log and metric aggregation, analysis and alerting. 

"Alert-analyzer" and application dedicated to listening for alerts is also deployed to the Kubernetes cluster, it uses Gunicorn to recieve handle alert threads and then intergrates with Loki and OpenAI to perform log analysis whenever an alert is triggered. It then takes this analysis and sends it to AWS SNS for robust alerting. 

"Frontend" and "registry" are the microservices comprising the application logic. 
- "Frontend" is the GUI and is hosted on angular. 
- "registry" is the first of many backend microservices and is hosted on SringBoot. 

4.0 CI/CD 
The CloudFormationArchitecture is deployed to AWS automatically through Code Pipeline whenever a "push" occures. It saves the various CloudFormation files to S3, uses code build to construct an actionable master doc, creates a change set and then deploys, rollsback and implements the inrastructure. 

5.0 Next Steps 
I plan on creating a deployment on Elastic Beanstalk so that I develop the application itself in a cost effective manner and use a jenkins pipeine for automated deployment and testing. 

I also plan on making the kubernetes deployment more robust, the nodes need to be hardened for security, the Kubernetes DNS needs restrictions for zero trust, a full suit of alerting rules need to be implemented for greater transparency and peace of mind. 
