# VPC-Project
This provisions the AWS infrastructure required to implement a secure architecture using a Bastion host to access EC2 instances in a private subnet.

🔧 Terraform File Explanation
1. Provider and Region

provider "aws" {
  region = "us-west-2"
}
This sets AWS as the cloud provider and defines the deployment region.

2. VPC

resource "aws_vpc" "main" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_support   = true
  enable_dns_hostnames = true
  ...
}
Creates a Virtual Private Cloud with DNS support, which is essential for resolving hostnames inside the network.

3. Subnets
Public Subnet (for Bastion host)

Private Subnet (for internal app EC2 instance)


resource "aws_subnet" "public" {
  ...
}

resource "aws_subnet" "private" {
  ...
}
4. Internet Gateway + Route Table (Public)

resource "aws_internet_gateway" "gw" { ... }
resource "aws_route_table" "public" { ... }
resource "aws_route_table_association" "public" { ... }
Enables internet access in the public subnet by attaching an Internet Gateway and adding a route to 0.0.0.0/0.

5. NAT Gateway + EIP (Private Subnet internet access)

resource "aws_eip" "nat" { ... }
resource "aws_nat_gateway" "nat" { ... }
resource "aws_route_table" "private" { ... }
Allows private subnet instances to access the internet securely (e.g., for updates or pulling packages) without being directly exposed.

6. Security Groups

resource "aws_security_group" "bastion_sg" { ... }
resource "aws_security_group" "private_sg" { ... }
Bastion SG: Allows SSH (port 22) from your IP.

Private EC2 SG: Allows SSH only from Bastion host's security group.

7. Key Pair

resource "aws_key_pair" "deployer" {
  ...
}
Uploads the public key for SSH access.

8. EC2 Instances

resource "aws_instance" "bastion" { ... }
resource "aws_instance" "private" { ... }
Bastion Host is deployed in the public subnet.

Private EC2 Instance is launched in the private subnet, accessible only via the Bastion.
