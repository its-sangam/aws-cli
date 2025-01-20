# AWS CLI Setup and Common Commands

## Setting up AWS CLI Locally

### 1. Install AWS CLI
Follow the official documentation to install the AWS CLI based on your operating system:
- [AWS CLI Installation Guide](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

### 2. Configure AWS CLI
Run the following command to configure the AWS CLI with your credentials:
```bash
aws configure --profile profile-name
```
You will be prompted to enter the following:
- **AWS Access Key ID**
- **AWS Secret Access Key**
- **Default region name** (e.g., `us-east-1` or `ap-northeast-1`)
- **Default output format** (e.g., `json`, `text`, or `table`)

### 3. Test the Configuration
Verify that the AWS CLI is correctly configured by running:
```bash
aws sts get-caller-identity --profile profile-name
```
This command will return the identity (UserId, Account, and Arn) associated with the provided credentials.

---

## **S3 Operations**

### List S3 Buckets
```bash
aws s3 ls --profile profile-name --region region-name
```

### Create an S3 Bucket
```bash
aws s3 mb s3://bucket-name --profile profile-name --region region-name
```

### Remove an S3 Bucket
```bash
aws s3 rb s3://bucket-name --force --profile profile-name --region region-name
```

---

## **EC2 Operations**

### List EC2 Instances
```bash
aws ec2 describe-instances --profile profile-name --region region-name
```

### Get Instance ID, Name, and Status for All EC2 Instances
```bash
aws ec2 describe-instances --query "Reservations[*].Instances[*].{ID:InstanceId,Name:Tags[?Key=='Name']|[0].Value,State:State.Name}" --output table --profile profile-name --region region-name
```

### Get Instance ID, Name, and Status for EC2 Instance with Specific Status
```bash
aws ec2 describe-instances --query "Reservations[*].Instances[?State.Name=='${status}'].{ID:InstanceId,Name:Tags[?Key=='Name']|[0].Value,State:State.Name}" --output table --profile profile-name --region region-name
```
**status**: `pending`, `running`, `stopping`, `stopped`, `shutting-down`, `terminated`, `starting`

### Start an EC2 Instance
```bash
aws ec2 start-instances --instance-ids instance-id --profile profile-name --region region-name
```

### Stop an EC2 Instance
```bash
aws ec2 stop-instances --instance-ids instance-id --profile profile-name --region region-name
```

### Terminate an EC2 Instance
```bash
aws ec2 terminate-instances --instance-ids instance-id --profile profile-name --region region-name
```

---

## **IAM Operations**

### List IAM Users
```bash
aws iam list-users --profile profile-name
```

### List IAM Groups
```bash
aws iam list-groups --profile profile-name
```

### Get Details of a Specific IAM User
```bash
aws iam get-user --user-name user-name --profile profile-name
```

### List Attached Policies for a User
To list all policies attached to a specific IAM user:
```bash
aws iam list-attached-user-policies --profile profile-name --user-name user-name
```

---

## **Additional EC2 Operations**

### 6. List Running EC2 Instances
To filter and list only running EC2 instances:
```bash
aws ec2 describe-instances --filters Name=instance-state-name,Values=running --profile profile-name --region region-name
```

### 7. List Security Groups
To list all security groups in a region:
```bash
aws ec2 describe-security-groups --profile profile-name --region region-name
```

### 8. List Key Pairs
To list all key pairs associated with your AWS account in a region:
```bash
aws ec2 describe-key-pairs --profile profile-name --region region-name
```

---

## **S3 Operations (Cont'd)**

### 9. List S3 Objects in a Bucket
To list all objects in a specific S3 bucket:
```bash
aws s3 ls s3://bucket-name --profile profile-name --region region-name
```

### 10. Download an Object from S3
To download a specific file from an S3 bucket:
```bash
aws s3 cp s3://bucket-name/object-key ./local-path --profile profile-name --region region-name
```

---

## **CloudFormation & Load Balancer Operations**

### 11. List CloudFormation Stacks
To list all CloudFormation stacks in a region:
```bash
aws cloudformation list-stacks --profile profile-name --region region-name
```

### 12. List Elastic Load Balancers (ELBs)
To list all load balancers in a region:
```bash
aws elb describe-load-balancers --profile profile-name --region region-name
```

---

## **Advanced Queries**

### List EC2 Instances with Specific Paid Services (Indirect Methods)

To check if an EC2 instance is associated with Elastic IPs or EBS volumes (which may incur additional charges):
- **Elastic IPs:**
  ```bash
  aws ec2 describe-addresses --query "Addresses[?InstanceId=='i-xxxxxxxxxxxx'].{PublicIP:PublicIp,InstanceId:InstanceId}" --output table --profile profile-name --region region-name
  ```
  
- **EBS Volumes:**
  ```bash
  aws ec2 describe-volumes --query "Volumes[?Attachments[?InstanceId=='i-xxxxxxxxxxxx']].{VolumeId:VolumeId,Size:Size,State:State}" --output table --profile profile-name --region region-name
  ```

For third-party marketplace services, monitoring tools like **AWS Systems Manager** or **Cost Explorer** are recommended for deeper analysis.

---

## Notes
- Replace `profile-name` with the name of your configured AWS CLI profile.
- Replace `region-name` with the AWS region where your resources are located (e.g., `us-east-1`, `ap-south-1`).
- Use the `--output` flag to specify output formats (e.g., `json`, `table`, `text`).
- Use the `--region` flag to specify the AWS region for operations.

For more information on AWS CLI commands and their options, refer to the [AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/).