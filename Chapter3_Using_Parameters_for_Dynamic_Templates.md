
# Chapter 3: Using Parameters for Dynamic Templates

## Objective
Learn to make CloudFormation templates dynamic and reusable by introducing parameters.

---

## Step 1: Introduction to Parameters
Parameters allow you to pass values into a CloudFormation stack at the time of deployment. This makes the template:
- **Dynamic**: You can deploy the same template in different environments.
- **Reusable**: No need to hard-code values like AMI IDs, instance types, or bucket names.

---

## Step 2: Hands-On Example

### YAML Template with Parameters

In this example, we’ll use parameters for:
1. **Bucket Name**: To specify the name of the S3 bucket.
2. **Instance Type**: To dynamically set the EC2 instance type.

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: CloudFormation Template with Parameters for S3 and EC2 Resources

Parameters:
  BucketName:
    Description: Name of the S3 bucket
    Type: String
  InstanceType:
    Description: EC2 instance type
    Type: String
    Default: t2.micro
    AllowedValues:
      - t2.micro
      - t2.small
      - t2.medium
    ConstraintDescription: Must be a valid EC2 instance type.

Resources:
  # S3 Bucket Resource
  MyS3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: !Ref BucketName
      Tags:
        - Key: Environment
          Value: Development
        - Key: Project
          Value: CloudFormationDemo

  # EC2 Instance Resource
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !Ref InstanceType
      ImageId: ami-0c02fb55956c7d316 # Replace with an appropriate AMI ID for your region
      KeyName: my-key-pair # Replace with your key pair name
      Tags:
        - Key: Environment
          Value: Development
        - Key: Project
          Value: CloudFormationDemo
```

---

## Step 3: Steps to Deploy

### Step 3.1: Save the Template
Save the YAML code into a file named `template-with-parameters.yaml`.

---

### Step 3.2: Deploy the Template
#### **Using AWS Console**:
1. Go to the **CloudFormation** service.
2. Click **Create stack** > **With new resources (standard)**.
3. Upload the `template-with-parameters.yaml` file.
4. Provide values for:
   - **BucketName**: e.g., `my-dynamic-s3-bucket`.
   - **InstanceType**: Select from allowed values.
5. Follow the prompts to create the stack.

#### **Using AWS CLI**:
Run the following command:
```bash
aws cloudformation create-stack --stack-name DynamicResourcesStack \
  --template-body file://template-with-parameters.yaml \
  --parameters ParameterKey=BucketName,ParameterValue=my-dynamic-s3-bucket \
               ParameterKey=InstanceType,ParameterValue=t2.micro
```

---

## Step 4: Validate Deployment
1. **Verify S3 Bucket**:
   - Go to the **S3 service**.
   - Check if the bucket with the specified name is created.
2. **Verify EC2 Instance**:
   - Go to the **EC2 service**.
   - Check the instance type and ensure it matches the parameter value provided.

---

## Step 5: Template Validation
Validate the template syntax using AWS CLI:
```bash
aws cloudformation validate-template --template-body file://template-with-parameters.yaml
```

---

## Next Steps
In **Chapter 4**, we’ll explore the **Outputs** section and learn to reference resources from one template in another.
