## AWS CLOUD SOLUTION FOR 2 COMPANY WEBSITES USING A REVERSE PROXY TECHNOLOGY


![image](https://user-images.githubusercontent.com/52359007/170261120-b583cdf0-dc68-451a-8a01-3873febaaf9c.png)

Project fifteen is all about implementing the infrastructure architecture above and deploying tooling and wordpress websites we deployed in previous projects in AWS.


### Starting Off Your AWS Cloud Project

- Properly configure your AWS account and Organization Unit [watch the video to learn how](https://www.youtube.com/watch?v=9PQYCc_20-Q)

- Create a free domain name for your fictitious company at Freenom domain registrar [here](https://www.freenom.com/en/index.html?lang=en)

- Create a hosted zone in AWS, and map it to your free domain from Freenom. [watch how](https://www.youtube.com/watch?v=IjcHp94Hq8A)



  ![Route53](https://user-images.githubusercontent.com/52359007/170263028-2e618e42-bd22-4cb0-9834-a6728de3bd3d.PNG)
  

- Create a VPC


![VPC](https://user-images.githubusercontent.com/52359007/170263733-0c070cf4-2b2c-4312-9f6b-cd430bd50afa.PNG)


- Create subnets as shown in the architecture


![subnets](https://user-images.githubusercontent.com/52359007/170264052-18f2c72a-9ad8-4ae1-b193-af3b60d1c016.PNG)


- Create a route table and associate it with public subnets


![route table](https://user-images.githubusercontent.com/52359007/170264345-0a196f5d-ae98-4810-aaf2-08f728a7d030.PNG)


- Create an Internet Gateway


![Internet gateway](https://user-images.githubusercontent.com/52359007/170264684-b977efe3-2ef9-480f-8fae-6b962438f5e7.PNG)


- Edit a route in public route table, and associate it with the Internet Gateway. (This is what allows a public subnet to be accisble from the Internet)

- Create 3 Elastic IPs


![elastic ip](https://user-images.githubusercontent.com/52359007/170265492-9a29b056-cd38-48bc-9b41-05724b886b1d.PNG)


- Create a Nat Gateway and assign one of the Elastic IPs (*The other 2 will be used by Bastion hosts)


![Nat gateway](https://user-images.githubusercontent.com/52359007/170265905-8f4cae1b-5330-4161-ab7b-9dfc043fe91a.PNG)


- Create a Security Group for: 

  - Nginx Servers: Access to Nginx should only be allowed from a Application Load balancer (ALB). At this point, we have not created a load balancer, therefore we will     update the rules later. For now, just create it and put some dummy records as a place holder.

  - Bastion Servers: Access to the Bastion servers should be allowed only from workstations that need to SSH into the bastion servers. Hence, you can use your             workstation public IP address. To get this information, simply go to your terminal and type curl www.canhazip.com


  - Application Load Balancer: ALB will be available from the Internet

  - Webservers: Access to Webservers should only be allowed from the Nginx servers. Since we do not have the servers created yet, just put some dummy records as a         place holder, we will update it later.

  - Data Layer: Access to the Data layer, which is comprised of Amazon Relational Database Service (RDS) and Amazon Elastic File System (EFS) must be carefully             desinged – only webservers should be able to connect to RDS, while Nginx and Webservers will have access to EFS Mountpoint.

- Set Up Compute Resources for Nginx


  - Create an EC2 Instance based on CentOS Amazon Machine Image (AMI) in any 2 Availability Zones (AZ) in any AWS Region (it is recommended to use the Region that is       closest to your customers). Use EC2 instance of T2 family (e.g. t2.micro or similar)

  - Ensure that it has the following software installed:

     - python
     - ntp
     - net-tools
     - vim
     - wget
     - telnet
     - epel-release
     - htop

  - Create an AMI out of the EC2 instance

  - Prepare Launch Template For Nginx (One Per Subnet)

    - Make use of the AMI to set up a launch template
    
    - Ensure the Instances are launched into a public subnet
    
    - Assign appropriate security group
    
    - Configure Userdata to update yum package repository and install nginx

 

     ![AMIs](https://user-images.githubusercontent.com/52359007/170268945-1c71da12-d426-4f56-ab26-d1bd3cb68805.PNG)
     
     
  - Prepare Launch Template For Nginx (One Per Subnet)

    - Make use of the AMI to set up a launch template
    
    - Ensure the Instances are launched into a public subnet
    
    - Assign appropriate security group
    
    - Configure Userdata to update yum package repository and install nginx


     ![launch template](https://user-images.githubusercontent.com/52359007/170269926-5850eb00-813c-43c0-8f3a-ac2b1a9149ae.PNG) 
     
     
  - Configure Target Groups

    - Select Instances as the target type
    
    - Ensure the protocol HTTPS on secure TLS port 443
    
    - Ensure that the health check path is /healthstatus
    
    - Register Nginx Instances as targets
    
    - Ensure that health check passes for the target group

  

   ![target-group](https://user-images.githubusercontent.com/52359007/170270960-cebc2dd1-0323-48a2-94f5-d445e8d7230d.PNG)
   
   
  - Configure Autoscaling For Nginx

    - Select the right launch template
    
    - Select the VPC
    
    - Select both public subnets
    
    - Enable Application Load Balancer for the AutoScalingGroup (ASG)
    
    - Select the target group you created before
    
    - Ensure that you have health checks for both EC2 and ALB
    
    - The desired capacity is 2
        - Minimum capacity is 2
        
        - Maximum capacity is 4
        
        - Set scale out if CPU utilization reaches 90%
        
    - Ensure there is an SNS topic to send scaling notifications

 
   ![Autoscaling group](https://user-images.githubusercontent.com/52359007/170272396-1abd719e-b9ea-49f1-b714-112408db6f86.PNG) 
   
   
 - Set Up Compute Resources for Bastion


  - Provision the EC2 Instances for Bastion
  
  - Create an EC2 Instance based on CentOS Amazon Machine Image (AMI) per each Availability Zone in the same Region and same AZ where you created Nginx server
  
  - Ensure that it has the following software installed

    - python
    
    - ntp
    
    - net-tools
    
    - vim
    
    - wget
    
    - telnet
    
    - epel-release
    
    - htop
    
  - Associate an Elastic IP with each of the Bastion EC2 Instances
  
  - Create an AMI out of the EC2 instance


  - Prepare Launch Template For Bastion (One per subnet)
  
      - Make use of the AMI to set up a launch template
      
      - Ensure the Instances are launched into a public subnet
      
      - Assign appropriate security group
      
      - Configure Userdata to update yum package repository and install Ansible and git
      
  - Configure Target Groups
  
      - Select Instances as the target type
      
      - Ensure the protocol is TCP on port 22
      
      - Register Bastion Instances as targets
      
      - Ensure that health check passes for the target group
      
      
  - Configure Autoscaling For Bastion


    - Select the right launch template
    
    - Select the VPC
    
    - Select both public subnets
    
    - Enable Application Load Balancer for the AutoScalingGroup (ASG)
    
    - Select the target group you created before
    
    - Ensure that you have health checks for both EC2 and ALB
    
    - The desired capacity is 2
    
    - Minimum capacity is 2
    
    - Maximum capacity is 4
    
    - Set scale out if CPU utilization reaches 90%
    
    - Ensure there is an SNS topic to send scaling notifications


 - Set Up Compute Resources for Webservers

 - Provision the EC2 Instances for Webserver

 -  Now, you will need to create 2 separate launch templates for both the WordPress and Tooling websites

 -  Create an EC2 Instance (Centos) each for WordPress and Tooling websites per Availability Zone (in the same Region).

 -   Ensure that it has the following software installed

      - python
      
      - ntp
      
      - net-tools
      
      - vim
      
      - wget
      
      - telnet
      
      - epel-release
      
      - htop
      
      - php
      
 - Create an AMI out of the EC2 instance

 - Prepare Launch Template For Webservers (One per subnet)
 
   - Make use of the AMI to set up a launch template
   
   - Ensure the Instances are launched into a public subnet
   
   - Assign appropriate security group
   
   - Configure Userdata to update yum package repository and install wordpress (Only required on the WordPress launch template)
   

 ### TLS Certificates From Amazon Certificate Manager (ACM)
 
 You will need TLS certificates to handle secured connectivity to your Application Load Balancers (ALB)
 
  - Navigate to AWS ACM
  
  - Request a public wildcard certificate for the domain name you registered in Freenom
  
  - Use DNS to validate the domain name
  
  - Tag the resource

  ### CONFIGURE APPLICATION LOAD BALANCER (ALB)
  Application Load Balancer To Route Traffic To NGINX
  Nginx EC2 Instances will have configurations that accepts incoming traffic only from Load Balancers. No request should go directly to Nginx servers. With this kind     of setup, we will benefit from intelligent routing of requests from the ALB to Nginx servers across the 2 Availability Zones. We will also be able to offload SSL/TLS   certificates on the ALB instead of Nginx. Therefore, Nginx will be able to perform faster since it will not require extra compute resources to valifate certificates   for every request.
  
  - Create an Internet facing ALB
  
  - Ensure that it listens on HTTPS protocol (TCP port 443)
  
  - Ensure the ALB is created within the appropriate VPC | AZ | Subnets
  
  - Choose the Certificate from ACM
  
  - Select Security Group
  
  - Select Nginx Instances as the target group

- Application Load Balancer To Route Traffic To Web Servers
  Since the webservers are configured for auto-scaling, there is going to be a problem if servers get dynamically scalled out or in. Nginx will not know about the new   IP addresses, or the ones that get removed. Hence, Nginx will not know where to direct the traffic.

  To solve this problem, we must use a load balancer. But this time, it will be an internal load balancer. Not Internet facing since the webservers are within a         private subnet, and we do not want direct access to them.
  
    - Create an Internal ALB
    
    - Ensure that it listens on HTTPS protocol (TCP port 443)
    
    - Ensure the ALB is created within the appropriate VPC | AZ | Subnets
    
    - Choose the Certificate from ACM
    
    - Select Security Group
    
    - Select webserver Instances as the target group
    
    - Ensure that health check passes for the target group

 - NOTE: This process must be repeated for both WordPress and Tooling websites.

 - Setup EFS

  Amazon Elastic File System (Amazon EFS) provides a simple, scalable, fully managed elastic Network File System (NFS) for use with AWS Cloud services and on-premises   resources. In this project, we will utulize EFS service and mount filesystems on both Nginx and Webservers to store data.
  
    - Create an EFS filesystem
    
    - Create an EFS mount target per AZ in the VPC, associate it with both subnets dedicated for data layer
    
    - Associate the Security groups created earlier for data layer.
    
    - Create an EFS access point. (Give it a name and leave all other settings as default)




















































