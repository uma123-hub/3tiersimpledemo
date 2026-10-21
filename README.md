
3-tier architecture 
 
echo "<h1>Three Tier Web Tier – us east 1b</h1>" | sudo tee /var/www/html/index.html
echo "<h1>Three‑Tier private‑1b</h1>" | sudo tee /var/www/html/index.html

 
 
From bastian to private----
Ppk to pem convert----copy to bastain server—chmod 400 .pem---ssh -I that server as shoiwn in picture
 

 








Billing alert:
 
launch-wizard-2 –webserver sg
 
 
Download the client RPMs
wget https://dev.mysql.com/get/Downloads/MySQL-8.0/mysql-community-client-8.0.46-1.el9.x86_64.rpm
wget https://dev.mysql.com/get/Downloads/MySQL-8.0/mysql-community-common-8.0.46-1.el9.x86_64.rpm
wget https://dev.mysql.com/get/Downloads/MySQL-8.0/mysql-community-libs-8.0.46-1.el9.x86_64.rpm
wget https://dev.mysql.com/get/Downloads/MySQL-8.0/mysql-community-client-plugins-8.0.46-1.el9.x86_64.rpm

Install them manually (ignoring GPG check)
sudo dnf install -y mysql-community-common-8.0.46-1.el9.x86_64.rpm \
                    mysql-community-libs-8.0.46-1.el9.x86_64.rpm \
                    mysql-community-client-plugins-8.0.46-1.el9.x86_64.rpm \
                    mysql-community-client-8.0.46-1.el9.x86_64.rpm –nogpgcheck

Verify installation
mysql –version
Connect to RDS
mysql -h three-tier-db.c8b4ue04cm2f.us-east-1.rds.amazonaws.com -u admin -p
 









Written way with SS from scratch of 3-tier architechture.(21/10/2026)
VPC and Networking:
 
Create VPC → CIDR 10.0.0.0/16
Open VPC Console → click Create VPC.
•	Name: new3tier
•	IPv4 CIDR: 10.0.0.0/16
•	Tenancy: Default → Create
 

Create Subnets → six total (two public, four private)
 
Create IGW, Attach Internet Gateway → link to your VPC
 
Create NAT Gateway → in Public Subnet 1 with Elastic IP
 
Click Allocate Elastic IP → AWS will assign a new public IP.
Select that Elastic IP.

Route Tables →
•	Public RT: 0.0.0.0/0 → Internet Gateway.
•	Private RT: 0.0.0.0/0 → NAT Gateway.
 
Go to Routes → Edit routes → Add route.
•	Destination: 0.0.0.0/0
•	Target: Internet Gateway (select the one attached to your VPC).
Save changes.
Go to Subnet associations → Edit subnet associations.
•	Select Public Subnet 1 and Public Subnet 2
 
Open each private route table.
Go to Routes → Edit routes → Add route.
•	Destination: 0.0.0.0/0
•	Target: NAT Gateway (the one you created earlier).
Save changes.
Go to Subnet associations → Edit subnet associations.
•	For private1-us-east-1a, select Private Subnet 1 and Private Subnet 3.
•	For private2-us-east-1b, select Private Subnet 2 and Private Subnet 4


Web Tier 
1.	Launch EC2 instances in Public Subnets 1 & 2.
 
Sg:
 
2.	Create Bastion Host in Public Subnet 1 for SSH access.
 
3.	Install web server (Apache or Nginx).
sudo yum update -y
sudo yum install -y httpd
sudo systemctl start httpd
sudo systemctl enable httpd
echo "<h1>Welcome to Three‑Tier Web Tier</h1>" | sudo tee /var/www/html/index.html
4.	Create Application Load Balancer (Internet facing).
5.	Configure Target Group and Auto Scaling Group (webserver sg).
 
Load balancer:
 
 
 
 
 
Application Tier 
Sg:
EC2 → Security Groups → Create Security Group
•	Name: appserver-sg
•	VPC: ThreeTierVPC
•	Inbound Rules:
o	Type: SSH (22) → Source: Your IP (for admin access)
o	Type: HTTP (80) → Source: webserver-sg (only web tier can talk to app tier)
•	Outbound Rules: All traffic → 0.0.0.0/0

Bastion Host (Jump Server) :
•	launch a small EC2 (e.g., t3.micro) in your public subnet.
•	Connect to it via SSH using your key pair.
•	From that bastion, SSH into the private EC2 using its private IP

1.	Launch EC2 instances in Private Subnets 1 & 2   install apache by connecting from bastian
 
Convert to .pem --- copy key to bastian server from winscp ---- 
chmod 400 puttykey.pem
ssh -i puttykey.pem ec2-user@10.0.40.75(this is private ip of app server)

2.	Create Internal Load Balancer (private rb).
3.	Configure Auto Scaling Group (appserver sg).
4.	Set Security Groups to allow traffic only from web tier.
Database Tier 
1.	Create RDS instance (MySQL or PostgreSQL).
 
2.	Place in Private Subnets 3 & 4 with Multi AZ enabled.
3.	Configure DB Subnet Group and database sg to allow connections only from appserver sg.
 
  
 



