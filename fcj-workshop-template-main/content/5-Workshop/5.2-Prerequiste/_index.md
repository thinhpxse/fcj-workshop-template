---
title : "Prerequisites"
date :  2025-12-06
weight : 2 
chapter : false
pre : " <b> 5.2. </b> "
---

{{% notice warning %}}
⚠️ **Note:** The information below is for reference purposes only. Please **do not copy verbatim** for your report, including this warning.
{{% /notice %}}

#### Required IAM Permissions

Before starting this workshop, you must ensure your AWS IAM user or role has sufficient permissions to create and manage the necessary resources. Attach the following custom IAM policy to your user account or assume a role with these permissions.

**Important Security Notes:**
- Review all permissions before applying to understand what resources will be created
- These permissions are required for workshop deployment and cleanup operations
- After completing the workshop, consider removing these permissions if they're no longer needed
- For production environments, always follow the principle of least privilege
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "cloudformation:*",
                "cloudwatch:*",
                "ec2:AcceptTransitGatewayPeeringAttachment",
                "ec2:AcceptTransitGatewayVpcAttachment",
                "ec2:AllocateAddress",
                "ec2:AssociateAddress",
                "ec2:AssociateIamInstanceProfile",
                "ec2:AssociateRouteTable",
                "ec2:AssociateSubnetCidrBlock",
                "ec2:AssociateTransitGatewayRouteTable",
                "ec2:AssociateVpcCidrBlock",
                "ec2:AttachInternetGateway",
                "ec2:AttachNetworkInterface",
                "ec2:AttachVolume",
                "ec2:AttachVpnGateway",
                "ec2:AuthorizeSecurityGroupEgress",
                "ec2:AuthorizeSecurityGroupIngress",
                "ec2:CreateClientVpnEndpoint",
                "ec2:CreateClientVpnRoute",
                "ec2:CreateCustomerGateway",
                "ec2:CreateDhcpOptions",
                "ec2:CreateFlowLogs",
                "ec2:CreateInternetGateway",
                "ec2:CreateLaunchTemplate",
                "ec2:CreateNetworkAcl",
                "ec2:CreateNetworkInterface",
                "ec2:CreateNetworkInterfacePermission",
                "ec2:CreateRoute",
                "ec2:CreateRouteTable",
                "ec2:CreateSecurityGroup",
                "ec2:CreateSubnet",
                "ec2:CreateSubnetCidrReservation",
                "ec2:CreateTags",
                "ec2:CreateTransitGateway",
                "ec2:CreateTransitGatewayPeeringAttachment",
                "ec2:CreateTransitGatewayPrefixListReference",
                "ec2:CreateTransitGatewayRoute",
                "ec2:CreateTransitGatewayRouteTable",
                "ec2:CreateTransitGatewayVpcAttachment",
                "ec2:CreateVpc",
                "ec2:CreateVpcEndpoint",
                "ec2:CreateVpcEndpointConnectionNotification",
                "ec2:CreateVpcEndpointServiceConfiguration",
                "ec2:CreateVpnConnection",
                "ec2:CreateVpnConnectionRoute",
                "ec2:CreateVpnGateway",
                "ec2:DeleteCustomerGateway",
                "ec2:DeleteFlowLogs",
                "ec2:DeleteInternetGateway",
                "ec2:DeleteNetworkInterface",
                "ec2:DeleteNetworkInterfacePermission",
                "ec2:DeleteRoute",
                "ec2:DeleteRouteTable",
                "ec2:DeleteSecurityGroup",
                "ec2:DeleteSubnet",
                "ec2:DeleteSubnetCidrReservation",
                "ec2:DeleteTags",
                "ec2:DeleteTransitGateway",
                "ec2:DeleteTransitGatewayPeeringAttachment",
                "ec2:DeleteTransitGatewayPrefixListReference",
                "ec2:DeleteTransitGatewayRoute",
                "ec2:DeleteTransitGatewayRouteTable",
                "ec2:DeleteTransitGatewayVpcAttachment",
                "ec2:DeleteVpc",
                "ec2:DeleteVpcEndpoints",
                "ec2:DeleteVpcEndpointServiceConfigurations",
                "ec2:DeleteVpnConnection",
                "ec2:DeleteVpnConnectionRoute",
                "ec2:Describe*",
                "ec2:DetachInternetGateway",
                "ec2:DisassociateAddress",
                "ec2:DisassociateRouteTable",
                "ec2:GetLaunchTemplateData",
                "ec2:GetTransitGatewayAttachmentPropagations",
                "ec2:ModifyInstanceAttribute",
                "ec2:ModifySecurityGroupRules",
                "ec2:ModifyTransitGatewayVpcAttachment",
                "ec2:ModifyVpcAttribute",
                "ec2:ModifyVpcEndpoint",
                "ec2:ReleaseAddress",
                "ec2:ReplaceRoute",
                "ec2:RevokeSecurityGroupEgress",
                "ec2:RevokeSecurityGroupIngress",
                "ec2:RunInstances",
                "ec2:StartInstances",
                "ec2:StopInstances",
                "ec2:UpdateSecurityGroupRuleDescriptionsEgress",
                "ec2:UpdateSecurityGroupRuleDescriptionsIngress",
                "iam:AddRoleToInstanceProfile",
                "iam:AttachRolePolicy",
                "iam:CreateInstanceProfile",
                "iam:CreatePolicy",
                "iam:CreateRole",
                "iam:DeleteInstanceProfile",
                "iam:DeletePolicy",
                "iam:DeleteRole",
                "iam:DeleteRolePolicy",
                "iam:DetachRolePolicy",
                "iam:GetInstanceProfile",
                "iam:GetPolicy",
                "iam:GetRole",
                "iam:GetRolePolicy",
                "iam:ListPolicyVersions",
                "iam:ListRoles",
                "iam:PassRole",
                "iam:PutRolePolicy",
                "iam:RemoveRoleFromInstanceProfile",
                "lambda:CreateFunction",
                "lambda:DeleteFunction",
                "lambda:DeleteLayerVersion",
                "lambda:GetFunction",
                "lambda:GetLayerVersion",
                "lambda:InvokeFunction",
                "lambda:PublishLayerVersion",
                "logs:CreateLogGroup",
                "logs:DeleteLogGroup",
                "logs:DescribeLogGroups",
                "logs:PutRetentionPolicy",
                "route53:ChangeTagsForResource",
                "route53:CreateHealthCheck",
                "route53:CreateHostedZone",
                "route53:CreateTrafficPolicy",
                "route53:DeleteHostedZone",
                "route53:DisassociateVPCFromHostedZone",
                "route53:GetHostedZone",
                "route53:ListHostedZones",
                "route53domains:ListDomains",
                "route53domains:ListOperations",
                "route53domains:ListTagsForDomain",
                "route53resolver:AssociateResolverEndpointIpAddress",
                "route53resolver:AssociateResolverRule",
                "route53resolver:CreateResolverEndpoint",
                "route53resolver:CreateResolverRule",
                "route53resolver:DeleteResolverEndpoint",
                "route53resolver:DeleteResolverRule",
                "route53resolver:DisassociateResolverEndpointIpAddress",
                "route53resolver:DisassociateResolverRule",
                "route53resolver:GetResolverEndpoint",
                "route53resolver:GetResolverRule",
                "route53resolver:ListResolverEndpointIpAddresses",
                "route53resolver:ListResolverEndpoints",
                "route53resolver:ListResolverRuleAssociations",
                "route53resolver:ListResolverRules",
                "route53resolver:ListTagsForResource",
                "route53resolver:UpdateResolverEndpoint",
                "route53resolver:UpdateResolverRule",
                "s3:AbortMultipartUpload",
                "s3:CreateBucket",
                "s3:DeleteBucket",
                "s3:DeleteObject",
                "s3:GetAccountPublicAccessBlock",
                "s3:GetBucketAcl",
                "s3:GetBucketOwnershipControls",
                "s3:GetBucketPolicy",
                "s3:GetBucketPolicyStatus",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetObject",
                "s3:GetObjectVersion",
                "s3:GetBucketVersioning",
                "s3:ListAccessPoints",
                "s3:ListAccessPointsForObjectLambda",
                "s3:ListAllMyBuckets",
                "s3:ListBucket",
                "s3:ListBucketMultipartUploads",
                "s3:ListBucketVersions",
                "s3:ListJobs",
                "s3:ListMultipartUploadParts",
                "s3:ListMultiRegionAccessPoints",
                "s3:ListStorageLensConfigurations",
                "s3:PutAccountPublicAccessBlock",
                "s3:PutBucketAcl",
                "s3:PutBucketPolicy",
                "s3:PutBucketPublicAccessBlock",
                "s3:PutObject",
                "secretsmanager:CreateSecret",
                "secretsmanager:DeleteSecret",
                "secretsmanager:DescribeSecret",
                "secretsmanager:GetSecretValue",
                "secretsmanager:ListSecrets",
                "secretsmanager:ListSecretVersionIds",
                "secretsmanager:PutResourcePolicy",
                "secretsmanager:TagResource",
                "secretsmanager:UpdateSecret",
                "sns:ListTopics",
                "ssm:DescribeInstanceProperties",
                "ssm:DescribeSessions",
                "ssm:GetConnectionStatus",
                "ssm:GetParameters",
                "ssm:ListAssociations",
                "ssm:ResumeSession",
                "ssm:StartSession",
                "ssm:TerminateSession"
            ],
            "Resource": "*"
        }
    ]
}

```

**Policy Coverage:**
This IAM policy grants permissions across multiple AWS services essential for the workshop:
- **EC2 & VPC Networking**: Create and manage VPCs, subnets, route tables, security groups, Transit Gateway, VPN connections, and VPC endpoints
- **CloudFormation**: Deploy and manage infrastructure as code stacks
- **IAM**: Create roles and instance profiles for EC2 instances
- **S3**: Create buckets and manage objects for workshop resources
- **Lambda & CloudWatch**: Deploy serverless functions and monitor resources
- **Route53**: Configure DNS and resolver endpoints
- **SSM (Systems Manager)**: Enable secure instance access via Session Manager
- **Secrets Manager**: Store and retrieve sensitive configuration data

#### Environment Setup Using Infrastructure as Code

**Workshop Region:** This lab is designed for the **US East (N. Virginia)** region (`us-east-1`). Ensure you select this region before proceeding.

**Automated Deployment Overview:**
Instead of manually creating each resource, we'll use AWS CloudFormation to automate the entire infrastructure setup. This Infrastructure as Code (IaC) approach provides:
- **Consistency**: All participants work with identical configurations
- **Speed**: Deploy complex multi-resource environments in minutes
- **Repeatability**: Easy to recreate or clean up the environment
- **Best Practices**: Pre-configured with AWS recommended settings

**Deployment Steps:**

1. **Launch the CloudFormation Stack:**
   - Click this link to open the CloudFormation quick-create console: [Deploy Workshop Infrastructure](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/quickcreate?templateURL=https://s3.us-east-1.amazonaws.com/reinvent-endpoints-builders-session/Nested.yaml&stackName=PLCloudSetup)
   - The template URL is pre-populated with the workshop configuration
   - Stack name is automatically set to `PLCloudSetup`

2. **Review Stack Parameters:**
   Review the pre-configured parameters (all defaults are optimized for this workshop):

![CloudFormation Stack Configuration](/images/5-Workshop/5.2-Prerequisite/create-stack1.png)

3. **Acknowledge IAM Resource Creation:**
   - Scroll to the bottom of the page
   - Check both acknowledgment boxes:
     - ✓ "I acknowledge that AWS CloudFormation might create IAM resources."
     - ✓ "I acknowledge that AWS CloudFormation might create IAM resources with custom names."
   - Click **Create stack**

![Acknowledge and Create Stack](/images/5-Workshop/5.2-Prerequisite/create-stack2.png)

4. **Monitor Deployment Progress:**
   - CloudFormation will begin provisioning resources
   - Expected deployment time: **approximately 15-20 minutes**
   - You can monitor progress in the CloudFormation console under the "Events" tab
   - The stack uses nested stacks to organize resource creation logically

**What Gets Created:**
The CloudFormation template automatically provisions:
- **2 Virtual Private Clouds (VPCs)**: One simulating cloud environment, one simulating on-premises
- **Multiple Subnets**: Public and private subnets across availability zones
- **AWS Transit Gateway**: Central hub for VPC connectivity
- **Site-to-Site VPN**: Pre-configured VPN connection between VPCs
- **3 EC2 Instances**: Test instances in different network segments
- **Security Groups**: Pre-configured for workshop traffic
- **IAM Roles**: Instance profiles for EC2 instances
- **Route Tables**: Properly configured routing between environments

![Deployment Complete](/images/5-Workshop/5.2-Prerequisite/complete.png)

**Verify Deployment:**

Once the stack status shows `CREATE_COMPLETE`, verify the resources:

1. **Check VPCs Created:**
   Navigate to VPC Dashboard and confirm two VPCs are present:
   - Cloud VPC (for AWS cloud resources)
   - On-Premises VPC (simulating datacenter)

![VPCs Created](/images/5-Workshop/5.2-Prerequisite/vpcs.png)

2. **Check EC2 Instances:**
   Navigate to EC2 Dashboard and verify three instances are running:
   - Cloud Test Instance (in Cloud VPC)
   - On-Prem Test Instance (in On-Prem VPC)
   - VPN Gateway Instance (for Site-to-Site connectivity)

![EC2 Instances Created](/images/5-Workshop/5.2-Prerequisite/ec2.png)

**Troubleshooting:**
- If stack creation fails, check the "Events" tab for error messages
- Ensure your IAM user has all required permissions from the policy above
- Verify you're in the us-east-1 region
- Check AWS service quotas for EC2, VPC, and CloudFormation if you encounter limits

You're now ready to proceed with the workshop exercises!