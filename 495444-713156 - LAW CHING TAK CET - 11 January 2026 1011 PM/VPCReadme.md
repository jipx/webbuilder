# 🛠️ AWS Multi-AZ VPC Infrastructure (CloudFormation)

This project deploys a **multi-AZ VPC network architecture** using AWS CloudFormation. It sets up a secure, highly available environment with public and private subnets, internet and NAT gateways, and route tables. The template is modular and exports key resources for reuse in other stacks.

---

## 🌍 Architecture Summary

The CloudFormation stack provisions:

- ✅ 1 VPC with DNS support
- ✅ 2 Public subnets (in separate Availability Zones)
- ✅ 2 Private subnets (in separate Availability Zones)
- ✅ 1 Internet Gateway (IGW)
- ✅ 2 NAT Gateways (for private subnet egress)
- ✅ 2 Route tables for public and private traffic
- ✅ Outputs for VPC and subnet IDs

---

## 🗺️ Network Diagram

```
        [ Internet ]
             |
         [ IGW (Internet Gateway) ]
             |
    +----------------------------+
    |      Public Subnets       |
    |  AZ-a           AZ-b      |
    | [Subnet1]     [Subnet2]   |
    |   |              |        |
    | [NAT GW 1]   [NAT GW 2]   |
    +----------------------------+
             |         |
    +----------------------------+
    |      Private Subnets       |
    |  AZ-a           AZ-b       |
    | [Subnet1]     [Subnet2]    |
    +----------------------------+
```

---

## ⚙️ Parameters

| Parameter             | Description                       | Default        |
|----------------------|-----------------------------------|----------------|
| `VpcCidr`            | CIDR block for the VPC            | `10.2.0.0/16`  |
| `PublicSubnet1Cidr`  | Public Subnet AZ-a CIDR           | `10.2.1.0/24`  |
| `PublicSubnet2Cidr`  | Public Subnet AZ-b CIDR           | `10.2.2.0/24`  |
| `PrivateSubnet1Cidr` | Private Subnet AZ-a CIDR          | `10.2.11.0/24` |
| `PrivateSubnet2Cidr` | Private Subnet AZ-b CIDR          | `10.2.12.0/24` |

---

## 🚀 How to Deploy

### 📌 Using AWS Console

1. Go to **AWS CloudFormation > Create Stack**.
2. Choose **Upload a template file** and select `vpc.yaml`.
3. Set a stack name (e.g., `MyVPCStack`).
4. Customize parameters if needed.
5. Click **Next** through options and create the stack.

### 🧪 Using AWS CLI

```bash
aws cloudformation create-stack \
  --stack-name MyVPCStack \
  --template-body file://vpc.yaml \
  --capabilities CAPABILITY_NAMED_IAM
```

---

## 📤 Outputs

| Export Name                       | Description                    |
|----------------------------------|--------------------------------|
| `<StackName>-VpcId`              | VPC ID                         |
| `<StackName>-PublicSubnet1Id`   | Public Subnet AZ-a             |
| `<StackName>-PublicSubnet2Id`   | Public Subnet AZ-b             |
| `<StackName>-PrivateSubnet1Id`  | Private Subnet AZ-a            |
| `<StackName>-PrivateSubnet2Id`  | Private Subnet AZ-b            |

These outputs can be imported by other stacks (e.g., app, database, or load balancer layers).

---

## ✅ Best Practices Used

- **High Availability**: Resources span two Availability Zones.
- **Security**: Private subnets have no direct internet access.
- **Modularity**: Output exports support multi-stack deployments.
- **Scalability**: Designed for use with services like ECS, RDS, or ALB.

---

## 🔧 Suggested Improvements

You can extend or enhance this stack by:

- Adding **tags** to all resources for easier management
- Enabling **VPC Flow Logs** for monitoring network traffic
- Adding **S3 VPC Endpoint** to avoid using NAT for S3 access
- Using **Conditions** to deploy 1 vs 2 NAT Gateways (e.g., dev vs prod)
- Integrating with **ALB, ECS, RDS**, etc.

---

## 🧠 Learning Goals

This project helps you learn to:

- Build secure, highly available networks in AWS
- Use CloudFormation for Infrastructure as Code (IaC)
- Follow AWS architecture best practices
- Deploy reusable, modular cloud infrastructure

---

## 📚 Helpful Links

- [AWS VPC Documentation](https://docs.aws.amazon.com/vpc/latest/userguide/)
- [CloudFormation Documentation](https://docs.aws.amazon.com/cloudformation/)
- [VPC Best Practices](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-best-practices.html)
