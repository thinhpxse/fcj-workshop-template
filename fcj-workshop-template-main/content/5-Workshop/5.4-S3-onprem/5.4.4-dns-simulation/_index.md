---
title : "Configuring Hybrid DNS for Private Endpoint Resolution"
date : 2025-12-06
weight : 4
chapter : false
pre : " <b> 5.4.4 </b> "
---

{{% notice warning %}}
⚠️ **Important:** The information provided here is for reference purposes only. Please **do not copy verbatim** for your report including this warning message.
{{% /notice %}}

In this section, you will configure the DNS infrastructure to enable seamless name resolution for S3 API calls from your on-premises environment. This simulates how enterprise networks use conditional DNS forwarding to resolve AWS service endpoints to private IP addresses.

**Why DNS Configuration Is Critical:**

AWS PrivateLink endpoints use elastic network interfaces (ENIs) with fixed private IP addresses in each Availability Zone. While these IPs remain stable for the endpoint's lifecycle, AWS strongly recommends using DNS resolution rather than hardcoding IPs because:

- **Dynamic AZ Management**: ENIs may be added to new AZs or removed over time
- **Automatic Failover**: DNS allows automatic switching between AZ-specific IPs during outages
- **Service Updates**: AWS may update endpoint infrastructure requiring new IPs
- **Best Practice**: DNS provides abstraction and flexibility for enterprise architectures

**What You'll Configure:**

1. **DNS Alias Records**: Map S3 service domains to Interface Endpoint IPs in Route 53 Private Hosted Zone
2. **Resolver Forwarding Rule**: Direct on-premises DNS queries for S3 to the cloud VPC
3. **End-to-End Testing**: Validate DNS resolution and S3 access using the simulated on-premises environment

This configuration mimics real-world hybrid DNS architectures where on-premises DNS servers conditionally forward specific domains to AWS for private resolution.

---

#### Part 1: Create DNS Alias Records for Interface Endpoint

You'll now create DNS records in the Route 53 Private Hosted Zone that map S3 service names to your Interface Endpoint's private IP addresses.

**Understanding the Private Hosted Zone:**

The CloudFormation stack you deployed earlier created a Private Hosted Zone for `s3.us-east-1.amazonaws.com`. This zone:
- Is associated only with your VPCs (not public)
- Overrides public DNS resolution for S3 in your VPCs
- Allows you to return private IPs instead of public IPs for S3 domains
- Integrates with Route 53 Resolver for hybrid DNS

**Step 1: Access Route 53 Hosted Zones**

1. Open the [Route 53 Management Console](https://us-east-1.console.aws.amazon.com/route53/v2/hostedzones?region=us-east-1#) (Hosted Zones section)
2. You should see a Private Hosted Zone named `s3.us-east-1.amazonaws.com`
3. Click on the **hosted zone name** to view its DNS records

![Route 53 Private Hosted Zone](/images/5-Workshop/5.4-S3-onprem/hosted-zone.png)

{{% notice info %}}
**Private Hosted Zone Indicator**: You'll see "Private" listed under the Type column, and the Associated VPCs will show which VPCs use this zone for DNS resolution. This ensures only resources in your VPCs receive the private endpoint IPs.
{{% /notice %}}

**Step 2: Create Primary Alias Record**

You'll create an Alias record that points the base S3 domain to your Interface Endpoint.

1. **Initiate record creation**:
   - Click the **Create record** button at the top

2. **Configure the first record**:
   
   - **Record name**: Leave blank (this creates a record for the zone apex: `s3.us-east-1.amazonaws.com`)
   - **Record type**: Select **A – IPv4 address** (default)
   
   - **Alias configuration**:
     - Toggle the **Alias** switch to **ON** (enabled)
     - This tells Route 53 to alias to another AWS resource
   
   - **Route traffic to**:
     - Choose **Alias to VPC endpoint** from the dropdown
   
   - **Region**: 
     - Select **US East (N. Virginia) [us-east-1]**
   
   - **Choose endpoint**:
     - Paste the **Regional VPC Endpoint DNS name** from your text editor
     - This is the DNS name you saved in section 5.4.3
     - Format: `vpce-xxxxxxxxx.s3.us-east-1.vpce.amazonaws.com`

![Create Primary DNS Record](/images/5-Workshop/5.4-S3-onprem/create-record1.png)

![Primary Record Configuration](/images/5-Workshop/5.4-S3-onprem/record1.png)

{{% notice tip %}}
**Alias vs CNAME**: Using Alias records (instead of CNAME) provides benefits:
- Works at the zone apex (bare domain without subdomain)
- No charge for Alias queries to AWS resources
- Better performance with AWS service integration
- Automatic health checking and failover support
{{% /notice %}}

**Step 3: Create Wildcard Alias Record**

Now create a second record to handle all subdomains of the S3 service (e.g., `bucket.s3.us-east-1.amazonaws.com`).

1. **Add another record**:
   - Click **Add another record** button (do not click Create yet)

2. **Configure the wildcard record**:
   
   - **Record name**: Enter `*` (asterisk - this creates a wildcard)
   - **Record type**: Keep **A – IPv4 address**
   
   - **Alias configuration**:
     - Toggle **Alias** to **ON**
   
   - **Route traffic to**:
     - Choose **Alias to VPC endpoint**
   
   - **Region**: 
     - Select **US East (N. Virginia) [us-east-1]**
   
   - **Choose endpoint**:
     - Paste the same **Regional VPC Endpoint DNS name**

3. **Create both records**:
   - Click **Create records** button
   - Both records will be created simultaneously

![Wildcard DNS Record Configuration](/images/5-Workshop/5.4-S3-onprem/record2.png)

**Why Two Records?**

The two DNS records serve different purposes:

1. **Apex Record** (`s3.us-east-1.amazonaws.com`):
   - Handles queries to the base S3 regional endpoint
   - Used by commands like: `aws s3 ls --endpoint-url https://s3.us-east-1.amazonaws.com`

2. **Wildcard Record** (`*.s3.us-east-1.amazonaws.com`):
   - Handles queries with bucket names in the subdomain
   - Matches: `bucket.s3.us-east-1.amazonaws.com`, `my-bucket.s3.us-east-1.amazonaws.com`, etc.
   - Used by path-style S3 URLs

**Step 4: Verify Record Creation**

After creation, you should see both records in the hosted zone:

![DNS Records Created Successfully](/images/5-Workshop/5.4-S3-onprem/result.png)

Verify:
- Two new A records (apex and wildcard)
- Both show Type: **A - Alias**
- Value points to your VPC endpoint

{{% notice success %}}
**DNS Records Configured!** These records ensure that when systems query for S3 domain names, they receive the private IP addresses of your Interface Endpoint ENIs instead of public S3 IPs.
{{% /notice %}}

---

#### Part 2: Create Route 53 Resolver Forwarding Rule

Now you'll configure conditional DNS forwarding from the on-premises VPC to the cloud VPC. This simulates how on-premises DNS servers forward specific domains to AWS for resolution.

**Understanding Resolver Forwarding Rules:**

Route 53 Resolver Forwarding Rules enable:
- **Conditional forwarding**: Only specific domains are forwarded (e.g., `s3.us-east-1.amazonaws.com`)
- **Hybrid DNS**: On-premises systems query their local DNS, which forwards to AWS
- **Centralized management**: Rules define which domains resolve through AWS
- **Bidirectional resolution**: Can forward both from AWS to on-prem and vice versa

In this workshop:
- On-prem EC2 queries the **Outbound Resolver** in VPC On-prem
- Outbound Resolver forwards S3 queries through VPN to **Inbound Resolver** in VPC Cloud
- Inbound Resolver queries the Private Hosted Zone
- Response with private IPs travels back to on-prem

**Step 1: Retrieve Inbound Resolver IP Addresses**

First, you need the IP addresses where the Inbound Resolver is listening in VPC Cloud.

1. **Navigate to Inbound Endpoints**:
   - From Route 53 console, click **Resolver** in left sidebar
   - Click **Inbound endpoints**

2. **Access endpoint details**:
   - Click on the **Endpoint ID** of the inbound endpoint
   - CloudFormation created this with a name like `VPCCloudInboundEndpoint`

![Route 53 Inbound Endpoint](/images/5-Workshop/5.4-S3-onprem/route53-1.png)

3. **Copy IP addresses**:
   - In the endpoint details, scroll to the **IP addresses** section
   - You'll see **2 IP addresses** (one per AZ)
   - **Copy both IPs** to your text editor
   - Format: `10.0.x.x` (within VPC Cloud CIDR)

![Inbound Resolver IP Addresses](/images/5-Workshop/5.4-S3-onprem/route53-2.png)

{{% notice note %}}
**Multi-AZ Resolver**: The Inbound Resolver has IPs in multiple AZs for high availability. The forwarding rule will use both, automatically failing over if one AZ becomes unavailable.
{{% /notice %}}

**Step 2: Create Forwarding Rule**

1. **Navigate to Rules**:
   - In Route 53 console, click **Resolver** > **Rules** in left sidebar
   - Click **Create rule** button

![Create Resolver Rule](/images/5-Workshop/5.4-S3-onprem/route53-3.png)

2. **Configure rule basics**:
   
   - **Name**: Enter `S3-PrivateEndpoint-ForwardingRule`
   - **Description** (optional): "Forward S3 DNS queries to VPC Cloud for private endpoint resolution"
   - **Rule type**: Select **Forward** (not System)
   - **Domain name**: Enter `s3.us-east-1.amazonaws.com`

This domain name tells the rule: "Forward any DNS queries for this domain and its subdomains."

![Forwarding Rule Basic Configuration](/images/5-Workshop/5.4-S3-onprem/route53-4.png)

**Step 3: Associate VPC and Outbound Endpoint**

3. **VPC Configuration**:
   
   - **VPCs to associate this rule with**:
     - Select **VPC On-prem** from the dropdown
     - This applies the rule to DNS queries originating in the on-premises VPC
   
   - **Outbound endpoint**:
     - Select **VPCOnpremOutboundEndpoint**
     - This is the resolver endpoint that forwards queries from VPC On-prem

![VPC and Outbound Endpoint Association](/images/5-Workshop/5.4-S3-onprem/route53-5.png)

{{% notice info %}}
**Outbound Endpoint Role**: The Outbound Resolver in VPC On-prem receives DNS queries from EC2 instances and forwards them through the VPN tunnel to the target IP addresses (the Inbound Resolver in VPC Cloud).
{{% /notice %}}

**Step 4: Specify Target IP Addresses**

4. **Add target IPs**:
   
   - **Target IP addresses**: This is where forwarded queries will be sent
   - Click **Add target IP address**
   - **IP address**: Paste the **first Inbound Resolver IP** from your text editor
   - Click **Add target IP address** again
   - **IP address**: Paste the **second Inbound Resolver IP**
   - Both IPs should now be listed

5. **Create the rule**:
   - Click **Submit** button

![Target IP Configuration](/images/5-Workshop/5.4-S3-onprem/route53-6.png)

**Understanding the Target IPs:**

These are the Inbound Resolver IPs in VPC Cloud:
- They listen for DNS queries coming through the VPN
- They query the Private Hosted Zone for answers
- They return results back through the VPN to the Outbound Resolver

**Step 5: Verify Rule Creation**

After creation, you should see your new forwarding rule:

![Forwarding Rule Created Successfully](/images/5-Workshop/5.4-S3-onprem/route53-7.png)

Check that:
- **Status** shows **Active**
- **Type** shows **Forward**
- **Domain name** is `s3.us-east-1.amazonaws.com`
- **VPC** shows VPC On-prem

{{% notice success %}}
**DNS Forwarding Configured!** Queries from VPC On-prem for `s3.us-east-1.amazonaws.com` now forward through the Outbound Resolver, across the VPN tunnel, to the Inbound Resolver, and finally resolve through the Private Hosted Zone.
{{% /notice %}}

---

#### Part 3: Test On-Premises DNS Resolution

Now validate that the entire DNS resolution chain is working correctly.

**Step 1: Connect to On-Premises Instance**

1. Open **AWS Systems Manager** > **Session Manager**
2. Click **Start session**
3. Select the **Test-Interface-Endpoint** EC2 instance (in VPC On-prem)
4. Click **Start session**

![Connect to Test Instance](/images/5-Workshop/5.4-S3-onprem/test1.png)

**Step 2: Test DNS Resolution with Dig Command**

In the Session Manager terminal, run:

```bash
dig +short s3.us-east-1.amazonaws.com
```

**Expected Output:**

You should see **2 IP addresses** returned:
```
10.0.1.100
10.0.2.100
```

(Your actual IPs will differ but should be in the VPC Cloud CIDR range)

![DNS Resolution Test with Dig](/images/5-Workshop/5.4-S3-onprem/dig.png)

{{% notice note %}}
**Important Distinction**: The IP addresses returned are the **VPC Interface Endpoint ENI IPs**, NOT the Inbound Resolver IPs you configured. Both sets of IPs are in the VPC Cloud CIDR block, but they serve different purposes:
- **Inbound Resolver IPs**: Handle DNS queries (not returned in dig results)
- **Interface Endpoint IPs**: Where S3 API traffic is sent (returned by DNS queries)
{{% /notice %}}

**What Just Happened:**

The `dig` command triggered this DNS resolution flow:
```
EC2 On-prem → Outbound Resolver (VPC On-prem) 
→ VPN Tunnel → Inbound Resolver (VPC Cloud) 
→ Private Hosted Zone → Alias Record 
→ Interface Endpoint IPs → Response back to EC2
```

**Step 3: Verify IPs Match Interface Endpoint**

Cross-check the IPs returned by dig against the actual Interface Endpoint:

1. **Open VPC Console**:
   - Navigate to **VPC** > **Endpoints**
   - Click on your **S3 Interface Endpoint**

2. **Check Subnets tab**:
   - Click the **Subnets** tab
   - You'll see ENI IDs and their associated private IPs
   - **Verify** these IPs match those returned by the dig command

![Verify Endpoint IPs in VPC Console](/images/5-Workshop/5.4-S3-onprem/subnet.png)

The IPs should match perfectly, confirming DNS resolution is working correctly.

{{% notice success %}}
**DNS Resolution Validated!** Your on-premises environment is successfully resolving S3 domain names to private Interface Endpoint IPs through the hybrid DNS architecture.
{{% /notice %}}

**Step 4: Test S3 API Access via DNS**

Now test that applications can use the standard S3 endpoint URL and automatically connect through the private endpoint.

In the Session Manager terminal, run:

```bash
aws s3 ls --endpoint-url https://s3.us-east-1.amazonaws.com
```

**Expected Output:**

You should see a list of your S3 buckets, including the test bucket created earlier.

![S3 Access via Standard Endpoint URL](/images/5-Workshop/5.4-S3-onprem/endpoint.png)

**Key Observation:**

Notice you're now using the **standard S3 endpoint URL** (`https://s3.us-east-1.amazonaws.com`), not the VPC endpoint-specific URL you used in section 5.4.3. This is possible because:

1. DNS resolves `s3.us-east-1.amazonaws.com` to Interface Endpoint private IPs
2. HTTPS traffic routes through VPN to the Interface Endpoint
3. Applications don't need to be aware of the private endpoint configuration
4. Standard AWS SDKs and CLI work without modification

This demonstrates the power of DNS-based private endpoint resolution!

{{% notice tip %}}
**Production Benefit**: In enterprise environments, applications can use standard AWS service endpoints in their code. The DNS infrastructure automatically directs traffic through private endpoints when running on-premises or in VPCs, and through internet when running elsewhere (with appropriate routing).
{{% /notice %}}

**Step 5: Clean Up Session**

1. Terminate your Session Manager session by typing:
   ```bash
   exit
   ```

2. Or close the browser tab

![Terminate Session](/images/5-Workshop/5.4-S3-onprem/terminal.png)

---

#### Understanding Your Hybrid DNS Architecture

**Complete Traffic Flow:**

When an on-premises application accesses S3, this is the complete flow:

**DNS Resolution Path:**
```
┌──────────────────────┐
│ On-Prem Application  │
│ Queries: s3.us-...   │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ EC2 Instance         │
│ DNS Resolver Config  │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Outbound Resolver    │
│ (VPC On-prem)        │
│ Forwarding Rule:     │
│ s3.us-east-1.*       │
└──────────┬───────────┘
           │
           │ VPN Tunnel
           ▼
┌──────────────────────┐
│ Inbound Resolver     │
│ (VPC Cloud)          │
│ IPs: 10.0.x.x        │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Private Hosted Zone  │
│ s3.us-east-1.*.com   │
│ Alias Records        │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Returns: 10.0.1.100  │
│          10.0.2.100  │
│ (Endpoint ENI IPs)   │
└──────────────────────┘
```

**Data Path (After DNS Resolution):**
```
On-Prem Application → Interface Endpoint Private IP 
→ VPN Tunnel → VPC Cloud → Interface Endpoint ENI 
→ AWS PrivateLink → S3 Service
```

---

#### Key Accomplishments

**What You've Built:**

✅ **DNS Alias Records**: Mapped S3 domains to Interface Endpoint private IPs  
✅ **Conditional Forwarding**: Configured forwarding rule for S3 domain from on-prem to cloud  
✅ **Hybrid DNS Resolution**: Enabled seamless DNS resolution across VPN  
✅ **Transparent S3 Access**: Applications use standard S3 endpoints with private routing  
✅ **High Availability**: Multi-AZ DNS and endpoint configuration for fault tolerance  

**Enterprise Architecture Patterns:**

1. **Conditional DNS Forwarding**: Split-brain DNS with domain-specific forwarding
2. **Private Hosted Zones**: Override public DNS for AWS services with private IPs
3. **Alias Records**: Efficient DNS routing to AWS resources
4. **Resolver Endpoints**: Hybrid DNS bridge between on-prem and cloud
5. **Multi-AZ Resilience**: Automatic failover for DNS and endpoints

**Production Enhancements:**

For real-world deployments, consider:
- **DNS caching strategies** to reduce resolver query load
- **Monitoring CloudWatch metrics** for Resolver query counts and latency
- **Multiple forwarding rules** for different AWS services (EC2, RDS, etc.)
- **DNS failback to public resolution** if private endpoints are unavailable
- **Integration with on-premises DNS servers** (BIND, Active Directory, etc.)
- **DNS query logging** for security auditing and troubleshooting
- **Route 53 Resolver Query Logging** enabled for compliance

This hybrid DNS architecture provides enterprise-grade private connectivity to AWS services while maintaining standard application code and AWS SDK usage!