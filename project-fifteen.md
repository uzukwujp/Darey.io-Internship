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






















