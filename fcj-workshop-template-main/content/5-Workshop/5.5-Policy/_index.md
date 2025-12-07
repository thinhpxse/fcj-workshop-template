---
title : "Implementing Fine-Grained Access Control with VPC Endpoint Policies"
date : 2025-12-06
weight : 5
chapter : false
pre : " <b> 5.5. </b> "
---

{{% notice warning %}}
⚠️ **Important:** The information provided here is for reference purposes only. Please **do not copy verbatim** for your report including this warning message.
{{% /notice %}}

In this section, you will implement security best practices by applying restrictive VPC endpoint policies to control which S3 buckets can be accessed through your Gateway VPC Endpoint. This demonstrates the principle of least privilege in cloud architecture.

**Understanding VPC Endpoint Policies:**

VPC endpoint policies are IAM resource policies that provide granular access control for traffic flowing through VPC endpoints. They work as an additional security layer:

- **Default Behavior**: When you create an endpoint without a custom policy, AWS applies a permissive default policy allowing full access to all resources of the target service
- **Policy Type**: JSON-based IAM resource policies attached directly to the endpoint
- **Enforcement Point**: Evaluated when traffic passes through the endpoint (not on the AWS service side)
- **Combined Evaluation**: Works in conjunction with IAM user/role policies and S3 bucket policies (all must allow an action)
- **Scope**: Can restrict access by resource (specific buckets), principal (specific IAM identities), or action (specific S3 operations)

**Why Use Endpoint Policies?**

Endpoint policies are critical for enterprise security architectures:

1. **Defense in Depth**: Additional security layer beyond IAM and resource policies
2. **Network-Level Control**: Restrict what can be accessed through specific network paths
3. **Compliance Requirements**: Meet regulatory requirements for data access controls
4. **Prevent Data Exfiltration**: Limit which buckets can be accessed from VPC resources
5. **Cost Optimization**: Restrict access to authorized resources only, preventing misuse

**Workshop Scenario:**

You'll create a restrictive endpoint policy that:
- Allows access to a specific S3 bucket only
- Blocks access to all other S3 buckets
- Demonstrates how endpoint policies enforce access controls

![Endpoint Policy Architecture](/images/5-Workshop/5.5-Policy/s3-bucket-policy.png)

---

#### Part 1: Establish Baseline Connectivity

Before applying a restrictive policy, verify current access to demonstrate the before/after comparison.

**Step 1: Connect to Test Instance**

1. Open **AWS Systems Manager** > **Session Manager**
2. Click **Start session**
3. Select the EC2 instance named **Test-Gateway-Endpoint**
   - This instance is in the VPC with the Gateway Endpoint
   - It routes S3 traffic through the Gateway Endpoint via route table configuration
4. Click **Start session**

**Step 2: Verify Access to Original S3 Bucket**

In the Session Manager terminal, list the contents of the bucket you created in section 5.3:

```bash
aws s3 ls s3://<your-bucket-name>
```

Replace `<your-bucket-name>` with your actual bucket name (e.g., `my-endpoint-test-bucket-123456`).

![Verify Original Bucket Access](/images/5-Workshop/5.5-Policy/test1.png)

**Expected Output:**

You should see the two 1GB test files uploaded in earlier sections:
```
2024-12-07 10:30:15 1073741824 testfile.txt
2024-12-07 11:45:22 1073741824 onprem-data.dat
```

{{% notice info %}}
**Current Access**: This works because the Gateway Endpoint currently has the default policy allowing full access (`"*"`) to all S3 resources. You're about to change this to demonstrate policy-based restrictions.
{{% /notice %}}

---

#### Part 2: Create Second S3 Bucket for Policy Testing

You'll create a second bucket to demonstrate selective access control.

**Step 1: Navigate to S3 Console**

1. Open the [Amazon S3 Console](https://s3.console.aws.amazon.com/s3/home?region=us-east-1)
2. Click **Create bucket**

**Step 2: Configure New Bucket**

1. **Bucket name**: 
   - Follow your existing naming pattern, but append `-restricted`
   - Example: If your first bucket is `my-endpoint-test-bucket-123456`, name this one `my-endpoint-test-bucket-123456-restricted`
   - Must be globally unique across all AWS accounts

2. **Region**: 
   - Ensure it's **US East (N. Virginia) us-east-1** (same as your VPC and first bucket)

3. **Other settings**:
   - Leave all other settings at default values
   - Default encryption: Enabled (SSE-S3)
   - Block Public Access: Enabled (recommended)
   - Versioning: Disabled (for workshop simplicity)

4. **Create bucket**:
   - Scroll to bottom and click **Create bucket**

![Create Second S3 Bucket](/images/5-Workshop/5.5-Policy/create-bucket.png)

**Step 3: Verify Bucket Creation**

You should see a success message and the new bucket listed in the S3 console.

![Bucket Created Successfully](/images/5-Workshop/5.5-Policy/create-bucket-success.png)

{{% notice tip %}}
**Naming Strategy**: Using a consistent naming pattern (original name + suffix) makes it easy to identify related resources and simplifies policy ARN construction. In production, use naming conventions that reflect environment, application, and purpose.
{{% /notice %}}

---

#### Part 3: Apply Restrictive Endpoint Policy

Now you'll modify the Gateway Endpoint policy to allow access only to the new bucket.

**Step 1: Navigate to VPC Endpoints**

1. Open the [VPC Console](https://us-east-1.console.aws.amazon.com/vpc/home?region=us-east-1#Endpoints:)
2. Click **Endpoints** in the left sidebar
3. Locate and select the **Gateway Endpoint** you created in section 5.3
   - Type should show "Gateway"
   - Service name should be `com.amazonaws.us-east-1.s3`

**Step 2: Access Endpoint Policy Editor**

1. With the endpoint selected, click the **Policy** tab in the bottom details pane
2. You'll see the current policy (default allows all)
3. Click **Edit policy** button

![Edit Endpoint Policy](/images/5-Workshop/5.5-Policy/policy1.png)

**Current Default Policy:**

The existing policy looks like this (allowing unrestricted access):
```json
{
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": "*",
      "Resource": "*"
    }
  ]
}
```

**Step 3: Apply Restrictive Policy**

1. **Clear the existing policy** in the policy editor

2. **Copy and paste** the following restrictive policy:

```json
{
  "Version": "2012-10-17",
  "Id": "RestrictToSpecificBucketPolicy",
  "Statement": [
    {
      "Sid": "AllowAccessToRestrictedBucketOnly",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::YOUR-RESTRICTED-BUCKET-NAME",
        "arn:aws:s3:::YOUR-RESTRICTED-BUCKET-NAME/*"
      ]
    }
  ]
}
```

3. **Replace the ARN placeholders**:
   - Find `YOUR-RESTRICTED-BUCKET-NAME` (appears twice)
   - Replace with your actual second bucket name (the one ending in `-restricted`)
   - Example result:
     ```json
     "Resource": [
       "arn:aws:s3:::my-endpoint-test-bucket-123456-restricted",
       "arn:aws:s3:::my-endpoint-test-bucket-123456-restricted/*"
     ]
     ```

4. **Save the policy**:
   - Click **Save** button

![Apply Custom Endpoint Policy](/images/5-Workshop/5.5-Policy/policy2.png)

**Understanding the Policy:**

Let's break down each component:

- **`Version`**: IAM policy language version (always use "2012-10-17")
- **`Id`**: Optional identifier for the policy
- **`Statement`**: Array of policy statements (can have multiple)
  - **`Sid`**: Statement ID (descriptive label)
  - **`Effect`**: `Allow` (grants permissions) or `Deny` (explicitly denies)
  - **`Principal`**: `"*"` means any IAM identity (still subject to IAM policies)
  - **`Action`**: `s3:*` allows all S3 operations (GetObject, PutObject, ListBucket, etc.)
  - **`Resource`**: Two ARNs:
    - First ARN: The bucket itself (for operations like ListBucket)
    - Second ARN with `/*`: All objects within the bucket (for GetObject, PutObject, etc.)

{{% notice success %}}
**Policy Applied!** The Gateway Endpoint now enforces that only the restricted bucket can be accessed through it. All attempts to access other buckets (including your original bucket) will be denied.
{{% /notice %}}

**Policy Evaluation Logic:**

When an EC2 instance tries to access S3 through the endpoint:
```
1. IAM policy on EC2 instance role: Must Allow
2. VPC Endpoint policy: Must Allow ← We just restricted this
3. S3 bucket policy: Must Allow (or not explicitly Deny)
4. S3 ACLs: Must Allow (or not explicitly Deny)

Only if ALL checks pass → Access granted
```

---

#### Part 4: Test Policy Enforcement

Now validate that the endpoint policy is enforcing access restrictions.

**Step 1: Test Access to Original Bucket (Should Fail)**

In your still-open Session Manager terminal on Test-Gateway-Endpoint, try to list the original bucket:

```bash
aws s3 ls s3://<your-original-bucket-name>
```

![Access Denied to Original Bucket](/images/5-Workshop/5.5-Policy/error.png)

**Expected Output:**

You should receive an **Access Denied** error:
```
An error occurred (AccessDenied) when calling the ListObjectsV2 operation: Access Denied
```

{{% notice note %}}
**Why Access Denied?** The VPC endpoint policy no longer includes the original bucket in its allowed resources. Even though your EC2 instance IAM role has S3 permissions, the network path (through the endpoint) blocks access.
{{% /notice %}}

**Step 2: Prepare Test Data**

Create a test file to upload to the restricted bucket:

1. **Navigate to home directory**:
   ```bash
   cd ~
   ```

2. **Create a 1GB test file**:
   ```bash
   fallocate -l 1G policy-test-file.dat
   ```

**Step 3: Test Access to Restricted Bucket (Should Succeed)**

Upload the file to the restricted bucket:

```bash
aws s3 cp policy-test-file.dat s3://<your-restricted-bucket-name>
```

Replace `<your-restricted-bucket-name>` with your second bucket name.

![Successful Upload to Restricted Bucket](/images/5-Workshop/5.5-Policy/test2.png)

**Expected Output:**

The upload should succeed:
```
upload: ./policy-test-file.dat to s3://my-bucket-restricted/policy-test-file.dat
Completed 1.0 GiB/1.0 GiB
```

**Step 4: Verify File Upload in S3 Console**

1. Go back to the **S3 Console**
2. Click on your **restricted bucket**
3. You should see the `policy-test-file.dat` object

![File Successfully Uploaded](/images/5-Workshop/5.5-Policy/test2-success.png)

{{% notice success %}}
**Policy Enforcement Confirmed!** The endpoint policy successfully:
- ✅ Allowed access to the restricted bucket
- ❌ Blocked access to the original bucket

This demonstrates fine-grained network-level access control.
{{% /notice %}}

**Step 5: Confirm Original Bucket Is Still Blocked**

As a final test, try to upload to the original bucket (should fail):

```bash
aws s3 cp policy-test-file.dat s3://<your-original-bucket-name>
```

![Access Denied to Original Bucket](/images/5-Workshop/5.5-Policy/test2-fail.png)

**Expected Output:**

Access denied error:
```
upload failed: ./policy-test-file.dat to s3://my-original-bucket/policy-test-file.dat
An error occurred (AccessDenied) when calling the PutObject operation: Access Denied
```

This confirms the endpoint policy is consistently enforcing restrictions.

---

#### Understanding Policy Behavior

**Access Patterns Summary:**

| Operation | Target | Result | Reason |
|-----------|--------|--------|--------|
| List objects | Original bucket | ❌ Denied | Not in endpoint policy Resource list |
| Upload file | Original bucket | ❌ Denied | Not in endpoint policy Resource list |
| List objects | Restricted bucket | ✅ Allowed | Explicitly allowed in endpoint policy |
| Upload file | Restricted bucket | ✅ Allowed | Explicitly allowed in endpoint policy |

**How Traffic Flows:**

When EC2 accesses S3 through the Gateway Endpoint with a restrictive policy:

```
┌─────────────────────┐
│  EC2 Instance       │
│  Test-Gateway-EP    │
└──────────┬──────────┘
           │
           │ S3 API Request
           ▼
┌─────────────────────┐
│  Route Table        │
│  Directs S3 traffic │
│  to Gateway Endpoint│
└──────────┬──────────┘
           │
           ▼
┌─────────────────────────────────┐
│  Gateway VPC Endpoint           │
│  Evaluates Endpoint Policy      │
│                                 │
│  IF bucket IN allowed list:     │
│    → Forward to S3              │
│  ELSE:                          │
│    → Return Access Denied       │
└─────────────────────────────────┘
           │
           │ (If allowed)
           ▼
┌─────────────────────┐
│  Amazon S3 Service  │
│  (Backend)          │
└─────────────────────┘
```

**Key Observations:**

1. **Network-Level Enforcement**: The endpoint policy blocks traffic before it even reaches S3
2. **Independent of IAM**: Even if IAM policies allow access, endpoint policy can block it
3. **All-or-Nothing per Bucket**: You cannot selectively allow only certain operations (e.g., read-only); the policy applies to all allowed actions
4. **Principal Agnostic**: With `"Principal": "*"`, the restriction applies to all identities using the endpoint

---

#### Production Use Cases for Endpoint Policies

**Enterprise Scenarios:**

1. **Data Residency Compliance**:
   ```json
   {
     "Statement": [{
       "Effect": "Allow",
       "Principal": "*",
       "Action": "s3:*",
       "Resource": "arn:aws:s3:::eu-compliant-data-bucket/*",
       "Condition": {
         "StringEquals": {
           "s3:ExistingObjectTag/DataClassification": "EUPersonalData"
         }
       }
     }]
   }
   ```

2. **Prevent Data Exfiltration**:
   - Whitelist only approved corporate buckets
   - Block access to personal or external buckets
   - Audit endpoint policy changes with CloudTrail

3. **Multi-Tenant Environments**:
   ```json
   {
     "Statement": [{
       "Effect": "Allow",
       "Principal": "*",
       "Action": "s3:*",
       "Resource": [
         "arn:aws:s3:::tenant-a-data/*",
         "arn:aws:s3:::shared-resources/*"
       ]
     }]
   }
   ```

4. **Development vs Production Isolation**:
   - Dev VPC endpoint: Access only to dev/test buckets
   - Prod VPC endpoint: Access only to production buckets
   - Prevents accidental cross-environment access

**Advanced Policy Patterns:**

**Condition-Based Access:**
```json
{
  "Statement": [{
    "Effect": "Allow",
    "Principal": "*",
    "Action": ["s3:GetObject", "s3:PutObject"],
    "Resource": "arn:aws:s3:::sensitive-bucket/*",
    "Condition": {
      "StringEquals": {
        "aws:SourceVpce": "vpce-xxxxxxxx"
      }
    }
  }]
}
```

**Read-Only Access:**
```json
{
  "Statement": [{
    "Effect": "Allow",
    "Principal": "*",
    "Action": [
      "s3:GetObject",
      "s3:ListBucket"
    ],
    "Resource": [
      "arn:aws:s3:::readonly-bucket",
      "arn:aws:s3:::readonly-bucket/*"
    ]
  }]
}
```

---

#### Key Takeaways

**What You Accomplished:**

✅ **Baseline Testing**: Verified unrestricted access with default endpoint policy  
✅ **Bucket Creation**: Created second S3 bucket for policy testing  
✅ **Policy Application**: Applied restrictive endpoint policy limiting access to specific bucket  
✅ **Enforcement Validation**: Confirmed policy blocks unauthorized buckets and allows authorized bucket  
✅ **Security Best Practice**: Demonstrated principle of least privilege at network level  

**Security Principles Demonstrated:**

1. **Defense in Depth**: Endpoint policies add network-level control layer
2. **Least Privilege**: Grant only necessary access, not broad permissions
3. **Explicit Allow**: Whitelist approach (specify what's allowed, block everything else)
4. **Network Segmentation**: Control data flow at VPC endpoint level
5. **Auditability**: Policy changes logged in CloudTrail for compliance

**Production Recommendations:**

- **Version Control**: Store endpoint policies in Git with code review process
- **Policy Testing**: Test policies in dev/staging before production deployment
- **Monitoring**: Set up CloudWatch alarms for AccessDenied errors indicating policy violations
- **Documentation**: Maintain clear documentation of which buckets are accessible through which endpoints
- **Regular Review**: Audit endpoint policies quarterly to remove unnecessary access
- **Condition Keys**: Use advanced IAM condition keys for time-based, IP-based, or tag-based restrictions
- **Deny Statements**: Consider explicit Deny statements for high-security requirements

This section demonstrated how VPC endpoint policies provide critical access control for enterprise cloud architectures, enabling you to enforce organizational security policies at the network infrastructure level!


