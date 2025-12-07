---
title : "Creating Gateway Endpoint for S3"
date :  2025-12-06
weight : 1
chapter : false
pre : " <b> 5.3.1 </b> "
---

{{% notice warning %}}
⚠️ **Note:** The information below is for reference purposes only. Please **do not copy verbatim** for your report, including this warning.
{{% /notice %}}

In this section, you'll configure a Gateway VPC Endpoint to enable private connectivity between your VPC and Amazon S3. This eliminates the need for internet gateway, NAT devices, or VPN connections, while keeping traffic within the AWS network.

**What is a Gateway Endpoint?**
Gateway endpoints are route-based endpoints that appear as targets in your VPC route tables. When you create a gateway endpoint for S3 or DynamoDB, AWS automatically updates the specified route tables with routes directing traffic to the endpoint. This approach:
- Incurs no additional data transfer charges
- Doesn't require elastic network interfaces (ENIs)
- Scales automatically to handle your workload

**Benefits:**
- **Cost Efficiency**: No NAT Gateway charges for S3 access
- **Security**: Traffic never leaves the AWS network
- **Performance**: Lower latency compared to internet-based access
- **Simplified Architecture**: No need to manage NAT or internet gateway for S3 access

---

#### Step 1: Navigate to VPC Endpoints Console

1. Access the [Amazon VPC Console](https://us-east-1.console.aws.amazon.com/vpc/home?region=us-east-1#Home:) in the **us-east-1** region
2. From the left navigation menu, locate and click on **Endpoints**
3. Click the **Create endpoint** button in the upper right

{{% notice info %}}
**Expected Observation**: You should see **6 pre-existing VPC endpoints** in the list. These are **Interface Endpoints** for **AWS Systems Manager (SSM)** services that were automatically provisioned by the CloudFormation template during setup. These SSM endpoints enable secure, private connectivity to Systems Manager without requiring public internet access, allowing you to manage EC2 instances through Session Manager.
{{% /notice %}}

![Existing VPC Endpoints](/images/5-Workshop/5.3-S3-vpc/endpoints.png)

---

#### Step 2: Configure Endpoint Basic Settings

In the **Create endpoint** configuration page:

1. **Name tag**: Enter a descriptive name: `s3-gateway-endpoint`
   - This helps identify the endpoint's purpose in a multi-endpoint environment

2. **Service category**: Select **AWS services**
   - This option allows you to choose from AWS-managed service endpoints

![Endpoint Name and Service Category](/images/5-Workshop/5.3-S3-vpc/create-s3-gwe1.png)

---

#### Step 3: Select the S3 Gateway Service

1. In the **Services** section, use the search functionality:
   - Type `s3` in the filter box
   - The list will display S3-related endpoint options

2. **Important**: Select the service entry where:
   - **Service Name** contains `com.amazonaws.us-east-1.s3`
   - **Type** column shows **Gateway**
   
   Do NOT select the Interface type endpoint for S3.

![S3 Gateway Service Selection](/images/5-Workshop/5.3-S3-vpc/services.png)

**Understanding the Difference:**
- **Gateway endpoint** (what we're creating): Uses route table entries, free data transfer
- **Interface endpoint**: Uses ENIs, incurs PrivateLink charges

---

#### Step 4: Associate VPC and Route Tables

1. **VPC Selection**:
   - From the VPC dropdown menu, select **VPC Cloud**
   - This is the VPC where your cloud resources reside

2. **Route Tables Configuration**:
   - In the **Route tables** section, you'll see all route tables associated with the selected VPC
   - **Select the route table** that shows association with **2 subnets**
   
   {{% notice tip %}}
**Important**: This is NOT the main route table of the VPC. It's a custom route table created by CloudFormation specifically for the private subnets where your EC2 instances are running. The gateway endpoint will automatically add a route to this table directing S3 traffic (`pl-63a5400a` prefix list) to the endpoint.
{{% /notice %}}

![VPC and Route Table Selection](/images/5-Workshop/5.3-S3-vpc/vpc.png)

**What Happens Behind the Scenes:**
When you select the route table, AWS will automatically add an entry like:
```
Destination: pl-63a5400a (S3 prefix list)
Target: vpce-xxxxx (your gateway endpoint ID)
```

---

#### Step 5: Configure Endpoint Policy

1. **Policy Type**: Leave the default **Full access** option selected
   
   This grants unrestricted access to all S3 operations and buckets through this endpoint.

![Endpoint Policy Configuration](/images/5-Workshop/5.3-S3-vpc/policy.png)

{{% notice info %}}
**Note**: In a later section of this workshop, you will learn how to implement **restrictive VPC endpoint policies** to limit access to specific S3 buckets or operations. This demonstrates the principle of least privilege and shows how endpoint policies provide an additional security layer beyond IAM and bucket policies.
{{% /notice %}}

**Policy Options Explained:**
- **Full access**: Allows all S3 actions on all resources
- **Custom**: Lets you define specific allowed/denied actions and resources using JSON policy

---

#### Step 6: Review and Create Endpoint

1. **Tags (Optional)**: Skip adding tags for this workshop
   - In production, consider adding tags like `Environment: Workshop`, `Owner: YourName`

2. Click **Create endpoint** to provision the gateway endpoint

3. **Confirmation**: You should see a success message indicating the endpoint was created

4. Click the **X** or **Close** button to return to the endpoints list

![Endpoint Creation Success](/images/5-Workshop/5.3-S3-vpc/complete.png)

**Verification:**
- The new endpoint appears in your endpoints list with status **available**
- Check the associated route table to see the new S3 prefix list route

---

#### Understanding What Was Created

Your gateway endpoint is now active and has automatically:
1. **Updated the route table** with an entry pointing S3 traffic to the endpoint
2. **Created a managed prefix list** containing all S3 IP ranges for us-east-1
3. **Enabled private S3 access** for resources in the associated subnets

**Next Steps:**
In the following section, you'll test this endpoint by accessing S3 from an EC2 instance in your VPC and verify that traffic flows through the private endpoint instead of the internet.