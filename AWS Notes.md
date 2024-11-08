- AWS Developer Associate is a developer-focused certification in teaching:
	- Build, Deploy, and CI/CD workflows for *Web application to the cloud*
	- Working with AWS programmatically via the API, SDK, CLI and IaC
	- Develop a Cloud-first mindset for building applications
	- DVA-C02 is the course code
- Platform as a Service (PaaS) - A platform allowing customers to develop, run, and manage application, without the complexity of building and maintain the infrastructure
## Elastic Beanstalk
- helps run applications on the web
- You choose a platform, upload your code, and it runs with little knowledge of the infrastructure.
	- NOT recommended for "Production" <--(AWS is talking about enterprise, large companies) applications
	- Nowadays EB is much more complicated than it used to be in the past
- EB is powered by a CloudFormation template setups for you
	- CloudFormation makes you work with templates and stacks
		- You create templates to describe your AWS resources and their properties.
		- When creating a stack, CloudFormation provisions the resources that are described in your template
	- CloudFormation Templates is a JSON or YAML formatted text file
		- CF uses these templates as a blueprint to build out your AWS resources
	- CF Stacks is a single unit of related resources defined by a Template
- An example EB instance defined by a CF Template
	- Elastic Load balancer, Autoscaling Groups, RDS Database, EC2 Instance preconfigured (or customer) platforms, Monitoring (CloudWatch, SNS), In-Place and Blue/Green deployment methodologies, Security (Rotates passwords), Can run Dockerized environments

```
# Example of a YAML CloudFormation Template

{
    "AWSTemplateFormatVersion": "2010-09-09",
    "Description": "A sample template",
    "Resources": {
        "MyEC2Instance": {
            "Type": "AWS::EC2::Instance",
            "Properties": {
                "ImageId": "ami-0ff8a91507f77f867",
                "InstanceType": "t2.micro",
                "KeyName": "testkey",
                "BlockDeviceMappings": [
                    {
                        "DeviceName": "/dev/sdm",
                        "Ebs": {
                            "VolumeType": "io1",
                            "Iops": 200,
                            "DeleteOnTermination": false,
                            "VolumeSize": 20
                        }
                    }
                ]
            }
        },
        "MyEIP": {
            "Type": "AWS::EC2::EIP",
            "Properties": {
                "InstanceId": {
                    "Ref": "MyEC2Instance"
                }
            }
        }
    }
}
```

EB Supported languages and Frameworks

| Supported Languages | Framework |
| ------------------- | --------- |
| Ruby                | Rails     |
| Python              | Django    |
| PHP                 | Laravel   |
| Tomcat              | Spring    |
| Node.js             | Express   |

- Two Types of Web Environments for EB
	- Load-Balanced ENV:
		- Uses AutoScaling Group and set to scale
		- Uses the Elastic Load Balancer
		- Designed to Scale
	- Single-Instance Env
		- Still uses an Auto Scaling Group but Desired Capacity set to 1, to ensure server is always running
		- No ELB to save on cost
		- Public IP Address has to be used to route traffic to server
- Deployment policies for Elastic Beanstalk:

| Deployment Policy             | Load-Balanced ENV | Single-Instance ENV |
| ----------------------------- | ----------------- | ------------------- |
| All at once                   | 👍                | 👍                  |
| Rolling                       | 👍                |                     |
| Rolling with additional batch | 👍                |                     |
| Immutable                     | 👍                | 👍                  |
| Traffic splitting             | 👍 (ALB)          |                     |
- EB Deployment Strategy: All at once
	- Deploys the new app version to all instances at the same time
	- Takes all instances ==out of service== while the deployment process is occurring
	- Servers become available again when it is complete
	- THIS IS THE DEFAULT POLICY
	- Fastest method for deployment, but it is also the most dangerous
		- You are essentially taking your servers offline for a period of time
		- You are deploying an issue or mistake to all instances
	- In case of failure: Roll back changes by re-deploying the original version again to all instances
- EB Deployment Strategy: Rolling
	- Deploys the new app version to a batch of instances at a time
	- Takes the batch's instances ==out of service== while the deployment process is occurring 
	- Reattaches updated instances
	- Once done with current batch, moves on to the next batch, taking them out of service as well
	- In case of failure: You need to perform an additional rolling update in order to roll back the changes
- EB Deployment Strategy: EB Rolling with additional Batch
	- Similar to rolling, but instead of taking down a batch, you launch an instance that will be used to replace the old batch
	- Deploy the update app version to the new batch
	- Attach the new batch and terminate the existing old batch
	- This strategy ensures that our capacity is never reduced. This is important for applications where a reduction in capacity could cause availability issues for users.
	- In case of failure: You need to perform an additional rolling update to roll back the changes
- EB Deployment Strategy: Immutable
	- Creates a new Auto Scaling group for the EC2 instances
	- Deploy the updated version of the app on the new EC2 Instances
	- It will then point the Elastic Load Balancer to the new ASG and delete the old ASG, which will terminate the old EC2 Instances
	- Safest way to deploy for critical applications
	- In case of failure: Just terminate the new instances since the existing instances still remain

🕐 Less clock emoji means faster
✅ Downtime exists


| Method                       | Impact of failed deployment                                                                | Deploy Time | Downtime | CNS Change    | Rollback process                            | Code deployed to Instances |
| ---------------------------- | ------------------------------------------------------------------------------------------ | ----------- | -------- | ------------- | ------------------------------------------- | -------------------------- |
| All at once                  | Downtime                                                                                   | 🕐          | ✅        | No DNS Change | Manual                                      | Existing                   |
| Rolling                      | Single batch out of service; any successful batches before failure running new app version | 🕐🕐🕐+     |          | No DNS Change | Manual                                      | Existing                   |
| Rolling with Addtional Batch | Minimal if first batch fails; otherwise similar to Rolling                                 | 🕐🕐🕐+     |          | No DNS Change | Manual                                      | New and Existing           |
| Immutable                    | Minimal                                                                                    | 🕐🕐🕐🕐    |          | No DNS Change | Terminate New                               | New                        |
| Blue/Green                   | Minimal                                                                                    | 🕐🕐🕐🕐    |          | DNS Change    | Swap URL                                    | New                        |
| Traffic Splitting            | Percentage of the client traffic is routed to the new version; temporarily impacted        | 🕐🕐🕐🕐++  |          | No DNS Change | Reroute traffic and terminate new instances | New                        |