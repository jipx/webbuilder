# 🚀 Application Stack: ALB + Auto Scaling Group on AWS (CloudFormation)

This CloudFormation template deploys a **web application infrastructure** using an **Application Load Balancer (ALB)** and an **Auto Scaling Group (ASG)**. It uses a pre-approved **Golden AMI** stored in AWS Systems Manager (SSM) Parameter Store and imports VPC and subnet resources from a separate networking stack.

---

## 🧱 Architecture Overview

**Resources Deployed:**

- Application Load Balancer (ALB)
- Target Group with health checks
- Auto Scaling Group (ASG) in two private subnets
- Launch Template using a Golden AMI from SSM
- EC2 IAM Role with SSM access
- Security groups for ALB and EC2 access control

```
  Internet
     |
+------------+
|   ALB (80) |
+------------+
     |
+---------------------+
| Target Group (HTTP) |
+---------------------+
     |
+-------------------------------+
| Auto Scaling Group (EC2 x2+)  |
+-------------------------------+
```

---

## 🧾 Template Details

**File Name:** `app-stack.yaml`

**Imports from:**  
Another stack named `studentrecords-vpc`, which exports:

- `VpcId`
- `PublicSubnet1Id`, `PublicSubnet2Id`
- `PrivateSubnet1Id`, `PrivateSubnet2Id`

---

## 🔧 Parameters

| Parameter              | Description                                      | Default               |
|------------------------|--------------------------------------------------|-----------------------|
| `VpcStackName`         | Name of the VPC stack to import subnet values    | `studentrecords-vpc`  |
| `AmiSsmParameterName`  | SSM parameter holding Golden AMI ID              | `/student-records/golden-ami-id` |
| `ProjectTag`           | Tag for resource identification                  | `StudentRecordsApp`   |
| `EnvironmentTag`       | Deployment environment tag                       | `Dev`                 |
| `InstanceType`         | EC2 instance type                                | `t3.micro`            |
| `DesiredCapacity`      | Number of instances to run by default            | `2`                   |
| `MinSize`              | Minimum number of EC2 instances                  | `2`                   |
| `MaxSize`              | Maximum number of EC2 instances                  | `4`                   |

---

## 🚀 Deployment Instructions

### 1. Ensure VPC Stack Exists
This template depends on another stack (`studentrecords-vpc`) that provides VPC and subnet resources.

### 2. Deploy This Stack

#### Option A: AWS Console
1. Go to **CloudFormation > Create Stack > With new resources**.
2. Upload `app-stack.yaml`.
3. Provide the stack name and parameters.
4. Click **Next** and then **Create Stack**.

#### Option B: AWS CLI
```bash
aws cloudformation create-stack \
  --stack-name studentrecords-app \
  --template-body file://app-stack.yaml \
  --capabilities CAPABILITY_NAMED_IAM
```

---

## 🛡️ Security Groups

| Group             | Purpose                            |
|------------------|------------------------------------|
| `AlbSecurityGroup` | Allows HTTP from the internet     |
| `AppSecurityGroup` | Allows HTTP from ALB only         |

---

## 🔄 Auto Scaling Group Behavior

- Launched in **private subnets**
- Uses Golden AMI from **SSM Parameter Store**
- Starts Apache HTTP server on boot
- Scales automatically between 2 and 4 instances

---

## 🔍 Outputs

| Output Name             | Description                          |
|-------------------------|--------------------------------------|
| `AlbDNSName`            | Public DNS to access the application |
| `AutoScalingGroupName`  | Name of the EC2 auto scaling group   |
| `TargetGroupArn`        | Target group ARN for use in rules    |
| `TargetGroupFullName`   | Full name for CloudWatch alarms      |
| `AppSecurityGroupId`    | Useful for referencing in other stacks|

---

## ✅ Best Practices Followed

- 🧼 **Modular design**: Uses imports/exports between stacks
- 🔐 **Controlled access**: Security groups enforce least privilege
- ♻️ **Reusable AMI**: Via SSM Parameter Store
- 🌐 **Scalable**: Auto Scaling Group for elasticity
- 🩺 **Health checks**: Target group uses `/` as health path

---

## 🧪 Suggested Improvements

- Add HTTPS listener with an ACM certificate for secure access
- Add CloudWatch Alarms and scaling policies
- Add tags to all resources for cost tracking and automation
- Add VPC endpoints or logging for enhanced observability
- Install CloudWatch Agent in Launch Template for logs and metrics

---

## 🧠 Learning Outcomes

- How to use CloudFormation to manage scalable compute resources
- How to use ALB + ASG together with SSM and Launch Templates
- How to integrate modular stacks with cross-stack references

---

## 📚 References

- [AWS ALB Docs](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html)
- [Auto Scaling Docs](https://docs.aws.amazon.com/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html)
- [CloudFormation Docs](https://docs.aws.amazon.com/cloudformation/)
- [SSM Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html)
