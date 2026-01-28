# 📊 CloudWatch Monitoring Stack (Alarms + Metrics)

This CloudFormation template creates **CloudWatch alarms** to monitor critical components of a web application infrastructure:

- Auto Scaling Group (EC2)
- Application Load Balancer (ALB)
- Amazon RDS (MySQL)

Each alarm helps detect performance issues early and supports better observability in production or testing environments.

---

## 📦 What This Stack Includes

| Resource            | Purpose                                      |
|---------------------|----------------------------------------------|
| `AsgHighCpuAlarm`   | Monitors average CPU of EC2 Auto Scaling Group |
| `Alb5xxAlarm`       | Monitors HTTP 5XX errors on Application Load Balancer |
| `RdsHighCpuAlarm`   | Monitors average CPU of RDS instance         |

These alarms are triggered based on **CloudWatch metrics** using appropriate dimensions.

---

## ⚙️ Parameters

| Parameter Name           | Description                                   |
|--------------------------|-----------------------------------------------|
| `AutoScalingGroupName`   | Name of the Auto Scaling Group                |
| `AlbFullName`            | Full resource name of the ALB (not just DNS) |
| `TargetGroupFullName`    | Full resource name of the target group (optional, not used yet) |
| `RdsInstanceIdentifier`  | RDS DB instance ID                            |

> ⚠️ Make sure you provide the **correct ALB and ASG names**, not just display names. These must match the CloudWatch dimension format.

---

## 🔔 Alarm Details

### 🚀 EC2 Auto Scaling Group: High CPU
- **Alarm Name:** `${AutoScalingGroupName}-HighCPU`
- **Metric:** `AWS/EC2 – CPUUtilization`
- **Threshold:** > 70% average for 5 minutes

### 🌐 Application Load Balancer: 5XX Errors
- **Alarm Name:** `${AlbFullName}-5XX`
- **Metric:** `AWS/ApplicationELB – HTTPCode_ELB_5XX_Count`
- **Threshold:** ≥ 10 errors over 5 minutes

### 🗄️ RDS Instance: High CPU
- **Alarm Name:** `${RdsInstanceIdentifier}-HighCPU`
- **Metric:** `AWS/RDS – CPUUtilization`
- **Threshold:** > 70% average for 5 minutes

---

## 📤 Deployment Instructions

### Via AWS Console
1. Go to **CloudFormation > Create Stack > With new resources**.
2. Upload your template file (e.g., `monitoring.yaml`).
3. Fill in the required parameter values for ASG, ALB, and RDS identifiers.
4. Click **Next** through the options and create the stack.

### Via AWS CLI
```bash
aws cloudformation create-stack \
  --stack-name monitoring-stack \
  --template-body file://monitoring.yaml \
  --parameters \
      ParameterKey=AutoScalingGroupName,ParameterValue=studentrecords-asg \
      ParameterKey=AlbFullName,ParameterValue=app/alb-name/123456abcdef \
      ParameterKey=TargetGroupFullName,ParameterValue=targetgroup/app-target/abcdef123456 \
      ParameterKey=RdsInstanceIdentifier,ParameterValue=studentrecords-db
```

---

## ✅ Suggested Improvements

You can extend this stack by adding:

- 📈 **CloudWatch Dashboards** for visual monitoring
- 📬 **SNS Notifications** for alarm actions (email/SMS)
- 🔁 **Auto Scaling Policies** triggered by alarms
- 🧪 **Additional Metrics** (e.g., latency, request count, disk space)

---

## 🧠 Learning Outcomes

- How to create **CloudWatch alarms** in CloudFormation
- How to monitor **EC2, ALB, and RDS** metrics
- How to use metric **dimensions** to target specific AWS resources

---

## 📚 References

- [CloudWatch Alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html)
- [Application Load Balancer Metrics](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-cloudwatch-metrics.html)
- [RDS Metrics](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/MonitoringOverview.html)
- [CloudFormation + CloudWatch](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-cw-alarm.html)
