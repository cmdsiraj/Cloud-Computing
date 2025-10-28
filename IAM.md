# AWS Identity and Access Management (IAM) — Notes

**AWS Identity and Access Management (IAM)** is a service that helps you securely control ***who can access*** your AWS resources and ***what actions*** they can perform.

IAM = Who can do what on which AWS resources.

## 🧠 IAM in a Nutshell

We have **users** (people or applications) and **AWS resources** (like S3 buckets, EC2 instances, DynamoDB tables, etc.).  
We **don’t want everyone to have full access** to everything — that’s insecure and risky.

So, we use **Identity and Access Management (IAM)** to control **who can do what** on **which resources**.

---

### 🔐 How IAM Works

1. **Policies define permissions**
   - A **policy** is a JSON document that says **“who can access what, and how.”**
   - Example: *Allow `s3:GetObject` on bucket `my-bucket`.*

2. **Attach policies to identities**
   - You can attach policies to:
     - **Users** → individual people  
     - **Groups** → collections of users  
     - **Roles** → temporary identities used by users or AWS services  

3. **Roles are special**
   - A **role** also has permissions (through attached policies).  
   - Instead of belonging to one user, **any trusted entity** (user, AWS service, or external account) can **assume** that role temporarily.
   - Example: An **EC2 instance** assumes a role to access S3.  

---

### 🧩 Key Concepts Summary

| Concept | Purpose | Can Have Policy? | Can Assume Role? | Example |
|----------|----------|------------------|------------------|----------|
| **User** | Human identity | ✅ Yes | ✅ Yes | You logging into AWS |
| **Group** | Set of users | ✅ Yes | ❌ No | “Developers” group |
| **Role** | Temporary identity | ✅ Yes | ❌ (it’s assumed by others) | EC2 role to access S3 |
| **Policy** | Defines permissions | — | — | “Allow S3:GetObject” |
| **Principal** | The entity making requests | — | — | IAM user, role, AWS service |

---

### 💡 In Short
> IAM defines **who can access what resources, and under what conditions.**
> It uses **policies** attached to **users, groups, or roles**, and **roles** can also be assumed by AWS services (like EC2, Lambda) or other users.

---

## 🧱 Structure of an IAM Policy

### Basic Format

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow" | "Deny",
      "Action": "service:operation",
      "Resource": "arn:aws:service:region:account-id:resource",
      "Condition": {
        "condition_type": {
          "key": "value"
        }
      }
    }
  ]
}
```

### Components

| Key | Description | Example |
|------|--------------|----------|
| **Version** | Defines policy language version | `"2012-10-17"` |
| **Statement** | One or more permission blocks | `[ { ... } ]` |
| **Effect** | Allow or Deny | `"Effect": "Allow"` |
| **Action** | Specific AWS actions | `"Action": "s3:GetObject"` |
| **Resource** | ARN of resources | `"Resource": "arn:aws:s3:::my-bucket/*"` |
| **Condition** | (Optional) Filters by time, IP, tag, etc. | `"Condition": {"IpAddress": {"aws:SourceIp": "192.168.1.0/24"}}` |

---

### Example 1: Simple S3 Read Policy

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

### Example 2: EC2 Start/Stop with Condition

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["ec2:StartInstances", "ec2:StopInstances"],
      "Resource": "arn:aws:ec2:us-east-1:123456789012:instance/*",
      "Condition": {
        "StringEquals": { "ec2:ResourceTag/Environment": "Dev" }
      }
    }
  ]
}
```

---

### Optional Elements

| Element | Purpose | Example |
|----------|----------|----------|
| **Sid** | Label for a statement | `"Sid": "S3ReadAccess"` |
| **NotAction** | All except listed actions | `"NotAction": "s3:DeleteObject"` |
| **NotResource** | All except listed resources | `"NotResource": "arn:aws:s3:::private-bucket/*"` |

---

### Quick Template

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "UniqueStatementName",
      "Effect": "Allow",
      "Action": ["service:Action1", "service:Action2"],
      "Resource": ["arn:aws:service:::resource"],
      "Condition": { ... } 
    }
  ]
}
```
