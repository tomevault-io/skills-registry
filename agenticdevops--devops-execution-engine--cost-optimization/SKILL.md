---
name: cost-optimization
description: Cloud cost analysis and optimization strategies Use when this capability is needed.
metadata:
  author: agenticdevops
---

# Cost Optimization

Cloud cost analysis and optimization strategies.

## When to Use This Skill

Use this skill when:
- Analyzing cloud spending
- Finding cost savings opportunities
- Right-sizing resources
- Investigating cost spikes

## AWS Cost Analysis

### Current Month Costs

```bash
# Total month-to-date
aws ce get-cost-and-usage \
  --time-period Start=$(date +%Y-%m-01),End=$(date +%Y-%m-%d) \
  --granularity MONTHLY \
  --metrics BlendedCost \
  --query 'ResultsByTime[].Total.BlendedCost.Amount' \
  --output text
```

### Cost by Service

```bash
aws ce get-cost-and-usage \
  --time-period Start=$(date +%Y-%m-01),End=$(date +%Y-%m-%d) \
  --granularity MONTHLY \
  --metrics BlendedCost \
  --group-by Type=DIMENSION,Key=SERVICE \
  --query 'ResultsByTime[].Groups[].{Service:Keys[0],Cost:Metrics.BlendedCost.Amount}' \
  --output table
```

### Cost Trend (Daily)

```bash
aws ce get-cost-and-usage \
  --time-period Start=$(date -d '7 days ago' +%Y-%m-%d),End=$(date +%Y-%m-%d) \
  --granularity DAILY \
  --metrics BlendedCost \
  --query 'ResultsByTime[].{Date:TimePeriod.Start,Cost:Total.BlendedCost.Amount}' \
  --output table
```

### Cost by Tag

```bash
# By environment
aws ce get-cost-and-usage \
  --time-period Start=$(date +%Y-%m-01),End=$(date +%Y-%m-%d) \
  --granularity MONTHLY \
  --metrics BlendedCost \
  --group-by Type=TAG,Key=Environment \
  --output table

# By team
aws ce get-cost-and-usage \
  --time-period Start=$(date +%Y-%m-01),End=$(date +%Y-%m-%d) \
  --granularity MONTHLY \
  --metrics BlendedCost \
  --group-by Type=TAG,Key=Team \
  --output table
```

## Find Unused Resources

### EC2 Instances

```bash
# Stopped instances (still incur EBS costs)
aws ec2 describe-instances \
  --filters "Name=instance-state-name,Values=stopped" \
  --query 'Reservations[].Instances[].[InstanceId,Tags[?Key==`Name`].Value|[0],LaunchTime]' \
  --output table

# Low CPU utilization (last 7 days average < 5%)
aws cloudwatch get-metric-statistics \
  --namespace AWS/EC2 \
  --metric-name CPUUtilization \
  --dimensions Name=InstanceId,Value=i-1234567890 \
  --start-time $(date -d '7 days ago' -u +%Y-%m-%dT%H:%M:%SZ) \
  --end-time $(date -u +%Y-%m-%dT%H:%M:%SZ) \
  --period 604800 \
  --statistics Average
```

### Unattached EBS Volumes

```bash
aws ec2 describe-volumes \
  --filters "Name=status,Values=available" \
  --query 'Volumes[].[VolumeId,Size,CreateTime]' \
  --output table
```

### Unused Elastic IPs

```bash
aws ec2 describe-addresses \
  --query 'Addresses[?AssociationId==`null`].[PublicIp,AllocationId]' \
  --output table
```

### Old Snapshots

```bash
# Snapshots older than 90 days
aws ec2 describe-snapshots \
  --owner-ids self \
  --query "Snapshots[?StartTime<='$(date -d '90 days ago' +%Y-%m-%d)'].[SnapshotId,VolumeSize,StartTime]" \
  --output table
```

### Unused Load Balancers

```bash
# ALBs with no targets
aws elbv2 describe-target-groups \
  --query 'TargetGroups[?length(TargetHealthDescriptions)==`0`].[TargetGroupName,LoadBalancerArns]'
```

### Unused RDS Instances

```bash
# Check for low connections
aws cloudwatch get-metric-statistics \
  --namespace AWS/RDS \
  --metric-name DatabaseConnections \
  --dimensions Name=DBInstanceIdentifier,Value=mydb \
  --start-time $(date -d '7 days ago' -u +%Y-%m-%dT%H:%M:%SZ) \
  --end-time $(date -u +%Y-%m-%dT%H:%M:%SZ) \
  --period 604800 \
  --statistics Maximum
```

## Kubernetes Cost Analysis

### Resource Requests vs Usage

```bash
# Current resource requests
kubectl get pods -A -o custom-columns=\
'NAMESPACE:.metadata.namespace,NAME:.metadata.name,CPU_REQ:.spec.containers[*].resources.requests.cpu,MEM_REQ:.spec.containers[*].resources.requests.memory'

# Actual usage
kubectl top pods -A --sort-by=cpu

# Compare requests vs usage
kubectl top pods -A --containers
```

### Over-Provisioned Pods

```bash
# Find pods with high requests but low usage
kubectl top pods -A --no-headers | awk '$3 < 10 {print $1, $2, "CPU:", $3}'
```

### Namespace Resource Usage

```bash
# By namespace
kubectl top pods -A --no-headers | awk '{ns[$1]+=$3} END {for (n in ns) print n, ns[n]"m"}' | sort -k2 -rn
```

## Right-Sizing Recommendations

### EC2 Right-Sizing

```bash
# Get recommendations
aws ce get-rightsizing-recommendation \
  --service EC2 \
  --configuration '{"RecommendationTarget":"SAME_INSTANCE_FAMILY","BenefitsConsidered":true}'
```

### General Recommendations

```bash
aws ce get-cost-forecast \
  --time-period Start=$(date +%Y-%m-%d),End=$(date -d 'next month' +%Y-%m-01) \
  --metric BLENDED_COST \
  --granularity MONTHLY
```

## Reserved Instances & Savings Plans

### RI Coverage

```bash
aws ce get-reservation-coverage \
  --time-period Start=$(date -d '30 days ago' +%Y-%m-%d),End=$(date +%Y-%m-%d) \
  --granularity MONTHLY
```

### RI Utilization

```bash
aws ce get-reservation-utilization \
  --time-period Start=$(date -d '30 days ago' +%Y-%m-%d),End=$(date +%Y-%m-%d) \
  --granularity MONTHLY
```

### Savings Plans Utilization

```bash
aws ce get-savings-plans-utilization \
  --time-period Start=$(date -d '30 days ago' +%Y-%m-%d),End=$(date +%Y-%m-%d)
```

## S3 Cost Optimization

### Bucket Sizes

```bash
# List bucket sizes
for bucket in $(aws s3api list-buckets --query 'Buckets[].Name' --output text); do
  size=$(aws cloudwatch get-metric-statistics \
    --namespace AWS/S3 \
    --metric-name BucketSizeBytes \
    --dimensions Name=BucketName,Value=$bucket Name=StorageType,Value=StandardStorage \
    --start-time $(date -d '1 day ago' -u +%Y-%m-%dT%H:%M:%SZ) \
    --end-time $(date -u +%Y-%m-%dT%H:%M:%SZ) \
    --period 86400 \
    --statistics Average \
    --query 'Datapoints[0].Average' --output text 2>/dev/null)
  echo "$bucket: ${size:-0} bytes"
done
```

### S3 Storage Lens

```bash
# Get storage lens config
aws s3control list-storage-lens-configurations --account-id $(aws sts get-caller-identity --query Account --output text)
```

## Quick Wins Checklist

### Immediate Savings

- [ ] Delete stopped EC2 instances (still pay for EBS)
- [ ] Remove unattached EBS volumes
- [ ] Release unused Elastic IPs ($3.60/month each)
- [ ] Delete old snapshots
- [ ] Review and delete unused load balancers
- [ ] Move infrequent S3 data to cheaper storage class

### Medium-Term Savings

- [ ] Right-size over-provisioned EC2 instances
- [ ] Use Spot instances for fault-tolerant workloads
- [ ] Implement Reserved Instances for stable workloads
- [ ] Enable S3 Intelligent-Tiering
- [ ] Use Aurora Serverless for variable workloads

### Kubernetes Savings

- [ ] Set appropriate resource requests/limits
- [ ] Use cluster autoscaler
- [ ] Implement pod disruption budgets
- [ ] Use spot instances for node groups
- [ ] Right-size persistent volumes

## Cost Monitoring

### Set Budget Alert

```bash
aws budgets create-budget \
  --account-id $(aws sts get-caller-identity --query Account --output text) \
  --budget '{
    "BudgetName": "Monthly-Spend",
    "BudgetLimit": {"Amount": "1000", "Unit": "USD"},
    "BudgetType": "COST",
    "TimeUnit": "MONTHLY"
  }' \
  --notifications-with-subscribers '[{
    "Notification": {
      "NotificationType": "ACTUAL",
      "ComparisonOperator": "GREATER_THAN",
      "Threshold": 80,
      "ThresholdType": "PERCENTAGE"
    },
    "Subscribers": [{"SubscriptionType": "EMAIL", "Address": "alerts@example.com"}]
  }]'
```

## Quick Reference

```bash
# This month's total
aws ce get-cost-and-usage --time-period Start=$(date +%Y-%m-01),End=$(date +%Y-%m-%d) --granularity MONTHLY --metrics BlendedCost

# Top 5 services
aws ce get-cost-and-usage --time-period Start=$(date +%Y-%m-01),End=$(date +%Y-%m-%d) --granularity MONTHLY --metrics BlendedCost --group-by Type=DIMENSION,Key=SERVICE --query 'ResultsByTime[].Groups | sort_by(@, &to_number(Metrics.BlendedCost.Amount))[-5:]'

# Unused EBS
aws ec2 describe-volumes --filters "Name=status,Values=available" --query 'Volumes[].VolumeId'

# K8s resource usage
kubectl top nodes && kubectl top pods -A --sort-by=memory | head -20
```

## Related Skills

- **aws-ops**: For AWS resource management
- **k8s-debug**: For Kubernetes resource analysis
- **terraform-workflow**: For infrastructure changes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agenticdevops) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
