---
title : "Creating Interface VPC Endpoint for S3 Access"
date : 2025-12-06
weight : 2
chapter : false
pre : " <b> 5.4.2 </b> "
---

{{% notice warning %}}
⚠️ **Important:** The information provided here is for reference purposes only. Please **do not copy verbatim** for your report including this warning message.
{{% /notice %}}

In this section, you will deploy an S3 Interface VPC Endpoint within the cloud VPC. This endpoint enables private connectivity from your simulated on-premises network to Amazon S3, eliminating the need for internet gateways or NAT devices.

**Why Interface Endpoints for Hybrid Access?**

Interface VPC Endpoints create elastic network interfaces (ENIs) with private IP addresses in your VPC. This architecture provides several advantages for hybrid cloud scenarios:

- **Private IP Addressing**: On-premises systems can resolve S3 service names to private IPs within your VPC CIDR range
- **DNS Integration**: Works seamlessly with Route 53 Resolver for hybrid DNS resolution
- **Security**: Traffic flows through private connections (VPN/Direct Connect) without traversing the public internet
- **PrivateLink Technology**: Leverages AWS PrivateLink for secure, scalable service access
- **Fine-Grained Access Control**: Apply security groups and endpoint policies for granular access management

{{% notice info %}}
**Interface vs Gateway Endpoints**: While you previously used a Gateway Endpoint (which modifies route tables), Interface Endpoints use ENIs with private IPs. This makes them suitable for on-premises access through VPN/Direct Connect, as they can be reached via standard IP routing.
{{% /notice %}}

---

#### Step-by-Step Interface Endpoint Creation

**Step 1: Navigate to VPC Endpoints Console**

1. Open the [Amazon VPC Console](https://us-east-1.console.aws.amazon.com/vpc/home?region=us-east-1#Endpoints:)
2. In the left navigation menu, click **Endpoints**
3. Click the **Create endpoint** button in the top-right corner

**Step 2: Configure Endpoint Basic Settings**

In the Create Endpoint configuration page:

1. **Name your endpoint** (optional but recommended):
   - Enter a descriptive name: `S3-Interface-Endpoint-HybridAccess`
   - This helps identify the endpoint's purpose in production environments

2. **Select Service Category**:
   - Choose **AWS services**
   - This filters the service list to AWS-managed endpoints

![Endpoint Basic Configuration](/images/5-Workshop/5.4-S3-onprem/s3-interface-endpoint1.png)

{{% notice tip %}}
**Naming Convention Best Practice**: Use descriptive names that indicate the service, endpoint type, and purpose, e.g., `S3-Interface-CloudVPC` or `S3-InterfaceEP-Production`.
{{% /notice %}}

**Step 3: Locate and Select the S3 Interface Service**

1. **Search for S3 service**:
   - In the **Services** search box, type: `S3`
   - Press **Enter** or click the search icon

2. **Identify the correct service**:
   - Look for the service named: `com.amazonaws.us-east-1.s3`
   - **Verify the Type column shows "Interface"** (not Gateway)
   - Click the radio button to select this service

![Select S3 Interface Service](/images/5-Workshop/5.4-S3-onprem/s3-interface-endpoint2.png)

{{% notice warning %}}
**Critical Selection**: AWS offers both Gateway and Interface endpoint types for S3. Ensure you select the **Interface** type. Gateway endpoints won't work for on-premises access scenarios because they only modify VPC route tables.
{{% /notice %}}

**Step 4: Configure VPC and DNS Settings**

1. **Select Target VPC**:
   - From the **VPC** dropdown, select **VPC Cloud**
   - **Do NOT select "VPC On-prem"** - the endpoint must be in the cloud VPC

{{% notice warning %}}
**VPC Selection is Critical**: The Interface Endpoint must be created in **VPC Cloud**, not VPC On-prem. On-premises systems will access this endpoint through the VPN tunnel you configured earlier.
{{% /notice %}}

2. **Configure DNS Settings**:
   - Expand the **Additional settings** section
   - **Uncheck "Enable DNS name"** (disable it)
   - Why? You'll manually configure DNS using Route 53 Private Hosted Zones for more control

![VPC and DNS Configuration](/images/5-Workshop/5.4-S3-onprem/s3-interface-endpoint3.png)

{{% notice info %}}
**DNS Configuration Choice**: In this workshop, you're using Route 53 Resolver with Private Hosted Zones for DNS resolution (configured in section 5.4.1). This provides greater flexibility and mirrors enterprise hybrid DNS architectures. Alternatively, enabling "Enable DNS name" would automatically create Route 53 private DNS records, but we want explicit control for learning purposes.
{{% /notice %}}

**Step 5: Select Availability Zones and Subnets**

**Configure High Availability**:
- Select **2 subnets** in different Availability Zones:
  - **Availability Zone**: `us-east-1a`
    - Choose the corresponding private subnet in VPC Cloud
  - **Availability Zone**: `us-east-1b`
    - Choose the corresponding private subnet in VPC Cloud

![Subnet Selection](/images/5-Workshop/5.4-S3-onprem/s3-interface-endpoint4.png)

**Why Multiple AZs?**

Deploying Interface Endpoint ENIs in multiple Availability Zones provides:
- **High Availability**: If one AZ experiences issues, traffic can route through the other
- **Fault Tolerance**: Automatic failover between ENIs in different AZs
- **Geographic Redundancy**: Reduces latency by having endpoints in multiple locations
- **Production Best Practice**: AWS recommends multi-AZ deployment for critical services

{{% notice success %}}
**Best Practice**: Always deploy Interface Endpoints across at least 2 Availability Zones for production workloads. This ensures your on-premises applications maintain S3 connectivity even during AZ failures.
{{% /notice %}}

**Step 6: Apply Security Group**

1. **Select Security Group**:
   - In the **Security groups** dropdown, choose **SGforS3Endpoint**
   - This security group was created by the CloudFormation template
   - It contains inbound rules allowing HTTPS (port 443) traffic from on-premises CIDR

![Security Group Selection](/images/5-Workshop/5.4-S3-onprem/s3-interface-endpoint5.png)

**Security Group Purpose**:

The `SGforS3Endpoint` security group controls which sources can communicate with the Interface Endpoint ENIs:

```
Inbound Rules:
- Protocol: TCP
- Port: 443 (HTTPS)
- Source: VPC On-prem CIDR (e.g., 10.1.0.0/16)
- Purpose: Allow S3 API calls from on-premises systems
```

{{% notice note %}}
**Security Layer**: Security groups act as virtual firewalls for your Interface Endpoint. Even though traffic comes through a VPN, the security group provides an additional layer of control, following AWS defense-in-depth principles.
{{% /notice %}}

**Step 7: Configure Endpoint Policy**

1. **Policy Configuration**:
   - Leave the **Policy** set to **Full access** (default)
   - This allows all S3 actions through the endpoint

2. **Understanding Endpoint Policies**:
   - Endpoint policies control what AWS API actions can be performed through the endpoint
   - They work in conjunction with IAM policies (both must allow an action)
   - For this workshop, full access simplifies testing

3. **Click Create endpoint**

![Create Endpoint](/images/5-Workshop/5.4-S3-onprem/s3-interface-endpoint-success.png)

{{% notice tip %}}
**Production Consideration**: In production environments, use restrictive endpoint policies following the principle of least privilege. For example, limit to specific S3 buckets or actions like `s3:GetObject` and `s3:PutObject` only.
{{% /notice %}}

**Example Restrictive Policy:**
```json
{
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": [
        "s3:GetObject",
        "s3:PutObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::my-company-data/*",
        "arn:aws:s3:::my-company-data"
      ]
    }
  ]
}
```

---

#### Verify Endpoint Creation

**Check Endpoint Status:**

1. The endpoint creation typically takes 2-3 minutes
2. Refresh the Endpoints page periodically
3. Wait until the **Status** changes from `Pending` to **Available**
4. Once available, note the **Endpoint ID** (format: `vpce-xxxxxxxxxxxxxxxxx`)

**Inspect Endpoint Details:**

Click on the newly created endpoint to view:
- **DNS names**: Private DNS names assigned to the endpoint
- **Subnets**: Verify both AZs are listed with their ENI IDs
- **Network interfaces**: Each subnet has a dedicated ENI with a private IP
- **Security groups**: Confirm `SGforS3Endpoint` is attached

**Record Private IP Addresses:**

In the **Subnets** section, note the private IP addresses assigned to each ENI. You'll use these in DNS configuration (next step).

---

#### Understanding What You Created

**Architecture Overview:**

You've deployed an Interface VPC Endpoint with the following components:

1. **Two Elastic Network Interfaces (ENIs)**:
   - One ENI in `us-east-1a` subnet with a private IP (e.g., `10.0.1.100`)
   - One ENI in `us-east-1b` subnet with a private IP (e.g., `10.0.2.100`)

2. **Security Group Protection**:
   - ENIs protected by `SGforS3Endpoint` security group
   - Only allows HTTPS traffic from on-premises CIDR

3. **Private Connectivity Path**:
   ```
   On-Premises Systems → VPN Tunnel → VPC Cloud 
   → Interface Endpoint ENI → AWS PrivateLink → S3 Service
   ```

**How On-Premises Access Works:**

When an on-premises application makes an S3 API call:
1. DNS query for `s3.amazonaws.com` is sent to Outbound Resolver
2. Outbound Resolver forwards through VPN to Inbound Resolver
3. Inbound Resolver queries Private Hosted Zone (to be configured next)
4. Private Hosted Zone returns Interface Endpoint private IP
5. Application sends HTTPS request to private IP through VPN
6. Request reaches Interface Endpoint ENI
7. PrivateLink routes request to S3 service
8. Response follows reverse path back to on-premises

{{% notice success %}}
**Congratulations!** You've successfully created an S3 Interface VPC Endpoint for hybrid cloud access. This endpoint provides a secure, private connection from your on-premises environment to Amazon S3, eliminating internet exposure and enabling enterprise-grade hybrid architectures.
{{% /notice %}}

---

#### Next Steps

With the Interface Endpoint created, you'll proceed to:
1. **Configure DNS records** in Route 53 Private Hosted Zone to resolve S3 DNS names to endpoint IPs
2. **Test connectivity** from the on-premises EC2 instance to S3 through the private endpoint
3. **Verify traffic flow** using VPC Flow Logs and endpoint metrics
4. **Validate security** by confirming all traffic stays within AWS private networks

The Interface Endpoint is now ready to serve as your private gateway to S3 for hybrid workloads!