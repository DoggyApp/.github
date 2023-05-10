## Hi there 👋

1.0 Purpose 
The purpose of the application is to provide dog trainers a platform on which they can record, and maintain notes on the dogs they are training. These notes are to comment on behavior and training progress. 

2.0 System  
"CloudFormationArchitecture" provisions the AWS resources to host the appliction, such as the VPC, and Kubernetes Cluster. 

"KubernetesArchitecture" orchestrates the containerised application, it will pull the images of the different microservices 
off of docker hub, network them together, and provide a node port through which the UI can be accessed. It will (later) also be 
configured to provision AWS resources such as an API gateway and a dynamo DB database. 

"Frontend", "registry", and "APIGateway" are the microservices comprising the application logic. 
- "Frontend" is the GUI were users (dog trainers) will navigate and place their requests. 
- "APIGateway" is a proxy server where these requests from the GUI will be redirected. 
- "registry" is where server side logic will be performed, eventually making requests to a DynamoDB database. 

3.0 Dependencies 
"CloudFormationArchitecture" - YAML 
"KubernetesArchitecture" - YAML
"Frontend" – Node and Angualar 
"Registry" – SpringBoot; Spring Web; Java 17 
"APIGateway" -- YAML and JSON 

4.0 CI/CD 
"CloudFormationArchitecture" - this has been deployed to AWS through CodePipeline 
"KubernetesArchitecture" - this has been deployed locally on MiniKube. However I plan on deploying a container hosting Jenkins 
to the Cluster so that deployment of the microservices can be automated. 
"Frontend"; "registry"; "APIGateway" - This has been deployed locally on Docker and on MiniKube 

