# Identity and access management for WorkSpaces<a name="workspaces-access-control"></a>

By default, IAM users don't have permissions for WorkSpaces resources and operations\. To allow IAM users to manage WorkSpaces resources, you must create an IAM policy that explicitly grants them permissions, and attach the policy to the IAM users or groups that require those permissions\. For more information about IAM policies, see [Policies and Permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html) in the *IAM User Guide* guide\.

WorkSpaces also creates an IAM role to allow the WorkSpaces service access to required resources\.

**Note**  
Amazon WorkSpaces doesn’t support the provisioning of IAM credentials into a WorkSpace \(such as with an instance profile\)\.

For more information about IAM, see [Identity and Access Management \(IAM\)](https://aws.amazon.com/iam) and the [https://docs.aws.amazon.com/IAM/latest/UserGuide/](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\. You can find the WorkSpaces\-specific resources, actions, and condition context keys for use in IAM permission policies at [Actions, Resources, and Condition Keys for Amazon WorkSpaces](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonworkspaces.html) in the *IAM User Guide*\.

For a tool that helps you create IAM policies, see the [ AWS Policy Generator](http://aws.amazon.com/blogs/aws/aws-policy-generator/)\. You can also use the [IAM Policy Simulator](https://docs.aws.amazon.com/IAM/latest/UsingPolicySimulatorGuide/) to test whether a policy would allow or deny a specific request to AWS\.

**Example 1: Perform all WorkSpaces tasks**  <a name="perform-workspaces-tasks"></a>
The following policy statement grants an IAM user permission to perform all WorkSpaces tasks, including creating and managing directories\. It also grants permission to run the quick setup procedure\.  
Although Amazon WorkSpaces fully supports the `Action` and `Resource` elements when using the API and command line tools, to use Amazon WorkSpaces from the AWS Management Console, an IAM user must have permissions for the following actions and resources:   
+ Actions: "`workspaces:*"` and `"ds:*"`
+ Resources: `"Resource": "*"`
The following example policy shows how to allow an IAM user to use Amazon WorkSpaces from the AWS Management Console\. 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "workspaces:*",
        "ds:*",
        "iam:PassRole", 
        "iam:GetRole", 
        "iam:CreateRole", 
        "iam:PutRolePolicy", 
        "kms:ListAliases",
        "kms:ListKeys",
        "ec2:CreateVpc", 
        "ec2:CreateSubnet", 
        "ec2:CreateNetworkInterface", 
        "ec2:CreateInternetGateway", 
        "ec2:CreateRouteTable", 
        "ec2:CreateRoute", 
        "ec2:CreateTags", 
        "ec2:CreateSecurityGroup", 
        "ec2:DescribeInternetGateways",
        "ec2:DescribeSecurityGroups",
        "ec2:DescribeRouteTables", 
        "ec2:DescribeVpcs", 
        "ec2:DescribeSubnets", 
        "ec2:DescribeNetworkInterfaces", 
        "ec2:DescribeAvailabilityZones", 
        "ec2:AttachInternetGateway", 
        "ec2:AssociateRouteTable", 
        "ec2:AuthorizeSecurityGroupEgress", 
        "ec2:AuthorizeSecurityGroupIngress", 
        "ec2:DeleteSecurityGroup", 
        "ec2:DeleteNetworkInterface", 
        "ec2:RevokeSecurityGroupEgress", 
        "ec2:RevokeSecurityGroupIngress",
        "workdocs:RegisterDirectory",
        "workdocs:DeregisterDirectory",
        "workdocs:AddUserToGroup"
      ],
      "Resource": "*"
    }
  ]
}
```

**Example 2: Perform WorkSpace\-specific tasks**  <a name="perform-workspace-specific-tasks"></a>
The following policy statement grants an IAM user permission to perform WorkSpace\-specific tasks, such as launching and removing WorkSpaces\. In the policy statement, the `ds:*` action grants broad permissions — full control over all Directory Services objects in the account\.  

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "workspaces:*",
        "ds:*",
        "iam:PutRolePolicy"
      ],
      "Resource": "*"
    }
  ]
}
```
To also grant the user the ability to enable Amazon WorkDocs for users within WorkSpaces, add the `workdocs` operation shown in the following example\.  

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "workspaces:*",
        "ds:*",
        "workdocs:AddUserToGroup"
      ],
      "Resource": "*"
    }
  ]
}
```
To also grant the user the ability to use the Launch WorkSpaces wizard, add the `kms` operations as shown in the following example\.  

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "workspaces:*",
        "ds:*",
        "workdocs:AddUserToGroup",
        "kms:ListAliases",
        "kms:ListKeys"
      ],
      "Resource": "*"
    }
  ]
}
```

**Example 3: Perform all WorkSpaces tasks for BYOL WorkSpaces**  <a name="perform-byol-workspaces-tasks"></a>
The following policy statement grants an IAM user permission to perform all WorkSpaces tasks, including those Amazon EC2 tasks necessary for creating Bring Your Own License \(BYOL\) WorkSpaces\.  

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "workspaces:*",
        "ds:*",
        "iam:PassRole", 
        "iam:GetRole", 
        "iam:CreateRole", 
        "iam:PutRolePolicy", 
        "kms:ListAliases",
        "kms:ListKeys",
        "ec2:CreateVpc", 
        "ec2:CreateSubnet", 
        "ec2:CreateNetworkInterface", 
        "ec2:CreateInternetGateway", 
        "ec2:CreateRouteTable", 
        "ec2:CreateRoute", 
        "ec2:CreateTags", 
        "ec2:CreateSecurityGroup",
        "ec2:DescribeImages",
        "ec2:ModifyImageAttribute",
        "ec2:DescribeInternetGateways",
        "ec2:DescribeSecurityGroups",
        "ec2:DescribeRouteTables", 
        "ec2:DescribeVpcs", 
        "ec2:DescribeSubnets", 
        "ec2:DescribeNetworkInterfaces", 
        "ec2:DescribeAvailabilityZones", 
        "ec2:AttachInternetGateway", 
        "ec2:AssociateRouteTable", 
        "ec2:AuthorizeSecurityGroupEgress", 
        "ec2:AuthorizeSecurityGroupIngress", 
        "ec2:DeleteSecurityGroup", 
        "ec2:DeleteNetworkInterface", 
        "ec2:RevokeSecurityGroupEgress", 
        "ec2:RevokeSecurityGroupIngress",
        "workdocs:RegisterDirectory",
        "workdocs:DeregisterDirectory",
        "workdocs:AddUserToGroup"
      ],
      "Resource": "*"
    }
  ]
}
```

## Creating the workspaces\_DefaultRole Role<a name="create-default-role"></a>

Before you can register a directory using the API, you must create the workspaces\_DefaultRole role, if it doesn't already exist\.

**To create the workspaces\_DefaultRole role**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane on the left, choose **Roles**\.

1. Choose **Create role**\.

1. Under **Select type of trusted entity**, choose **Another AWS account**\.

1. For **Account ID**, enter your account ID with no hyphens or spaces\.

1. For **Options**, do not specify multi\-factor authentication \(MFA\)\.

1. Choose **Next: Permissions**\.

1. On the **Attach permissions policies** page, select the AWS managed policies **AmazonWorkSpacesServiceAccess** and **AmazonWorkSpacesSelfServiceAccess**\.

1. Under **Set permissions boundary**, we recommend that you not use a permissions boundary because of the potential for conflicts with the policies that are attached to the workspaces\_DefaultRole role\. Such conflicts could block certain necessary permissions for the role\.

1. Choose **Next: Tags**\.

1. On the **Add tags \(optional\)** page, add tags if needed\.

1. Choose **Next: Review**\.

1. On the **Review** page, for **Role name**, enter **workspaces\_DefaultRole**\.

1. \(Optional\) For **Role description**, enter a description\.

1. Choose **Create Role**\.

1. On the **Summary** page for the workspaces\_DefaultRole role, choose the **Trust relationships** tab\.

1. On the **Trust relationships** tab, choose **Edit trust relationship**\.

1. On the **Edit Trust Relationship** page, replace the existing policy statement with the following statement\.

   ```
   {
     "Statement": [
       {
         "Effect": "Allow",
         "Principal": {
           "Service": "workspaces.amazonaws.com"
         },
         "Action": "sts:AssumeRole"
       }
     ]
   }
   ```

1. Choose **Update Trust Policy**\.

## Specify WorkSpaces resources in an IAM policy<a name="wsp_iam_resource"></a>

To specify an WorkSpaces resource in the `Resource` element of the policy statement, use the Amazon Resource Name \(ARN\) of the resource\. You control access to your WorkSpaces resources by either allowing or denying permissions to use the API actions that are specified in the `Action` element of your IAM policy statement\. WorkSpaces defines ARNs for WorkSpaces, bundles, IP groups, and directories\.

### WorkSpace ARN<a name="wsp_arn_syntax"></a>

A WorkSpace ARN has the syntax shown in the following example\.

```
arn:aws:workspaces:region:account_id:workspace/workspace_identifier
```

*region*  
The Region that the WorkSpace is in \(for example, `us-east-1`\)\.

*account\_id*  
The ID of the AWS account, with no hyphens \(for example, `123456789012`\)\.

*workspace\_identifier*  
The ID of the WorkSpace \(for example, `ws-a1bcd2efg`\)\.

The following is the format of the `Resource` element of a policy statement that identifies a specific WorkSpace\.

```
"Resource": "arn:aws:workspaces:region:account_id:workspace/workspace_identifier"
```

You can use the `*` wildcard to specify all WorkSpaces that belong to a specific account in a specific Region\.

### Image ARN<a name="image_arn_syntax"></a>

A WorkSpace image ARN has the syntax shown in the following example\.

```
arn:aws:workspaces:region:account_id:workspaceimage/image_identifier
```

*region*  
The Region that the WorkSpace image is in \(for example, `us-east-1`\)\.

*account\_id*  
The ID of the AWS account, with no hyphens \(for example, `123456789012`\)\.

*bundle\_identifier*  
The ID of the WorkSpace image \(for example, `wsi-a1bcd2efg`\)\.

The following is the format of the `Resource` element of a policy statement that identifies a specific image\.

```
"Resource": "arn:aws:workspaces:region:account_id:workspaceimage/image_identifier"
```

You can use the `*` wildcard to specify all images that belong to a specific account in a specific Region\.

### Bundle ARN<a name="bundle_arn_syntax"></a>

A bundle ARN has the syntax shown in the following example\.

```
arn:aws:workspaces:region:account_id:workspacebundle/bundle_identifier
```

*region*  
The Region that the WorkSpace is in \(for example, `us-east-1`\)\.

*account\_id*  
The ID of the AWS account, with no hyphens \(for example, `123456789012`\)\.

*bundle\_identifier*  
The ID of the WorkSpace bundle \(for example, `wsb-a1bcd2efg`\)\.

The following is the format of the `Resource` element of a policy statement that identifies a specific bundle\.

```
"Resource": "arn:aws:workspaces:region:account_id:workspacebundle/bundle_identifier"
```

You can use the `*` wildcard to specify all bundles that belong to a specific account in a specific Region\.

### IP Group ARN<a name="ipgroup_arn_syntax"></a>

An IP group ARN has the syntax shown in the following example\.

```
arn:aws:workspaces:region:account_id:workspaceipgroup/ipgroup_identifier
```

*region*  
The Region that the WorkSpace is in \(for example, `us-east-1`\)\.

*account\_id*  
The ID of the AWS account, with no hyphens \(for example, `123456789012`\)\.

*ipgroup\_identifier*  
The ID of the IP group \(for example, `wsipg-a1bcd2efg`\)\.

The following is the format of the `Resource` element of a policy statement that identifies a specific IP group\.

```
"Resource": "arn:aws:workspaces:region:account_id:workspaceipgroup/ipgroup_identifier"
```

You can use the `*` wildcard to specify all IP groups that belong to a specific account in a specific Region\.

### Directory ARN<a name="directory_arn_syntax"></a>

A directory ARN has the syntax shown in the following example\.

```
arn:aws:workspaces:region:account_id:directory/directory_identifier
```

*region*  
The Region that the WorkSpace is in \(for example, `us-east-1`\)\.

*account\_id*  
The ID of the AWS account, with no hyphens \(for example, `123456789012`\)\.

*directory\_identifier*  
The ID of the directory \(for example, `d-12345a67b8`\)\.

The following is the format of the `Resource` element of a policy statement that identifies a specific directory\.

```
"Resource": "arn:aws:workspaces:region:account_id:directory/directory_identifier"
```

You can use the `*` wildcard to specify all directories that belong to a specific account in a specific Region\.

### Connection alias ARN<a name="connection_alias_arn_syntax"></a>

A connection alias ARN has the syntax shown in the following example\.

```
arn:aws:workspaces:region:account_id:connectionalias/connectionalias_identifier
```

*region*  
The Region that the connection alias is in \(for example, `us-east-1`\)\.

*account\_id*  
The ID of the AWS account, with no hyphens \(for example, `123456789012`\)\.

*connectionalias\_identifier*  
The ID of the connection alias \(for example, `wsca-12345a67b8`\)\.

The following is the format of the `Resource` element of a policy statement that identifies a specific connection alias\.

```
"Resource": "arn:aws:workspaces:region:account_id:connectionalias/connectionalias_identifier"
```

You can use the `*` wildcard to specify all connection aliases that belong to a specific account in a specific Region\.

### API actions with no support for resource\-level permissions<a name="no-resource-level-permissions"></a>

You can't specify a resource ARN with the following API actions:
+ `AssociateIpGroups`
+ `CreateIpGroup`
+ `CreateTags`
+ `DeleteTags`
+ `DeleteWorkspaceImage`
+ `DescribeAccount`
+ `DescribeAccountModifications`
+ `DescribeTags`
+ `DescribeWorkspaceDirectories`
+ `DescribeWorkspaceImages`
+ `DescribeWorkspaces`
+ `DescribeWorkspacesConnectionStatus`
+ `DisassociateIpGroups`
+ `ImportWorkspaceImage`
+ `ListAvailableManagementCidrRanges`
+ `ModifyAccount`

For API actions that don't support resource\-level permissions, you must specify the resource statement shown in the following example\.

```
"Resource": "*"
```

### API actions that don't support account\-level restrictions on shared resources<a name="shared-resource-permissions"></a>

For the following API actions, you can't specify an account ID in the resource ARN when the resource isn't owned by the account:
+ `AssociateConnectionAlias`
+ `CopyWorkspaceImage`
+ `DisassociateConnectionAlias`

For these API actions, you can specify an account ID in the resource ARN only when that account owns the resources to be acted upon\. When the account doesn't own the resources, you must specify `*` for the account ID, as shown in the following example\.

```
"arn:aws:workspaces:region:*:resource_type/resource_identifier"
```