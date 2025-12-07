---
title : "Testing Gateway Endpoint Connectivity"
date :  2025-12-06
weight : 2
chapter : false
pre : " <b> 5.3.2 </b> "
---

{{% notice warning %}}
⚠️ **Note:** The information below is for reference purposes only. Please **do not copy verbatim** for your report, including this warning.
{{% /notice %}}

Now that you've created the Gateway Endpoint, it's time to verify that it works correctly. In this section, you'll create an S3 bucket, connect to an EC2 instance in your VPC, and upload a test file to S3 - all without traffic leaving the AWS network.

**Testing Objectives:**
- Verify private connectivity to S3 through the Gateway Endpoint
- Confirm that traffic does not traverse the public internet
- Demonstrate successful S3 operations from a private subnet
- Validate route table configuration

---

#### Step 1: Create an S3 Bucket for Testing

First, you need an S3 bucket to test uploads through the Gateway Endpoint.

1. **Navigate to S3 Console:**
   - Open the [S3 Management Console](https://s3.console.aws.amazon.com/s3/buckets)
   - Click the **Create bucket** button

![S3 Create Bucket](/images/5-Workshop/5.3-S3-vpc/create-bucket.png)

2. **Configure Bucket Settings:**

   **Bucket Name:**
   - Enter a globally unique name (S3 bucket names must be unique across all AWS accounts)
   - Suggested format: `gateway-endpoint-test-<your-initials>-<random-number>`
   - Example: `gateway-endpoint-test-jd-20241207`

![Bucket Naming](/images/5-Workshop/5.3-S3-vpc/bucket-name.png)

   **AWS Region:**
   - Ensure the region is **US East (N. Virginia) us-east-1**
   - This must match the region where your VPC and Gateway Endpoint are deployed

   **Object Ownership:**
   - Leave as default (**ACLs disabled** - Recommended)

   **Block Public Access settings:**
   - Leave all options checked (default)
   - This ensures the bucket remains private

   **Bucket Versioning:**
   - Leave as default (Disabled)

   **Default Encryption:**
   - Leave as default (Server-side encryption with Amazon S3 managed keys - SSE-S3)

3. **Create the Bucket:**
   - Scroll to the bottom
   - Click **Create bucket**

![Create Button](/images/5-Workshop/5.3-S3-vpc/create-button.png)

4. **Verify Creation:**
   - You should see a success message
   - Your new bucket appears in the buckets list

![Bucket Created Successfully](/images/5-Workshop/5.3-S3-vpc/bucket-success.png)

{{% notice tip %}}
**Security Best Practice**: Notice that the bucket is created with all public access blocked by default. This aligns with AWS security best practices. Access will only be possible through the VPC endpoint and with proper IAM permissions.
{{% /notice %}}

---

---

#### Step 2: Connect to EC2 Instance Using Session Manager

You'll use **AWS Systems Manager Session Manager** to securely connect to the EC2 instance without requiring SSH keys, bastion hosts, or open inbound ports.

**What is AWS Session Manager?**
Session Manager is a fully managed Systems Manager capability that provides:
- **Secure Access**: Browser-based shell access without opening inbound ports
- **Audit Trail**: All sessions are logged in CloudWatch Logs
- **No SSH Keys**: No need to manage or distribute SSH keys
- **Cross-Platform**: Works with Linux and Windows instances

**Architecture Context:**
The EC2 instance you'll connect to is running in a **private subnet** within "VPC Cloud". It has no direct internet access - connectivity to AWS services (including Systems Manager) is provided through the SSM VPC Interface Endpoints that were created during CloudFormation deployment.

1. **Open Systems Manager Console:**
   - In the AWS Management Console search bar, type `Systems Manager`
   - Press **Enter** or click on the **Systems Manager** service

![Systems Manager Search](/images/5-Workshop/5.3-S3-vpc/sm.png)

2. **Navigate to Session Manager:**
   - In the left navigation pane, locate **Node Management** section
   - Click on **Session Manager**

![Session Manager Navigation](/images/5-Workshop/5.3-S3-vpc/sm1.png)

3. **Start a New Session:**
   - Click the **Start session** button
   - From the list of available instances, select the instance named **Test-Gateway-Endpoint**

{{% notice info %}}
**Instance Details**: This EC2 instance is deployed in a private subnet of "VPC Cloud" and is configured with an IAM instance profile that grants necessary permissions for Session Manager and S3 access. The instance will be used to verify that S3 traffic flows through the Gateway Endpoint you created, rather than the public internet.
{{% /notice %}}

![Start Session](/images/5-Workshop/5.3-S3-vpc/start-session.png)

4. **Session Established:**
   - Session Manager opens a new browser tab with a shell prompt
   - You should see a prompt like: `sh-4.2$` or similar
   - This indicates you're successfully connected to the EC2 instance

![Session Connected](/images/5-Workshop/5.3-S3-vpc/start-session-success.png)

{{% notice tip %}}
**Connectivity Verification**: The fact that Session Manager works confirms that the SSM Interface Endpoints are functioning correctly. If you couldn't connect, it would indicate an issue with the Interface Endpoints or IAM permissions.
{{% /notice %}}

---

#### Step 3: Create and Upload Test File to S3

Now you'll create a test file on the EC2 instance and upload it to S3 through the Gateway Endpoint.

1. **Navigate to Home Directory:**
   
   Execute the following command to switch to the ssm-user's home directory:
   ```bash
   cd ~
   ```

![Change Directory](/images/5-Workshop/5.3-S3-vpc/cli1.png)

   **Why this step?** Working in the home directory ensures you have proper write permissions and a clean working environment.

2. **Create a Test File:**
   
   Generate a 1GB test file using the following command:
   ```bash
   fallocate -l 1G testfile.xyz
   ```

![Create Test File](/images/5-Workshop/5.3-S3-vpc/cli-file.png)

   **Command Explanation:**
   - `fallocate`: Efficiently allocates space for a file
   - `-l 1G`: Specifies file size of 1 gigabyte
   - `testfile.xyz`: The filename for our test file
   
   This creates a 1GB file almost instantly by allocating disk space without writing actual data. We use a large file to make the upload more observable and to demonstrate that the Gateway Endpoint can handle substantial data transfers.

3. **Upload File to S3 Bucket:**
   
   Use the AWS CLI to upload the file to your S3 bucket:
   ```bash
   aws s3 cp testfile.xyz s3://YOUR-BUCKET-NAME/
   ```
   
   **Important**: Replace `YOUR-BUCKET-NAME` with the actual name of the bucket you created in Step 1.

![File Upload](/images/5-Workshop/5.3-S3-vpc/uploaded.png)

   **What's Happening Behind the Scenes:**
   - The EC2 instance makes an API call to S3
   - The VPC route table directs S3 traffic to the Gateway Endpoint (prefix list destination)
   - Traffic flows through the Gateway Endpoint to S3, staying within the AWS network
   - **No internet gateway or NAT gateway is used**

   {{% notice success %}}
**Success Indicator**: If the upload completes successfully, you'll see output showing the file transfer progress and completion. This confirms that:
- The Gateway Endpoint is configured correctly
- Route table entries are working as expected
- The EC2 instance has proper IAM permissions for S3
- Private connectivity to S3 is functioning
{{% /notice %}}

4. **Terminate the Session:**
   - Type `exit` or close the browser tab
   - The session will be cleanly terminated

---

#### Step 4: Verify Object in S3 Bucket

Finally, confirm that the file was successfully uploaded by checking the S3 console.

1. **Return to S3 Console:**
   - Navigate back to the [S3 Management Console](https://s3.console.aws.amazon.com/s3/buckets)

2. **Open Your Bucket:**
   - Click on the name of the bucket you created earlier

3. **Verify File Presence:**
   - You should see `testfile.xyz` listed in the Objects tab
   - The file size should show approximately 1 GB
   - Note the upload timestamp

![Verify S3 Object](/images/5-Workshop/5.3-S3-vpc/check-s3-bucket.png)

**Verification Checklist:**
- ✓ File `testfile.xyz` appears in bucket
- ✓ File size matches (1 GB)
- ✓ Upload timestamp is recent
- ✓ No errors in S3 console

---

#### Understanding What You Just Accomplished

**Network Path Verification:**
In this test, you successfully:

1. **Created Private Connectivity**: The Gateway Endpoint enabled private connectivity from your VPC to S3
2. **Bypassed Public Internet**: The upload did not traverse the public internet - it stayed entirely within AWS's network backbone
3. **Used Route-Based Routing**: The route table automatically directed S3 traffic to the Gateway Endpoint based on the S3 prefix list
4. **Demonstrated Cost Savings**: By using a Gateway Endpoint instead of a NAT Gateway, you avoided:
   - NAT Gateway hourly charges
   - NAT Gateway data processing charges
   - Potential internet gateway data transfer costs

**Traffic Flow Diagram:**
```
EC2 Instance → VPC Route Table → Gateway Endpoint → S3 Service
(Private Subnet)  (Routes S3 to vpce-xxx)  (No internet)  (AWS Network)
```

**Key Differences from Internet-Based Access:**
- **Without Gateway Endpoint**: EC2 → NAT Gateway → Internet Gateway → Public Internet → S3
- **With Gateway Endpoint**: EC2 → Gateway Endpoint → S3 (all within AWS network)

{{% notice tip %}}
**Performance Benefits**: Gateway Endpoints not only save costs but also typically provide better performance than internet-based access due to:
- Lower latency (direct AWS network path)
- Higher available bandwidth
- No NAT Gateway bottleneck
- Dedicated AWS backbone infrastructure
{{% /notice %}}

---

#### Section Summary

**Congratulations!** You've successfully completed the Gateway Endpoint testing exercise. 

**What You Learned:**
- ✓ How to create and configure S3 buckets with security best practices
- ✓ Using AWS Systems Manager Session Manager for secure instance access
- ✓ Uploading objects to S3 through a Gateway VPC Endpoint
- ✓ Verifying private connectivity without internet gateway dependency
- ✓ Understanding the traffic flow in a VPC with Gateway Endpoints

**Key Takeaways:**
1. **Gateway Endpoints provide cost-effective private connectivity** to S3 and DynamoDB
2. **No additional charges** for using Gateway Endpoints - you only pay for S3 storage and requests
3. **Traffic remains private** - never exposed to the public internet
4. **Automatic routing** through route table entries - no complex configuration needed
5. **Scalable and highly available** - AWS manages the endpoint infrastructure

**Next Steps:**
In the following sections, you'll explore:
- Creating Interface VPC Endpoints for other AWS services
- Implementing VPC Endpoint policies for fine-grained access control
- Testing connectivity from on-premises (simulated) environments
- Understanding the differences between Gateway and Interface Endpoints













