---
title : "Các bước chuẩn bị"
date :  2025-12-06
weight : 2
chapter : false
pre : " <b> 5.2. </b> "
---

{{% notice warning %}}
⚠️ **Lưu ý:** Các thông tin dưới đây chỉ nhằm mục đích tham khảo, vui lòng **không sao chép nguyên văn** cho bài báo cáo của bạn kể cả warning này.
{{% /notice %}}

#### Quyền IAM Bắt Buộc

Trước khi bắt đầu workshop này, bạn phải đảm bảo AWS IAM user hoặc role của bạn có đủ quyền để tạo và quản lý các tài nguyên cần thiết. Gắn IAM policy tùy chỉnh sau vào tài khoản người dùng của bạn hoặc assume một role với các quyền này.

**Lưu Ý Bảo Mật Quan Trọng:**
- Xem xét tất cả các quyền trước khi áp dụng để hiểu rõ tài nguyên nào sẽ được tạo
- Các quyền này cần thiết cho việc triển khai và dọn dẹp workshop
- Sau khi hoàn thành workshop, hãy cân nhắc xóa các quyền này nếu không còn cần thiết nữa
- Đối với môi trường production, luôn tuân theo nguyên tắc cấp quyền tối thiểu (principle of least privilege)

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

**Phạm Vi Policy:**
IAM policy này cấp quyền trên nhiều dịch vụ AWS cần thiết cho workshop:
- **EC2 & VPC Networking**: Tạo và quản lý VPCs, subnets, route tables, security groups, Transit Gateway, VPN connections, và VPC endpoints
- **CloudFormation**: Triển khai và quản lý các stack infrastructure as code
- **IAM**: Tạo roles và instance profiles cho EC2 instances
- **S3**: Tạo buckets và quản lý objects cho tài nguyên workshop
- **Lambda & CloudWatch**: Triển khai serverless functions và giám sát tài nguyên
- **Route53**: Cấu hình DNS và resolver endpoints
- **SSM (Systems Manager)**: Cho phép truy cập instance an toàn qua Session Manager
- **Secrets Manager**: Lưu trữ và truy xuất dữ liệu cấu hình nhạy cảm

#### Thiết Lập Môi Trường Sử Dụng Infrastructure as Code

**Region Workshop:** Lab này được thiết kế cho region **US East (N. Virginia)** (`us-east-1`). Đảm bảo bạn chọn region này trước khi tiếp tục.

**Tổng Quan Triển Khai Tự Động:**
Thay vì tạo từng tài nguyên thủ công, chúng ta sẽ sử dụng AWS CloudFormation để tự động hóa toàn bộ quá trình thiết lập cơ sở hạ tầng. Cách tiếp cận Infrastructure as Code (IaC) này cung cấp:
- **Tính nhất quán**: Tất cả người tham gia làm việc với cấu hình giống hệt nhau
- **Tốc độ**: Triển khai môi trường phức tạp với nhiều tài nguyên trong vài phút
- **Khả năng lặp lại**: Dễ dàng tạo lại hoặc dọn dẹp môi trường
- **Best Practices**: Được cấu hình sẵn với các thiết lập được AWS khuyến nghị

**Các Bước Triển Khai:**

1. **Khởi Chạy CloudFormation Stack:**
   - Click vào link này để mở CloudFormation quick-create console: [Triển Khai Cơ Sở Hạ Tầng Workshop](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/quickcreate?templateURL=https://s3.us-east-1.amazonaws.com/reinvent-endpoints-builders-session/Nested.yaml&stackName=PLCloudSetup)
   - Template URL đã được điền sẵn với cấu hình workshop
   - Tên stack tự động được đặt là `PLCloudSetup`

2. **Xem Lại Các Tham Số Stack:**
   Xem lại các tham số đã được cấu hình sẵn (tất cả các giá trị mặc định đều được tối ưu cho workshop này):

![Cấu Hình CloudFormation Stack](/images/5-Workshop/5.2-Prerequisite/create-stack1.png)

3. **Xác Nhận Tạo Tài Nguyên IAM:**
   - Cuộn xuống cuối trang
   - Đánh dấu vào cả hai ô xác nhận:
     - ✓ "I acknowledge that AWS CloudFormation might create IAM resources."
     - ✓ "I acknowledge that AWS CloudFormation might create IAM resources with custom names."
   - Click **Create stack**

![Xác Nhận và Tạo Stack](/images/5-Workshop/5.2-Prerequisite/create-stack2.png)

4. **Theo Dõi Tiến Trình Triển Khai:**
   - CloudFormation sẽ bắt đầu cung cấp tài nguyên
   - Thời gian triển khai dự kiến: **khoảng 15-20 phút**
   - Bạn có thể theo dõi tiến trình trong CloudFormation console dưới tab "Events"
   - Stack sử dụng nested stacks để tổ chức việc tạo tài nguyên một cách logic

**Những Gì Được Tạo:**
CloudFormation template tự động cung cấp:
- **2 Virtual Private Clouds (VPCs)**: Một mô phỏng môi trường cloud, một mô phỏng on-premises
- **Nhiều Subnets**: Public và private subnets trên các availability zones
- **AWS Transit Gateway**: Hub trung tâm cho kết nối VPC
- **Site-to-Site VPN**: Kết nối VPN được cấu hình sẵn giữa các VPCs
- **3 EC2 Instances**: Test instances trong các phân đoạn mạng khác nhau
- **Security Groups**: Được cấu hình sẵn cho lưu lượng workshop
- **IAM Roles**: Instance profiles cho EC2 instances
- **Route Tables**: Routing được cấu hình đúng giữa các môi trường

![Triển Khai Hoàn Tất](/images/5-Workshop/5.2-Prerequisite/complete.png)

**Xác Minh Triển Khai:**

Khi trạng thái stack hiển thị `CREATE_COMPLETE`, hãy xác minh các tài nguyên:

1. **Kiểm Tra VPCs Đã Tạo:**
   Di chuyển đến VPC Dashboard và xác nhận hai VPCs đã được tạo:
   - Cloud VPC (cho tài nguyên AWS cloud)
   - On-Premises VPC (mô phỏng datacenter)

![VPCs Đã Tạo](/images/5-Workshop/5.2-Prerequisite/vpcs.png)

2. **Kiểm Tra EC2 Instances:**
   Di chuyển đến EC2 Dashboard và xác minh ba instances đang chạy:
   - Cloud Test Instance (trong Cloud VPC)
   - On-Prem Test Instance (trong On-Prem VPC)
   - VPN Gateway Instance (cho Site-to-Site connectivity)

![EC2 Instances Đã Tạo](/images/5-Workshop/5.2-Prerequisite/ec2.png)

**Khắc Phục Sự Cố:**
- Nếu việc tạo stack thất bại, kiểm tra tab "Events" để xem thông báo lỗi
- Đảm bảo IAM user của bạn có tất cả các quyền cần thiết từ policy ở trên
- Xác minh bạn đang ở region us-east-1
- Kiểm tra AWS service quotas cho EC2, VPC, và CloudFormation nếu bạn gặp giới hạn

Bạn đã sẵn sàng để tiếp tục với các bài tập workshop!
