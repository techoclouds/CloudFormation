
# Chapter 1: Introduction to AWS CloudFormation

## Objective
Understand the purpose and benefits of CloudFormation by creating a minimal template.

---

## Template Components
A basic CloudFormation template has the following key sections:
1. **AWSTemplateFormatVersion**: Specifies the template version (optional).
2. **Description**: Describes the purpose of the template (optional but recommended).
3. **Resources**: Defines AWS resources to create (mandatory).

---

## Hands-On Example

### Minimal YAML Template

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Basic CloudFormation Template for an S3 Bucket

Resources:
  MyS3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: my-first-cloudformation-bucket
```

---

## Steps to Deploy
### Step 1: Save the Template
Save the above YAML code into a file named `basic-s3-template.yaml`.

### Step 2: Deploy the Template
#### **Using the AWS Console**:
   1. Go to the **CloudFormation** service.
   2. Click **Create stack** > **With new resources (standard)**.
   3. Upload your `basic-s3-template.yaml` file.
   4. Follow the prompts to create the stack.

#### **Using the AWS CLI**:
Run the following command:
```bash
aws cloudformation create-stack --stack-name BasicS3Stack --template-body file://basic-s3-template.yaml
```

---

## Validate the Template
1. **Using CloudFormation Designer**:
   - Go to the **CloudFormation** service.
   - Click on **Designer**.
   - Upload the YAML file to visualize resources.

2. **Using AWS CLI**:
Run the following command to validate the syntax:
```bash
aws cloudformation validate-template --template-body file://basic-s3-template.yaml
```

---

## Next Steps
In **Chapter 2**, we will enhance this template by adding tags and building on this foundation.
