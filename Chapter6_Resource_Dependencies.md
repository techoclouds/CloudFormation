
# Chapter 6: Resource Dependencies and Deployment Order in CloudFormation

## Objective
Learn how CloudFormation handles resource creation and dependencies, including how to reference resources dynamically using `Ref`, control deployment order with `DependsOn`, and perform step-by-step stack updates to incrementally build a fully functional VPC. This includes adding an Internet Gateway, route tables, security groups, and deploying an EC2 instance.

---

## Step 1: Deploy the Initial Template (VPC and Public Subnet)

### Why Start with VPC and Subnet?
A VPC is the foundational resource for AWS networking, and a subnet divides the VPC into logical segments. Starting with these resources provides a base for all subsequent configurations.

### Template: `vpc-and-subnet.yaml`
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: CloudFormation template to create a VPC and a public subnet.

Parameters:
  VPCCIDR:
    Description: CIDR block for the VPC
    Type: String
    Default: 10.0.0.0/16
  PublicSubnetCIDR:
    Description: CIDR block for the public subnet
    Type: String
    Default: 10.0.1.0/24
  AvailabilityZone:
    Description: Availability Zone for the subnet
    Type: String
    Default: us-east-1a

Resources:
  MyVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: !Ref VPCCIDR
      EnableDnsSupport: true
      EnableDnsHostnames: true
      Tags:
        - Key: Name
          Value: MyCustomVPC

  PublicSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: !Ref PublicSubnetCIDR
      AvailabilityZone: !Ref AvailabilityZone
      Tags:
        - Key: Name
          Value: MyPublicSubnet
```

### Steps to Deploy
1. Save the above template as `vpc-and-subnet.yaml`.
2. Deploy the stack:
   ```bash
   aws cloudformation create-stack --stack-name VPCStack      --template-body file://vpc-and-subnet.yaml
   ```
3. **Verify Deployment**:
   - Go to the **VPC service** in the AWS Console.
   - Confirm the VPC and Public Subnet are created.

---

## Step 2: Update the Stack to Add the Internet Gateway

### Why Add an Internet Gateway?
An Internet Gateway provides internet connectivity for resources like EC2 instances in the public subnet.

### Template Update: Adding Internet Gateway
```yaml
Resources:
  MyInternetGateway:
    Type: AWS::EC2::InternetGateway
    Properties:
      Tags:
        - Key: Name
          Value: MyInternetGateway

  VPCGatewayAttachment:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref MyVPC
      InternetGatewayId: !Ref MyInternetGateway
```

### Steps to Deploy
1. Update the previous template and save it as `vpc-with-igw.yaml`.
2. Update the stack:
   ```bash
   aws cloudformation update-stack --stack-name VPCStack      --template-body file://vpc-with-igw.yaml
   ```
3. **Verify Deployment**:
   - Check the **VPC service** for the attached Internet Gateway.

---

## Step 3: Update the Stack to Add a Route Table

### Why Add a Route Table?
A route table defines the traffic routing rules for subnets. Associating it with the Internet Gateway enables internet connectivity for public subnets.

### Template Update: Adding Route Table
```yaml
Resources:
  PublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref MyVPC
      Tags:
        - Key: Name
          Value: PublicRouteTable

  RouteToInternet:
    Type: AWS::EC2::Route
    DependsOn: VPCGatewayAttachment
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref MyInternetGateway

  SubnetRouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref PublicSubnet
      RouteTableId: !Ref PublicRouteTable
```

### Steps to Deploy
1. Update the template and save it as `vpc-with-route-table.yaml`.
2. Update the stack:
   ```bash
   aws cloudformation update-stack --stack-name VPCStack      --template-body file://vpc-with-route-table.yaml
   ```
3. **Verify Deployment**:
   - Go to the **Route Tables** tab in the VPC service.
   - Confirm the route table and its associations.

---

## Step 4: Update the Stack to Add a Security Group

### Why Add a Security Group?
A security group acts as a virtual firewall, controlling access to EC2 instances by specifying inbound and outbound traffic rules.

### Template Update: Adding Security Group
```yaml
Resources:
  MySecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Allow SSH and HTTP
      VpcId: !Ref MyVPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0
      Tags:
        - Key: Name
          Value: PublicAccessSG
```

### Steps to Deploy
1. Update the template and save it as `vpc-with-security-group.yaml`.
2. Update the stack:
   ```bash
   aws cloudformation update-stack --stack-name VPCStack      --template-body file://vpc-with-security-group.yaml
   ```
3. **Verify Deployment**:
   - Go to the **EC2 service** and check the Security Groups tab.

---

## Step 5: Update the Stack to Add an EC2 Instance

### Why Add an EC2 Instance?
Deploying an EC2 instance in the public subnet completes the setup by allowing you to run compute resources accessible from the internet.

### Template Update: Adding EC2 Instance
```yaml
Resources:
  PublicEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t2.micro
      ImageId: ami-0c02fb55956c7d316 # Replace with a valid AMI ID
      SubnetId: !Ref PublicSubnet
      SecurityGroupIds:
        - !Ref MySecurityGroup
      KeyName: my-key-pair # Replace with your key pair name
      Tags:
        - Key: Name
          Value: PublicInstance
```

### Steps to Deploy
1. Update the template and save it as `vpc-with-ec2.yaml`.
2. Update the stack:
   ```bash
   aws cloudformation update-stack --stack-name VPCStack      --template-body file://vpc-with-ec2.yaml
   ```
3. **Verify Deployment**:
   - Go to the **EC2 service** and check the running instance in the Public Subnet.

---

By following these steps, you can incrementally build a robust AWS network infrastructure using CloudFormation while learning stack updates and dependency management.
