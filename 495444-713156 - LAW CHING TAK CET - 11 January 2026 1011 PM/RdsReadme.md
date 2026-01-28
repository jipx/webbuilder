# 🗄️ RDS Stack: Multi-AZ MySQL Instance with Secrets Manager

This CloudFormation template provisions a **secure, high availability MySQL database** in AWS using Amazon RDS. It uses **Secrets Manager** for password storage, validates critical parameters, and ensures the database is deployed in **private subnets** across multiple Availability Zones.

---

## 📐 Architecture Overview

**What this stack deploys:**

- ✅ RDS MySQL instance (Multi-AZ for high availability)
- ✅ Secrets Manager secret for DB credentials
- ✅ Security Group allowing app-tier access (from `AppSecurityGroupId`)
- ✅ RDS Subnet Group using private subnets only

```
App (EC2/ASG)
     |
[ AppSecurityGroup ]
     |
[ DbSecurityGroup ]
     |
[  RDS MySQL (Multi-AZ)  ]
     |
[ Secrets Manager (Password) ]
```

---

## 🧾 Template Details

**File Name:** `rds-stack.yaml`  
**Dependencies:** Imports VPC and subnet IDs from another stack (`studentrecords-vpc`)

---

## ⚙️ Parameters

| Parameter           | Description                                                | Example / Default               |
|---------------------|------------------------------------------------------------|----------------------------------|
| `VpcStackName`      | Name of the stack that created the VPC                     | `studentrecords-vpc`             |
| `AppSecurityGroupId`| Security Group ID for app tier access (e.g. from `app.yaml`) | `sg-0123abcd4567efgh`            |
| `DbName`            | Name of the database (must start with a letter)            | `studentrecords`                 |
| `DbUser`            | Database master username (alphanumeric + underscore)       | `appuser`                        |

⚠️ **Strong validation** is built-in to prevent misconfigured names and security group IDs.

---

## 🔐 Secrets Manager

A secure password is generated using AWS Secrets Manager with:

- Username set to the provided `DbUser`
- 32-character random password (no `"`, `@`, `/`, or `'`)
- Reference to this password used in RDS configuration via:

```yaml
MasterUserPassword: !Sub "{{resolve:secretsmanager:${DbSecret}::password}}"
```

---

## 🛡️ Security Groups

- **DbSecurityGroup** allows MySQL (port 3306) only from the application security group you provide.
- This ensures private access from the app tier only (no public access).

---

## 📥 Subnet Configuration

- Uses `PrivateSubnet1Id` and `PrivateSubnet2Id` imported from your VPC stack.
- RDS is launched in **private subnets only** (no internet exposure).
- `MultiAZ: true` ensures standby instance is created in the second AZ.

---

## 📤 Outputs

| Output Name             | Description                          |
|-------------------------|--------------------------------------|
| `RdsEndpoint`           | DNS endpoint to connect to the DB    |
| `RdsInstanceIdentifier` | RDS instance ID (used for monitoring)|
| `DbSecretArn`           | ARN of the secret in Secrets Manager |

---

## 🧠 Learning Outcomes

By deploying this stack, you will:

- Learn how to provision a Multi-AZ RDS instance securely
- Use **Secrets Manager** to avoid hardcoding passwords
- Apply **parameter validation** in CloudFormation
- Integrate RDS securely into a private VPC architecture

---

## ✅ Best Practices Followed

- 🔐 Secrets stored securely, not in plain text
- 🧱 Infrastructure is modular and references outputs from other stacks
- 🌐 RDS is private and highly available
- 📜 Parameter validation avoids common user errors

---

## 📚 References

- [Amazon RDS Overview](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html)
- [AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/)
- [CloudFormation Parameter Constraints](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html)
