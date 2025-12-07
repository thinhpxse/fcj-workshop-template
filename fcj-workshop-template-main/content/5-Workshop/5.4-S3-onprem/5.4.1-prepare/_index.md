---
title : "Environment Preparation for Hybrid Connectivity"
date: 2025-12-06
weight : 1
chapter : false
pre : " <b> 5.4.1 </b> "
---

{{% notice warning %}}
⚠️ **Note:** The information below is for reference purposes only. Please **do not copy verbatim** for your report, including this warning.
{{% /notice %}}

In this section, you'll prepare the environment to simulate hybrid cloud connectivity between an on-premises datacenter and AWS. This setup demonstrates how organizations can securely access AWS services like S3 from their corporate networks through private connections.

**What You'll Configure:**
1. Deploy additional DNS infrastructure using CloudFormation
2. Configure VPN routing to enable on-premises to cloud connectivity

**Architecture Context:**
This configuration simulates a real-world hybrid architecture where:
- An on-premises datacenter connects to AWS via Site-to-Site VPN
- DNS queries from on-premises are resolved through Route 53
- S3 access from on-premises flows through Interface VPC Endpoints
- All traffic remains private without internet exposure

---

#### Part 1: Deploy DNS Infrastructure with CloudFormation

To enable proper DNS resolution in the hybrid environment, you'll deploy a CloudFormation stack that creates Route 53 resolver endpoints and private hosted zones.

**What This Stack Creates:**

The CloudFormation template provisions three critical DNS components:

1. **Route 53 Private Hosted Zone**
   - Hosts DNS records (Alias records) for the S3 Interface Endpoint
   - Allows on-premises systems to resolve S3 endpoint names to private IP addresses
   - Scoped to your VPC for private resolution

2. **Route 53 Inbound Resolver Endpoint**
   - Deployed in "VPC Cloud"
   - Receives DNS queries from on-premises environments
   - Forwards queries to the Private Hosted Zone for resolution
   - Creates elastic network interfaces (ENIs) in your VPC subnets

3. **Route 53 Outbound Resolver Endpoint**
   - Deployed in "VPC On-prem"
   - Forwards DNS queries for S3 domains to "VPC Cloud"
   - Enables conditional DNS forwarding based on domain names
   - Routes queries through the VPN tunnel to the Inbound Resolver

**DNS Flow Diagram:**

![Route 53 Resolver Architecture](/images/5-Workshop/5.4-S3-onprem/route53.png)

**Deployment Steps:**

1. **Launch CloudFormation Stack:**
   
   Click this link to open the CloudFormation console with pre-configured template: [Deploy DNS Infrastructure Stack](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/quickcreate?templateURL=https://s3.amazonaws.com/reinvent-endpoints-builders-session/R53CF.yaml&stackName=PLOnpremSetup)
   
   The template URL is pre-populated and the stack name is set to `PLOnpremSetup`.

![CloudFormation Quick Create](/images/5-Workshop/5.4-S3-onprem/create-stack.png)

2. **Review and Accept Defaults:**
   
   - All parameters are pre-configured with optimal values
   - The template will automatically detect your VPC IDs and subnet configurations
   - Scroll to the bottom of the page

3. **Acknowledge and Create:**
   
   - Check the acknowledgment box: "I acknowledge that AWS CloudFormation might create IAM resources."
   - Click **Create stack**

![Create Stack Button](/images/5-Workshop/5.4-S3-onprem/create-stack-button.png)

4. **Monitor Deployment (Optional):**
   
   - Stack creation typically takes 3-5 minutes
   - You can monitor progress in the **Events** tab
   - **You don't need to wait** - proceed to the next section while this deploys

{{% notice info %}}
**Background Processing**: The CloudFormation stack creates resolver endpoints which can take a few minutes. The next configuration step (routing table update) can be completed in parallel while CloudFormation runs.
{{% /notice %}}

**What Gets Created:**
- 2 Resolver Endpoint ENIs (Inbound in VPC Cloud)
- 2 Resolver Endpoint ENIs (Outbound in VPC On-prem)
- 1 Private Hosted Zone for S3 endpoint DNS resolution
- Resolver rules for conditional DNS forwarding

---

#### Part 2: Configure VPN Routing for On-Premises Connectivity

Now you'll configure the routing table in "VPC On-prem" to direct traffic destined for the cloud environment through the VPN tunnel.

**Understanding the VPN Setup:**

This workshop uses a software-based VPN solution running on an EC2 instance to simulate the connection between your simulated on-premises environment and AWS:

- **VPN Software**: strongSwan (open-source IPsec-based VPN)
- **VPN Gateway Instance**: An EC2 instance acting as the customer gateway
- **Connection Type**: Site-to-Site VPN tunnel
- **Purpose**: Simulates a real datacenter's VPN appliance

{{% notice tip %}}
**Production Note**: In real-world deployments, you would use:
- Physical or virtual VPN appliances in your datacenter
- AWS Site-to-Site VPN with redundant connections
- AWS Transit Gateway for centralized connectivity
- Multiple VPN tunnels for high availability
{{% /notice %}}

**Configuration Steps:**

**Step 1: Identify the VPN Gateway Instance**

1. Open the [Amazon EC2 Console](https://us-east-1.console.aws.amazon.com/ec2/home?region=us-east-1#Instances:)

2. Locate and select the EC2 instance with the name **infra-vpngw-test**
   - This instance is running strongSwan VPN software
   - It's configured to terminate the VPN tunnel from "VPC Cloud"

3. In the **Details** tab (bottom pane), locate the **Instance ID**
   - Format: `i-0123456789abcdef0`
   - **Copy this Instance ID** to your clipboard or text editor
   - You'll need this ID in the next steps

![VPN Gateway Instance ID](/images/5-Workshop/5.4-S3-onprem/ec2-onprem-id.png)

{{% notice note %}}
**Instance Role**: This EC2 instance has IP forwarding enabled and is configured with IPsec tunnels. It acts as the gateway between the simulated on-premises network and AWS Transit Gateway.
{{% /notice %}}

**Step 2: Navigate to VPC Route Tables**

1. Use the **search box** at the top of the AWS Console
2. Type `VPC` and press Enter
3. From the VPC Dashboard left menu, click **Route Tables**

**Step 3: Update On-Premises Route Table**

1. **Locate the correct route table:**
   - Filter or search for the route table named **RT Private On-prem**
   - This is the route table associated with the private subnets in "VPC On-prem"
   - Click on the route table to select it

2. **Access route editing:**
   - Click on the **Routes** tab in the bottom details pane
   - Click **Edit routes** button

![Route Table Selection](/images/5-Workshop/5.4-S3-onprem/rt.png)

3. **Add new route entry:**
   - Click **Add route** button
   
   Configure the new route:
   - **Destination**: Enter your Cloud VPC CIDR block
     - Format: `10.0.0.0/16` (or whatever CIDR your Cloud VPC uses)
     - This tells the route table where cloud resources are located
   
   - **Target**: Select **Instance**, then choose the **infra-vpngw-test** instance
     - Paste the Instance ID you copied earlier
     - Alternatively, start typing the instance name and select from the dropdown

![Add Route Configuration](/images/5-Workshop/5.4-S3-onprem/add-route.png)

4. **Save the configuration:**
   - Click **Save changes**
   - The route table is immediately updated

**Understanding What You Configured:**

This route entry tells instances in "VPC On-prem" that:
```
Traffic destined for Cloud VPC CIDR → Send to VPN Gateway Instance
```

**Traffic Flow:**
```
On-Prem EC2 Instance → Route Table Lookup → VPN Gateway EC2 
→ IPsec Tunnel → Transit Gateway → VPC Cloud
```

{{% notice success %}}
**Routing Configured!** Now any traffic from "VPC On-prem" destined for resources in "VPC Cloud" will be routed through the VPN tunnel. This simulates how your corporate network would route traffic to AWS over a Site-to-Site VPN connection.
{{% /notice %}}

---

#### Verification Steps

**Verify CloudFormation Stack:**
1. Return to [CloudFormation Console](https://us-east-1.console.aws.amazon.com/cloudformation/)
2. Check that **PLOnpremSetup** stack shows `CREATE_COMPLETE` status
3. Click on the stack and view the **Resources** tab to see created components

**Verify Route Table:**
1. Go back to the **RT Private On-prem** route table
2. Confirm the new route appears with:
   - Destination: Cloud VPC CIDR
   - Target: VPN Gateway instance ID
   - Status: Active

**What's Next:**
With DNS infrastructure and routing in place, you're ready to:
- Create an Interface VPC Endpoint for S3
- Test S3 access from the simulated on-premises environment
- Verify DNS resolution through Route 53 Resolver
- Confirm all traffic flows through private connections

---

#### Architecture Summary

You've now established the foundational hybrid connectivity:

**DNS Resolution Path:**
```
On-Prem Application → Outbound Resolver Endpoint → VPN Tunnel 
→ Inbound Resolver Endpoint → Private Hosted Zone → S3 Endpoint IP
```

**Data Path:**
```
On-Prem Application → VPN Gateway → IPsec Tunnel → Transit Gateway 
→ VPC Cloud → Interface Endpoint → S3
```

This architecture demonstrates AWS best practices for:
- Private connectivity to AWS services from on-premises
- DNS resolution in hybrid environments
- Secure data transfer without internet exposure
- Scalable VPN connectivity through Transit Gateway



