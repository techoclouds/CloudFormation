
# Chapter 2: Creating and Tagging AWS Resources

## Objective
Learn how to deploy an S3 bucket and EC2 instance, and understand how to tag resources using CloudFormation.

---

## Step 1: Updated Template Components
In this chapter, we’ll:
1. Deploy an **S3 bucket** with tags.
2. Create an **EC2 instance**.
3. Use the `Tags` property to add metadata to the resources.

---

## Step 2: Hands-On Example

### YAML Template for S3 and EC2 with Tags

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: CloudFormation Template for S3 and EC2 Resources with Tags

Resources:
  # S3 Bucket Resource
  MyS3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: my-tagged-s3-bucket
      Tags:
        - Key: Environment
          Value: Development
        - Key: Project
          Value: CloudFormationDemo

  # EC2 Instance Resource
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t2.micro
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
Save the above YAML code into a file named `s3-ec2-with-tags.yaml`.

---

### Step 3.2: Deploy the Template
#### **Using AWS Console**:
1. Go to the **CloudFormation** service.
2. Click **Create stack** > **With new resources (standard)**.
3. Upload the `s3-ec2-with-tags.yaml` file.
4. Follow the prompts to create the stack.

#### **Using AWS CLI**:
Run the following command:
```bash
aws cloudformation create-stack --stack-name TaggedResourcesStack --template-body file://s3-ec2-with-tags.yaml
```

---

### Step 3.3: Validate Deployment
1. **Verify S3 Bucket**:
   - Go to the **S3 service**.
   - Check if the bucket `my-tagged-s3-bucket` is created and has the specified tags.
2. **Verify EC2 Instance**:
   - Go to the **EC2 service**.
   - Verify the instance is running.
   - Check the **Tags** tab for the instance to ensure the tags are applied.

---

## Step 4: Template Validation
Validate the template before deployment using the AWS CLI:
```bash
aws cloudformation validate-template --template-body file://s3-ec2-with-tags.yaml
```

---

## Next Steps
In **Chapter 3**, we will introduce **parameters** to make the template dynamic and reusable.
