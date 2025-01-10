
# Chapter 4: Using Outputs and Resource References

## Objective
Learn to use the `Outputs` section in CloudFormation templates to share information about created resources. Understand how to reference resources from one stack in another.

---

## Step 1: Introduction to Outputs
The `Outputs` section in a CloudFormation template allows you to:
1. Export stack resources for cross-stack referencing.
2. Retrieve resource information (e.g., ARN, public IP) after stack creation.

---

## Step 2: Hands-On Example

### YAML Template with Outputs

In this example:
- We’ll create an S3 bucket and an EC2 instance.
- Add outputs for:
  - **S3 Bucket Name**.
  - **EC2 Instance Public IP**.

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: CloudFormation Template with Outputs for S3 and EC2 Resources

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

  # EC2 Instance Resource
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !Ref InstanceType
      ImageId: ami-0c02fb55956c7d316 # Replace with an appropriate AMI ID for your region
      KeyName: my-key-pair # Replace with your key pair name

Outputs:
  S3BucketName:
    Description: Name of the S3 bucket
    Value: !Ref MyS3Bucket
    Export:
      Name: S3BucketNameExport

  EC2PublicIP:
    Description: Public IP address of the EC2 instance
    Value: !GetAtt MyEC2Instance.PublicIp
    Export:
      Name: EC2PublicIPExport
```

---

## Step 3: Steps to Deploy

### Step 3.1: Save the Template
Save the YAML code into a file named `template-with-outputs.yaml`.

---

### Step 3.2: Deploy the Template
#### **Using AWS Console**:
1. Go to the **CloudFormation** service.
2. Click **Create stack** > **With new resources (standard)**.
3. Upload the `template-with-outputs.yaml` file.
4. Provide values for:
   - **BucketName**: e.g., `my-output-s3-bucket`.
   - **InstanceType**: Select from allowed values.
5. Follow the prompts to create the stack.

#### **Using AWS CLI**:
Run the following command:
```bash
aws cloudformation create-stack --stack-name OutputsStack \
  --template-body file://template-with-outputs.yaml \
  --parameters ParameterKey=BucketName,ParameterValue=my-output-s3-bucket \
               ParameterKey=InstanceType,ParameterValue=t2.micro
```

---

## Step 4: Validate Deployment
1. **Verify Outputs in the Console**:
   - Go to the **CloudFormation** service.
   - Open the stack details and view the **Outputs** tab.
2. **Verify S3 Bucket Name**:
   - Confirm the S3 bucket name matches the output.
3. **Verify EC2 Public IP**:
   - Confirm the public IP address is displayed in the outputs.

---

## Step 5: Cross-Stack Referencing
You can use the outputs exported by one stack in another stack using the `Fn::ImportValue` function. Example:

```yaml
Resources:
  AnotherS3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: !ImportValue S3BucketNameExport
```

---

## Step 6: Template Validation
Validate the template syntax using AWS CLI:
```bash
aws cloudformation validate-template --template-body file://template-with-outputs.yaml
```

---

## Next Steps
In **Chapter 5**, we’ll explore **Mappings** and **Conditions** to make the templates smarter and conditional.
