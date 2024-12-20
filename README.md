# Bookstore App Online
VPC and Subnet Setup
1. Create VPC1 and Subnet1 in cloudlibrary-rg:

VPC CIDR Block: 10.50.0.0/16
Subnet CIDR Block: 10.50.1.0/24

2. Create VPC2 and Subnet2 in cloudlibrary-rg:

VPC CIDR Block: 10.90.0.0/16
Subnet CIDR Block: 10.90.1.0/24

Catalog Service Setup
1. Setup catalog service locally:

Install .NET SDK 6.0.306: Download .NET SDK 6.0.306.
Clone the code using git clone -b core-update-v1 https://github.com/muralikrishna188/bookstoreonline.git.
Build the project:
dotnet build

Publish the code:
dotnet publish -o publish


2. Setup catalog service on EC2 (Windows Server):

Launch a Windows Server instance.
Install IIS.
Install .NET Core Runtime 6.0.33 Hosting Bundle.
Copy the published code folder (publish) to C:\inetpub\wwwroot.
Configure IIS to point to the application folder and start the application.

Weather Service Setup
1. Setup Weather Service on EC2 (Ubuntu 20.04):

Launch an Ubuntu EC2 instance.
Install Git:
sudo apt install git

Clone the code using:
git clone -b core https://github.com/muralikrishna188/bookstoreonline.git

Install Node.js and npm:
sudo apt install nodejs npm

Start the Node.js application:
sudo npm start



Inventory Service Setup
1. Setup locally:

Clone the repository and switch to the core-update-v1 branch.
Build and publish the project as done for the catalog service.

2. Deploy to AWS Elastic Beanstalk:

Create an Elastic Beanstalk application and environment.
Deploy the publish folder contents as a .NET Core application.


Cart Service Setup
1. Setup with Docker and EKS (Kubernetes):

Launch an EC2 instance for Docker.
Install Docker on the instance: Install Docker on Linux.
Clone the repository and build the Docker image:
docker build -t cart:latest .

Push the Docker image to Amazon Elastic Container Registry (ECR):
aws ecr create-repository --repository-name cart
docker tag cart:latest <ECR-URI>/cart:latest
docker push <ECR-URI>/cart:latest

Deploy the container to Amazon Elastic Kubernetes Service (EKS).


Order Service Setup
1. Setup locally using AWS Lambda:

Install AWS Toolkit for Visual Studio Code.
Configure a Lambda function to process orders and store them in Amazon S3.
Create an S3 bucket to store orders.
Deploy the Lambda function using the AWS Toolkit.


Networking
1. Setup Security Groups:

Create security groups to allow:
Catalog EC2 access from the internet on port 8080.
SSH access to the Weather EC2 instance from a specific IP address.


2. Setup VPC Peering:

Peer VPC1 and VPC2 to allow communication between catalog and weather services.

3. Restrict access to Weather Service:

Update the security group to only allow access from the Catalog EC2 instance on port 8080.


Application Gateway (AWS ALB)
1. Create an ALB:

Create an Application Load Balancer in a new dedicated VPC with CIDR: 172.17.0.0/16.
Add target groups for:
Catalog Service: Port 8080.
Inventory Service: Port 80.


2. Configure listeners and routing rules:

Listener for catalog: HTTP, port 8080, target group for Catalog.
Listener for inventory: HTTP, port 80, target group for Inventory.


Database Setup (AWS RDS)
1. Create an RDS Instance:

Choose SQL Server as the engine.
Set connectivity method to private.
Configure security groups to allow access from the Catalog EC2 instance.

2. Connect the catalog application to the RDS database:

Update the connection string in appsettings.json with the RDS endpoint.
Run the following to apply migrations:
dotnet ef migrations add InitialCreate
dotnet ef database update



Security Enhancements
1. Secure Access to Instances:

Enable Just-in-Time (JIT) access via AWS Systems Manager.
Use AWS Session Manager or a bastion host for EC2 access.

2. Enable Service Endpoints or Private Link:

For the catalog service, use a VPC endpoint for Amazon RDS.
