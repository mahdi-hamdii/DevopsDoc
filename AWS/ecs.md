# ECS Key Terms:
- **Capacity**: Infrastructure where your containers run
- **Controller**: Deploy and manage your applications that run on the containers
- **Provisionning**: The tools you can use to interface with the scheduler to deploy and manage applicaitons
The capacity can be any of the following AWS ressources: AZs, Local zones, Wavelength Zones, AWS regions, AWS Outposts.
# Application Lifecycle:
- After you create and store your iamge, you create a an Amazon ECS task definition which is a blueprint for the application. It is a JSON format file that defines the parameters and one or more container that form the application.
- After you define your task definition, it is deployed either as a task or a service on your cluster. A cluster is a logical grouping of tasks or services that run on the capacity infrastructure registered to a cluser.
- A task is an instance of a task definition within a cluster. You can run a standalone task or it can be run as part of a service. Amazon ECS service allows to maintain the desired number of tasks simultaneously. How it works is that the Amazon ECS scheduler launches another task if any of the task stops or fail for any reason
- The container agent runs on each container instance within ECS cluster. The agent sends information about the current running tasks and resource utilization. It starts and stops containers whenever it receives a requests from ECS.
- **`ECS Service Connect`**: Define a logical name for your service endpoints and use them in the client applications to connect to dependencies.
# Create Task Definition
```json
{
 "family": "sample-fargate",
 "networkMode": "awsvpc",
 "containerDefinitions": [
 {
 "name": "fargate-app",
 "image": "public.ecr.aws/docker/library/httpd:latest",
 "portMappings": [
 {
 "containerPort": 80,
 "hostPort": 80,
 "protocol": "tcp"
 }
 ],
 "essential": true,
 "entryPoint": [
 "sh",
 "-c"
 ],
 "command": [
 "/bin/sh -c \"echo '<html> <head> <title>Amazon ECS Sample
 App</title> <style>body {margin-top: 40px; background-color: #333;} </style> </
head><body> <div style=color:white;text-align:center> <h1>Amazon ECS Sample App</h1>
 <h2>Congratulations!</h2> <p>Your application is now running on a container in Amazon
 ECS.</p> </div></body></html>' > /usr/local/apache2/htdocs/index.html && httpdforeground\""
 ]
 }
 ],
 "requiresCompatibilities": [
 "FARGATE"
 ],
 "cpu": "256",
 "memory": "512"
}
```
# AWS ECS On Fargate:
- AWS Fargate requires that network mode is set to `awsvpc`. The AWS VPC network mode provides each task with its own elastic network interface 
- For tasks running on fargate we can use either ephemeral storage or EFS volumes for persistent storage.