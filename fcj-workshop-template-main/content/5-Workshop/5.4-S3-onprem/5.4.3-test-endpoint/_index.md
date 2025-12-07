---
title : "Testing Interface Endpoint Connectivity from On-Premises"
date : 2025-12-06
weight : 3
chapter : false
pre : " <b> 5.4.3 </b> "
---

{{% notice warning %}}
⚠️ **Important:** The information provided here is for reference purposes only. Please **do not copy verbatim** for your report including this warning message.
{{% /notice %}}

In this section, you will validate that the Interface VPC Endpoint is working correctly by testing S3 connectivity from the simulated on-premises environment. This demonstrates how enterprise applications running in corporate datacenters can securely access AWS services through private connections.

**Testing Objectives:**

1. Retrieve the Interface Endpoint DNS name for S3 API calls
2. Connect to an EC2 instance simulating an on-premises server
3. Upload data to S3 using the private endpoint connection
4. Verify successful data transfer through the hybrid architecture

**Why This Test Matters:**

This validation confirms that your hybrid networking configuration is functioning correctly:
- DNS resolution from on-premises to endpoint private IPs
- VPN routing directing traffic to the cloud VPC
- Interface Endpoint ENIs receiving and processing S3 API requests
- Security groups allowing proper traffic flow
- End-to-end private connectivity without internet exposure

---

#### Part 1: Retrieve Interface Endpoint DNS Information

Before testing connectivity, you need to obtain the endpoint-specific DNS name that on-premises systems will use to reach S3.

**Step 1: Access VPC Endpoints Console**

1. Open the [Amazon VPC Console](https://us-east-1.console.aws.amazon.com/vpc/home?region=us-east-1#Endpoints:)
2. In the left navigation pane, click **Endpoints**
3. You should see your recently created Interface Endpoint

**Step 2: Locate Endpoint DNS Names**

1. **Find your endpoint**:
   - Look for the endpoint you created in the previous section
   - It should have a name like `S3-Interface-Endpoint-HybridAccess` or similar
   - Click on the **Endpoint ID** (format: `vpce-xxxxxxxxx`) to view details

2. **Access DNS configuration**:
   - In the endpoint details page, scroll to the **Details** tab
   - Look for the **DNS names** section

3. **Identify the Regional DNS Name**:
   - You'll see multiple DNS names listed
   - **Copy the first DNS name** (the regional endpoint DNS name)
   - Format: `vpce-xxxxxxxxx.s3.us-east-1.vpce.amazonaws.com`
   - Save this to a text editor - you'll need it shortly

![Endpoint DNS Names](/images/5-Workshop/5.4-S3-onprem/dns.png)

{{% notice info %}}
**DNS Name Types**: Interface Endpoints provide several DNS names:
- **Regional DNS name**: Works across all AZs (recommended for general use)
- **Zonal DNS names**: Specific to each AZ where you deployed ENIs
- **Private DNS name**: Only works if you enabled private DNS (which we didn't)

For this test, use the **regional DNS name** as it provides automatic failover between AZs.
{{% /notice %}}

**Understanding the DNS Name Structure:**

The regional DNS name follows this pattern:
```
vpce-<endpoint-id>.s3.<region>.vpce.amazonaws.com
```

This DNS name resolves to the private IP addresses of the Interface Endpoint ENIs deployed in your VPC subnets.

---

#### Part 2: Connect to Simulated On-Premises Server

Now you'll connect to an EC2 instance that simulates an on-premises server in your corporate datacenter.

**Step 1: Open AWS Systems Manager Session Manager**

1. **Navigate to Systems Manager**:
   - In the AWS Console search bar at the top, type `Session Manager`
   - Select **Session Manager** from the results (under Systems Manager)

2. **Access Session Manager**:
   - You'll be taken to the Session Manager start page
   - This service provides secure shell access without requiring SSH keys or bastion hosts

{{% notice tip %}}
**Session Manager Advantages**: Session Manager provides secure browser-based shell access to EC2 instances without:
- Opening inbound SSH ports (improved security)
- Managing SSH keys or credentials
- Deploying bastion hosts
- Configuring security group SSH rules

Perfect for enterprise environments with strict security requirements.
{{% /notice %}}

**Step 2: Start Interactive Session**

1. **Initiate connection**:
   - Click the **Start session** button
   - You'll see a list of available EC2 instances

2. **Select the on-premises instance**:
   - Look for an instance named **Test-Interface-Endpoint**
   - This EC2 instance runs in "VPC On-prem" subnet
   - It simulates a server in your corporate datacenter
   - Click the radio button next to this instance

3. **Start the session**:
   - Click **Start session** button at the bottom
   - Session Manager will open a new browser tab with a shell prompt
   - You should see: `sh-4.2$` or similar prompt

![Start Session Manager Session](/images/5-Workshop/5.4-S3-onprem/start-session.png)

{{% notice note %}}
**Instance Context**: The Test-Interface-Endpoint EC2 instance is configured to:
- Run in the "VPC On-prem" private subnet
- Route traffic through the VPN Gateway you configured
- Use the Route 53 Outbound Resolver for DNS queries
- Simulate an on-premises application server accessing AWS services
{{% /notice %}}

---

#### Part 3: Prepare Test Data for Upload

You'll now create a test file to upload to S3, demonstrating data transfer through the private endpoint.

**Step 1: Navigate to User Home Directory**

In the Session Manager terminal, execute:

```bash
cd ~
```

This changes to the `ssm-user` home directory where you have write permissions.

**Step 2: Create a Test File**

Generate a 1GB test file to simulate a realistic data transfer:

```bash
fallocate -l 1G onprem-data.dat
```

**Command Breakdown:**
- `fallocate`: Efficiently creates a file of specified size
- `-l 1G`: Allocates 1 gigabyte of space
- `onprem-data.dat`: Output filename

This creates a sparse file instantly without writing actual data (perfect for testing).

![Create Test File](/images/5-Workshop/5.4-S3-onprem/cli1.png)

**Step 3: Verify File Creation**

Confirm the file was created:

```bash
ls -lh onprem-data.dat
```

You should see output showing a 1.0G file.

{{% notice tip %}}
**File Size Selection**: Using a 1GB file provides:
- Measurable transfer time to observe network performance
- Sufficient size to verify throughput through VPN and endpoint
- Realistic representation of enterprise data transfers
- Quick enough for workshop purposes (full upload in seconds over AWS network)
{{% /notice %}}

---

#### Part 4: Upload Data Through Interface Endpoint

Now comes the critical test: uploading data to S3 using the Interface Endpoint you created.

**Step 1: Construct the Endpoint URL**

For Interface Endpoints, you must specify a custom endpoint URL. The format is:

```
https://bucket.<regional-dns-name>
```

**Build your URL:**
1. Start with: `https://bucket.`
2. Append the regional DNS name you copied earlier
3. Example result: `https://bucket.vpce-0a1b2c3d4e5f.s3.us-east-1.vpce.amazonaws.com`

{{% notice warning %}}
**DNS Name Format**: When copying the regional DNS name from the VPC console:
- Do NOT include the leading asterisk (`*.`) if shown
- Use only the portion: `vpce-xxxxxxx.s3.us-east-1.vpce.amazonaws.com`
- The `bucket.` prefix is added in the endpoint URL
{{% /notice %}}

**Step 2: Identify Your S3 Bucket**

You need the name of the S3 bucket created earlier in section 5.3 (Gateway Endpoint testing):
- Format: Something like `my-endpoint-test-bucket-123456`
- If you don't remember, check the S3 console in another browser tab

**Step 3: Execute S3 Upload Command**

In the Session Manager terminal, run:

```bash
aws s3 cp --endpoint-url https://bucket.<Regional-DNS-Name> onprem-data.dat s3://<your-bucket-name>
```

**Replace placeholders:**
- `<Regional-DNS-Name>`: Your actual endpoint DNS name (without asterisk)
- `<your-bucket-name>`: Your actual S3 bucket name

**Example with real values:**
```bash
aws s3 cp --endpoint-url https://bucket.vpce-0a1b2c3d4e.s3.us-east-1.vpce.amazonaws.com onprem-data.dat s3://my-endpoint-test-bucket-123456
```

![Upload File Through Endpoint](/images/5-Workshop/5.4-S3-onprem/cli2.png)

**Understanding the Command:**

- `aws s3 cp`: AWS CLI command to copy files to/from S3
- `--endpoint-url`: **Critical parameter** - directs the S3 API call to the Interface Endpoint instead of public S3 endpoint
- `onprem-data.dat`: Source file (local file on the instance)
- `s3://<bucket-name>`: Destination S3 bucket

{{% notice info %}}
**Why --endpoint-url is Required**: By default, AWS CLI uses the public S3 endpoint (`s3.amazonaws.com`). The `--endpoint-url` parameter overrides this to use your Interface Endpoint's private DNS name, ensuring traffic routes through the VPN and private endpoint rather than the internet.
{{% /notice %}}

**Step 4: Monitor Upload Progress**

You should see output similar to:

```
upload: ./onprem-data.dat to s3://my-bucket/onprem-data.dat
Completed 1.0 GiB/1.0 GiB (50.0 MiB/s)
```

The upload should complete within seconds given the high-speed AWS backbone network.

**What Just Happened:**

Behind the scenes, your data traveled through this path:
```
EC2 On-Prem Instance → Route Table Lookup → VPN Gateway EC2 
→ IPsec VPN Tunnel → Transit Gateway → VPC Cloud 
→ Interface Endpoint ENI (Private IP) → AWS PrivateLink → S3 Service
```

All traffic remained on the AWS private network - **no internet exposure!**

---

#### Part 5: Verify Successful Upload in S3 Console

Finally, confirm the data arrived successfully in your S3 bucket.

**Step 1: Navigate to S3 Console**

1. Open the [Amazon S3 Console](https://s3.console.aws.amazon.com/s3/home?region=us-east-1)
2. You'll see a list of all your S3 buckets

**Step 2: Access Your Bucket**

1. **Locate your bucket**:
   - Find the bucket you used in the upload command
   - Click on the **bucket name** to view its contents

2. **View objects**:
   - You should now see the file `onprem-data.dat` listed
   - Check the **Size** column - it should show 1.0 GB
   - Note the **Last modified** timestamp - should be recent

![Verify Object in S3 Bucket](/images/5-Workshop/5.4-S3-onprem/check-bucket.png)

**Step 3: Inspect Object Details (Optional)**

Click on the object name to view detailed properties:
- **Storage class**: Likely Standard
- **Server-side encryption**: May show default encryption
- **Metadata**: Any custom metadata added
- **Tags**: Any tags applied during upload

{{% notice success %}}
**Test Successful!** Your on-premises simulated environment successfully uploaded data to S3 through:
- Route 53 Outbound Resolver (DNS query from on-prem)
- VPN Tunnel (network connectivity)
- Route 53 Inbound Resolver (DNS resolution to private IP)
- Interface VPC Endpoint (private S3 access point)
- AWS PrivateLink (secure service connection)

All traffic remained private without traversing the public internet!
{{% /notice %}}

---

#### Understanding Your Test Results

**Connectivity Validation:**

This successful test confirms:

1. **DNS Resolution Working**:
   - On-prem instance queried Outbound Resolver
   - Query forwarded through VPN to Inbound Resolver
   - Endpoint DNS name resolved to Interface Endpoint private IP

2. **Network Routing Functional**:
   - VPN tunnel carrying traffic between on-prem and cloud VPCs
   - Route tables directing traffic correctly
   - Transit Gateway (if used) routing between VPCs

3. **Security Configuration Correct**:
   - Security groups allowing HTTPS traffic to endpoint ENIs
   - Endpoint policy permitting S3 operations
   - IAM permissions on EC2 instance allowing S3 access

4. **Interface Endpoint Operational**:
   - ENIs receiving requests on private IPs
   - PrivateLink routing requests to S3 service
   - Responses returning through private path

**Traffic Flow Diagram:**

```
┌─────────────────┐   DNS Query      ┌──────────────────┐
│  On-Prem EC2    │─────────────────>│ Outbound Resolver│
│  Instance       │                  │  (VPC On-prem)   │
└────────┬────────┘                  └────────┬─────────┘
         │                                    │
         │ Data Upload (HTTPS)                │ DNS Forward
         │                                    │
         ▼                                    ▼
┌─────────────────┐   VPN Tunnel    ┌──────────────────┐
│  VPN Gateway    │◄────────────────►│ Inbound Resolver │
│  EC2 Instance   │                  │  (VPC Cloud)     │
└────────┬────────┘                  └────────┬─────────┘
         │                                    │
         │                                    │ DNS Response
         ▼                                    │ (Private IP)
┌─────────────────┐                          │
│ Transit Gateway │                          │
└────────┬────────┘                          │
         │                                    │
         ▼                                    ▼
┌─────────────────┐                 ┌──────────────────┐
│  VPC Cloud      │                 │ Private Hosted   │
│  Route Table    │                 │ Zone (Route 53)  │
└────────┬────────┘                 └──────────────────┘
         │
         ▼
┌─────────────────────────────────┐
│  Interface Endpoint ENI         │
│  (Private IP: 10.0.x.x)         │
└────────┬────────────────────────┘
         │
         │ AWS PrivateLink
         ▼
┌─────────────────────────────────┐
│  Amazon S3 Service              │
│  (Backend in AWS Network)       │
└─────────────────────────────────┘
```

---

#### Cleanup for Next Section (Optional)

If you want to test again or prepare for the next workshop section:

**In Session Manager terminal:**

```bash
# Remove the local test file to free space
rm onprem-data.dat
```

**In S3 Console (optional):**
- You can leave the object in S3 or delete it
- The object will be cleaned up in the final workshop cleanup section

{{% notice note %}}
**Leave Infrastructure Running**: Do NOT delete the Interface Endpoint, VPN configuration, or Route 53 Resolvers yet. These will be used in subsequent workshop sections and cleaned up at the end.
{{% /notice %}}

---

#### Key Takeaways

**What You Accomplished:**

✅ **Retrieved Interface Endpoint DNS names** for API access configuration  
✅ **Connected to simulated on-premises server** using Session Manager  
✅ **Created test data** representing corporate files  
✅ **Uploaded data to S3** through private Interface Endpoint  
✅ **Verified successful transfer** in S3 console  
✅ **Validated end-to-end hybrid connectivity** across multiple AWS services  

**Enterprise Architecture Patterns Demonstrated:**

1. **Hybrid Cloud Connectivity**: VPN-based connection between on-prem and AWS
2. **Private Service Access**: Using VPC Endpoints to avoid internet routing
3. **DNS Resolution**: Hybrid DNS architecture with Route 53 Resolver
4. **Secure Remote Access**: Session Manager for bastion-less connectivity
5. **Defense in Depth**: Multiple layers (VPN, security groups, endpoint policies)

**Production Considerations:**

For real-world deployments, enhance this architecture with:
- **AWS Direct Connect** instead of VPN for dedicated network connection
- **Multiple VPN tunnels** for redundancy and higher availability
- **VPC Flow Logs** to monitor and audit traffic patterns
- **CloudWatch metrics** for endpoint performance monitoring
- **Restrictive endpoint policies** limiting access to specific buckets
- **Private DNS enablement** for automatic DNS resolution
- **Multi-region endpoints** for disaster recovery scenarios

This test validates that your hybrid architecture is production-ready for private S3 access from on-premises systems!




