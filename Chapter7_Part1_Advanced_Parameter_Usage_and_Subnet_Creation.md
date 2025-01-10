
# Part 1: Advanced Parameter Usage and Dynamic Subnet Creation

## Introduction
In the previous chapter, we deployed multiple resources like VPCs, subnets, and route tables using CloudFormation. Now, we’ll enhance those templates to make them more dynamic and advanced by leveraging intrinsic functions, parameters, and conditional logic. This will allow you to create reusable templates that scale and adapt based on user inputs.

---

## Scenario 1: Direct CIDR Input

### Introduction
In this scenario, users directly provide CIDR blocks for public and private subnets. This approach offers precise control over subnet configurations.

[**Click here for full YAML**](#scenario-1-direct-cidr-input)  

---

## Scenario 2: CIDR and AZ Input

### Introduction
This scenario extends Scenario 1 by allowing users to specify the availability zones along with CIDR blocks for subnets. This provides greater control over resource placement across regions.

[**Click here for full YAML**](#scenario-2-cidr-and-az-input)  

---

## Scenario 3: Derived Subnet CIDRs Using Bits

### Introduction
Users only need to provide the VPC CIDR block and the subnet bit mask. Subnets are automatically calculated using the `Fn::Cidr` intrinsic function.

[**Click here for full YAML**](#scenario-3-derived-subnet-cidrs-using-bits)  

---

## Scenario 4: Multi-AZ Subnets with User-Defined Count

### Introduction
Users can specify how many public and private subnets they want. Subnets are automatically distributed across multiple availability zones for high availability.

[**Click here for full YAML**](#scenario-4-multi-az-subnets-with-user-defined-count)  

---

## Scenario 5: Conditional Subnet Prefix and Tags Based on Environment

### Introduction
Dynamically apply prefixes and tags to subnets based on the environment (e.g., dev, staging, prod). This ensures clear resource labeling and environment-specific configurations.

---

### Scenario 5 YAML Example

```yaml
Parameters:
  Environment:
    Description: The deployment environment (dev, staging, prod)
    Type: String
    AllowedValues:
      - dev
      - staging
      - prod
    Default: dev

Resources:
  PublicSubnetDev:
    Type: AWS::EC2::Subnet
    Condition: IsDev
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: 10.0.1.0/24
      AvailabilityZone: !Select [0, !GetAZs ""]
      Tags:
        - Key: Name
          Value: Dev-PublicSubnet

  PublicSubnetProd:
    Type: AWS::EC2::Subnet
    Condition: IsProd
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: 10.0.2.0/24
      AvailabilityZone: !Select [1, !GetAZs ""]
      Tags:
        - Key: Name
          Value: Prod-PublicSubnet

Conditions:
  IsDev:
    Fn::Equals: [!Ref Environment, dev]
  IsProd:
    Fn::Equals: [!Ref Environment, prod]
```

---

### Explanation

1. **User Input**:
   - The `Environment` parameter determines the deployment environment (dev, staging, or prod).

2. **Conditional Logic**:
   - Use `Conditions` to create resources dynamically based on the environment.

3. **Dynamic Tags**:
   - Subnets are tagged with environment-specific labels for better management.

4. **Scaling**:
   - Extend this logic to apply environment-specific configurations to other resources like route tables or EC2 instances.

---

## Deployment Instructions

### Step 1: Save Templates for Each Scenario
- Save individual templates for each scenario (e.g., `scenario-1-direct-cidr.yaml`, `scenario-5-conditional-tags.yaml`).

### Step 2: Deploy Using AWS CLI
```bash
aws cloudformation create-stack --stack-name AdvancedVPCStack   --template-body file://scenario-5-conditional-tags.yaml   --parameters ParameterKey=Environment,ParameterValue=prod
```

### Step 3: Validate in AWS Console
1. Go to the **VPC service** and verify subnet configurations.
2. Check **Tags** to ensure they match the selected environment.

---

## Conclusion for Part 1
1. Explored dynamic parameter usage for flexible subnet creation.
2. Implemented advanced scenarios like multi-AZ distribution and environment-specific tagging.
3. Prepared the foundation for **Part 2**, which will focus on conditional resource creation and scaling based on user-defined environments.
