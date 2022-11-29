provider "aws" {
  version = "~>3.0"
  region = "us-east-1"
}
resource "aws_vpc" "vpc1" {
  cidr_block = "10.0.0.0/24"
  tags = {
    Name = "terraformVPC"
  }
}
resource "aws_subnet" "pub" {
  vpc_id = aws_vpc.vpc1.id
  cidr_block = "10.0.0.0/25"
  map_public_ip_on_launch = true
  tags = {
    Name = "pub-sub"
  }
}
resource "aws_subnet" "pvt" {
  vpc_id = aws_vpc.vpc1.id
  cidr_block = "10.0.0.128/25"
  tags = {
    Name = "pvt-sub"
  }
}
resource "aws_route_table" "pub-route" {
  vpc_id = aws_vpc.vpc1.id
  tags = {
    Name = "pub-route"
  }
}
resource "aws_route_table" "pvt-route" {
  vpc_id = aws_vpc.vpc1.id
  tags = {
    Name = "pvt-route"
  }
}
resource "aws_route_table_association" "pub-pub" {
  route_table_id = aws_route_table.pub-route.id
  subnet_id = aws_subnet.pub.id
}
resource "aws_route_table_association" "pvt-pvt" {
  route_table_id = aws_route_table.pvt-route.id
  subnet_id = aws_subnet.pvt.id
}
resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.vpc1.id
  tags = {
    Name = "igwFromTerraform"
  }
}
resource "aws_route" "route" {
  route_table_id = aws_route_table.pub-route.id
  destination_cidr_block = "0.0.0.0/0"
  gateway_id = aws_internet_gateway.igw.id
}
resource "aws_security_group" "security-group" {
  name = "mysecurity"
  vpc_id = aws_vpc.vpc1.id
  ingress {
    from_port = 22
    protocol  = "tcp"
    to_port   = 22
    cidr_blocks = ["0.0.0.0/0"]
  }
  egress {
    from_port = 0
    protocol  = "-1"
    to_port   = 0
    cidr_blocks = ["0.0.0.0/0"]
  }
}
resource "aws_instance" "ec2-1" {
  ami = "ami-0b0dcb5067f052a63"
  instance_type = "t2.micro"
  subnet_id = aws_subnet.pub.id
  security_groups = [aws_security_group.security-group.id]
  key_name = "key1"
  user_data = file("shell.sh")
}
resource "aws_instance" "ec2-2" {
  ami = "ami-0b0dcb5067f052a63"
  instance_type = "t2.micro"
  security_groups = [aws_security_group.security-group.id]
  subnet_id = aws_subnet.pvt.id
  key_name = "key1"
}
