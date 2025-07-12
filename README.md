# .github

Doggy App -- A behavioral tracking platform for dog trainers 

1.0 Purpose 
The purpose of the application is to provide dog trainers a platform on which they can record, and maintain notes on the dogs they are training. These notes are to comment on behavior and training progress. 

2.0 Table of Contents  
"CloudFormationArchitecture" provisions the AWS resources to host the appliction, such as the VPC, and Kubernetes Cluster. 

"KubernetesArchitecture" orchestrates the containerised application, it will pull the images of the different microservices 
off of docker hub, network them together, and provide a node port through which the UI can be accessed. It will (later) also be 
configured to provision AWS resources such as an API gateway and a dynamo DB database. 

"Frontend", and "registry", are the microservices comprising the application logic. 
- "Frontend" is the GUI were users (dog trainers) will navigate and place their requests.
- "registry" is where server side logic will be performed, eventually making requests to a DynamoDB database. 

3.0 Demo 


4.0 Architecture 
"CloudFormationArchitecture" 
"KubernetesArchitecture" 
"Frontend" – Node and Angualar 
"Registry" – SpringBoot; Spring Web; Java 17 

5.0 Deployment 
The Architecture in built on AWS so you can see how to deploy it in the CloudFormationArchitecture README. 

6.0 CI/CD and testing 
"CloudFormationArchitecture" - this has been deployed to AWS through CodePipeline 
"KubernetesArchitecture" - this has been deployed locally on MiniKube. However I plan on deploying a container hosting Jenkins 
to the Cluster so that deployment of the microservices can be automated. 
"Frontend"; "registry"; "APIGateway" - This has been deployed locally on Docker and on MiniKube 

7.0 Security considerations and best practices 


8.0 Roadmap 
- Intergrate a pipeline for the frontend and backend using GitHub actions, so that proper development can begin on the application itself.
    > It needs the core features of the application built. 
    > It needs proper log-in controls and JPT tokens. 
    > Probably much more that I haven't thought of. 
- Perform a suit of testing... 
    > load testing to ensure that the architecture can properly handle stress (after the features have been built out).
    > Chaos testing.
    > Intergration testing.
    > Deep dive into the various forms of testing that an application needs... 
- Deep dive into the monitoring suit...
    > Collect logs and forward alerts not just from the frontend and backend but also from other namespaces.
    > Learn more about the different metrics and how to interpret them and use them to optimize.
    > Practice these skills in a security conscious way to alert to potential vulernabilites and exploitations. 
- Bolster security... 
    > Learn more about how to defend a Kubernetes architecture, such as...
      * preventing communication across namespaces
      * hardening nodes
      * interegrating security tools
      * deep diving into kubernetes security...
    > Delving more into application security and AWS security to make sure that the whole structure is secure... 

9.0 License 

