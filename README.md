# NorthernStar
A Project to Migrate application to public cloud . 

# Current Architecture
Notejam is currently build as monolith containing built-in webserver and SQLite database. The task is to redesign the
architecture so it can meet business requirements on chosen public cloud platform.
 

# Business Requirements

* The Application must serve variable amount of traffic. Most users are active during business hours. During big
events and conferences the traffic could be 4 times more than typical.
* The Customer takes guarantee to preserve your notes up to 3 years and recover it if needed.
* The Customer ensures continuity in service in case of datacenter failures.
* The Service must be capable of being migrated to any regions supported by the cloud provider in case of
emergency.
* The Customer is planning to have more than 100 developers to work in this project who want to roll out multiple
deployments a day without interruption / downtime.
* The Customer wants to provision separated environments to support their development process for development,
testing, production in the near future.
* The Customer wants to see relevant metrics and logs from the infrastructure for quality assurance and security
purposes.


# High Level Solution 

* Google Cloud will be the chosen public cloud provider where the Notejam application will be hosted.
* To meet the Business Requirements the following needed to be feature of the new cloud infrastructure..
     - To server the variable amount of traffic and Load balance, we would need Autoscaling Policy , it will help to reduce the cost of having reserved more compute resources than the application need at any given point of time . That means it will reduce the cost. Also with Autoscaling policy and the desired calue of Threshold set; there wont be any disruption of service.
* To Preserve the the Notes , it is suggested to use GCP Storgae Class - Archieve as this the lowest cost of storage service and suitable for scenarios where the storage access is very less frequent and minimum time to live is 365 days , so that means we need to additionally has to force the duration remains minimum 3 years.  It also has no guarntee on response on retrieval time SLA . Assumption is these Notes don't need to be retrived within any SLA , however we can provide some guidelines how soon they may be retrieved when needed.
* The Reliability and Availiability of the application will be achieved by hosting the application in a multi zone within a Region.
* The solution will be based on Infrastructure as Code , so that will be possible to host any other region as per GCP if needed. So if the whole region breaks down it would take less time and would be easier to host on different region of choice with IaC. Also , in case of emergency if the infrastructure/application needs to be migrated to another region , then it would be necessary to have a constant incrementaly snapshot of the current state of the application and stored in GCP storage class. 
* We would need a CI/CD solution with an aumtomated deployment and configuration management so that developers can deploy multiple changes to the application without having any downtime.There are other relevant points along with this to consider further:
   a. Need to have a proper IAM policy with RBAC policies , so from 100 developers who has access to do what like deployment production , test etc.
   b. also as the infrastructure has Database as backend and developemnt environments we would need security provisions, such as Public and private subnets.
   c. 
* It also makes sense to have a seperate capacity based infrastructure planned for different enviornments like Dev, Test and Production. Infrastructure as Code should then be based on which environment we are building the infrastructure. Like the Compute instances wont need ta the capacity as we expect in production , also the Notes we dont need to store in Dev environments.
* To have monitoring enabled , we would enable GCP Monitoring service , Also we would need Logging service which can provide details on traffic usage , security details , infrastructure metrics . We can also put some alterting on top of that. Its possible to use 3rd Party Monitoring services integrated with the application as Prometheus and using Grafana as dashboard.

# Hosted Cloud Solution (GCP)

https://app.lucidchart.com/invitations/accept/0962f353-abe5-4c4d-be94-39e29dee8066

# ToolStack:
1. GCP as the Public Cloud platform
2. GIT as SCM
3. Terraform as IaC solution
4. Ansible as Configuration Management.

# Process :
Terraform will build the infrastructure on GCP with following file structure:
There will be seperate tfvars file for each Dev/Test/Production
where as main.tf and variables.tf will build the infrastructure.
if the application needs to be migrated to another region , then we would need to change the corresponsing variables in tfvars file only , assuming most of other variables value will be independent of the region.

Ansible will need a playbook which will configure the env to deploy the notejam application. It will be invoked from the main.tf file.

