
# Chapter 5: Advanced Features - Intrinsic Functions, Pseudoparameters, Mappings, and Conditions

## Objective
Understand advanced CloudFormation features like intrinsic functions, pseudoparameters, mappings, and conditions to make templates more powerful and dynamic.

---

## Step 1: CloudFormation Intrinsic Functions

### What are Intrinsic Functions?
Intrinsic functions are built-in methods that allow you to perform operations within a CloudFormation template. Examples:
- String manipulations.
- Referencing resource attributes.
- Conditional logic.

---

### Common Intrinsic Functions

| Function      | Description                                                                                  |
|---------------|----------------------------------------------------------------------------------------------|
| `Fn::Join`    | Concatenate strings with a delimiter.                                                       |
| `Fn::Sub`     | Perform string substitutions.                                                               |
| `Fn::GetAtt`  | Retrieve attributes of a resource (e.g., public IP, ARN).                                   |
| `Fn::Ref`     | Reference a parameter, pseudoparameter, or logical resource name.                           |
| `Fn::If`      | Add conditional logic to resource properties or outputs.                                    |
| `Fn::FindInMap` | Retrieve values from a mapping.                                                           |
| `Fn::ImportValue` | Import outputs from another stack.                                                      |

---

### Step 1.1: Using String Functions

#### Example: Add a Prefix to an S3 Bucket Name
```yaml
Resources:
  PrefixedBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: !Join ["-", ["my-prefix", !Ref "AWS::Region", "bucket"]]
```

#### Example: Create a Tag with Dynamic Value
```yaml
Resources:
  PrefixedBucket:
    Type: AWS::S3::Bucket
    Properties:
      Tags:
        - Key: Name
          Value: !Sub "AppBucket-${AWS::StackName}-${AWS::Region}"
```

---

### Step 1.2: CloudFormation Pseudoparameters

Pseudoparameters are predefined system values you can use without explicitly declaring them. Some common pseudoparameters:
- `AWS::Region`: Current region.
- `AWS::StackName`: Name of the stack.
- `AWS::AccountId`: AWS account ID.
- `AWS::NoValue`: Returns nothing, useful in conditions.

#### Example: Using Pseudoparameters
```yaml
Resources:
  BucketWithRegion:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: !Sub "${AWS::StackName}-${AWS::Region}-bucket"
```

---

## Step 2: CloudFormation Mappings

### What are Mappings?
Mappings allow you to store static data, such as region-specific AMI IDs, in a key-value structure. They help avoid hardcoding values directly into resources.

### Hands-On Example

#### Mapping for AMI IDs
```yaml
Mappings:
  RegionMap:
    us-east-1:
      AMI: ami-0c02fb55956c7d316
    us-west-1:
      AMI: ami-0b2f6494ff0b07a0e
```

#### Using the Mapping
```yaml
Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t2.micro
      ImageId: !FindInMap [RegionMap, !Ref "AWS::Region", AMI]
```

---

## Step 3: CloudFormation Conditions

### What are Conditions?
Conditions allow resources or properties to be created only if specific criteria are met.

### Hands-On Example

#### Condition to Create Resources in Production
```yaml
Conditions:
  IsProduction:
    Fn::Equals:
      - !Ref Environment
      - Production
```

#### Using the Condition
```yaml
Resources:
  ProductionBucket:
    Type: AWS::S3::Bucket
    Condition: IsProduction
    Properties:
      BucketName: production-only-bucket
```

---

## Step 4: Combined Hands-On Template

### Full YAML Example
This template combines intrinsic functions, pseudoparameters, mappings, and conditions.

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Advanced CloudFormation Template with Intrinsic Functions, Pseudoparameters, Mappings, and Conditions

Parameters:
  Environment:
    Description: Deployment environment (Production or Development)
    Type: String
    AllowedValues:
      - Production
      - Development
    Default: Development
  InstanceType:
    Description: EC2 instance type
    Type: String
    Default: t2.micro

Mappings:
  RegionMap:
    us-east-1:
      AMI: ami-0c02fb55956c7d316
    us-west-1:
      AMI: ami-0b2f6494ff0b07a0e

Conditions:
  IsProduction:
    Fn::Equals:
      - !Ref Environment
      - Production

Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !Ref InstanceType
      ImageId: !FindInMap [RegionMap, !Ref "AWS::Region", AMI]
      KeyName: my-key-pair # Replace with your key pair name
      Tags:
        - Key: Environment
          Value: !Ref Environment
        - Key: Name
          Value: !Sub "EC2Instance-${AWS::StackName}-${Environment}"

  ProductionBucket:
    Type: AWS::S3::Bucket
    Condition: IsProduction
    Properties:
      BucketName: !Sub "${AWS::StackName}-production-bucket"
```

---

## Step 5: Steps to Deploy

1. Save the template as `advanced-template.yaml`.
2. Deploy the stack using the AWS CLI:
   ```bash
   aws cloudformation create-stack --stack-name AdvancedStack \
     --template-body file://advanced-template.yaml \
     --parameters ParameterKey=Environment,ParameterValue=Production \
                  ParameterKey=InstanceType,ParameterValue=t2.micro
   ```

3. Validate the deployment by checking:
   - The EC2 instance properties (Tags, AMI ID).
   - The S3 bucket creation in the Production environment.

---

## Next Steps
In **Chapter 6**, we’ll explore **Nested Stacks** to modularize templates for large-scale deployments.
