## Stack Role
Docs
- https://docs.aws.amazon.com/prescriptive-guidance/latest/least-privilege-cloudformation/permissions-use-cloudformation.html

A service role is an AWS Identity and Access Management (IAM) role that allows CloudFormation to make calls to resources in a stack on your behalf. You can specify an IAM role that allows CloudFormation to create, update, or delete your stack resources. By default, CloudFormation uses a temporary session that it generates from your user credentials for stack operations. If you specify a service role, CloudFormation uses that role's credentials.

Use a service role to explicitly specify the actions that CloudFormation can perform, which might not always be the same actions that you or other users can do. For example, you might have administrative privileges, but you can limit CloudFormation access to only Amazon EC2 actions.

- When creating Stack, CFN creates physical resources
- CNF uses the permissions of the logged in identity
- Which means you need permissions for AWS
- CFN can assume a role to gain the permissions
- Allow you to implement role separation
- Identity creating the stack does not need resource permissions only PassRole

Eg.
Gabby (Account Admin)
- Creates the IAM Role which Phil has no permissions to assume/edit it ONLY can PASS it into CNF
- IAM ROLE is created is permissions to create, update, delete AWS resouces

Phil (Account HelpDesk)
- Doesn't need permissions to manipulate resources
- Only needs permissions to CREATE, UPDATE, DELETE stacks and PASS the role to CFN

### DEMO
1) Using Root account apply the RolesAndPolicy.yml
RolesAndPolicy.yml assumes account of admin has already been existed
and give the following permission
- Gives admin the permission to view cloudformation UI and Create S3 Bucket(Due to template requires to be stored in S3 Bucket ONLY REQUIRED VIA CONSOLE)
- Gives admin the permission to get and pass role (Required to be able to select which roles Cloudformation will run with)
- Gives admin the permission to create, update, delete cloudformation stacks
- Create a service role that allow the creation and deletion of VPC with trust permission for Cloudformation service ONLY

2) Using admin account apply the CreateVPC.yml