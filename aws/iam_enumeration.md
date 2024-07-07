# IAM Enumeration CLI commands
Retrieves general account information about including IAM users, groups, 
roles, and policies, and their relationships to one another. 
These are meant to be non-destructive enumeration commands. They only retrieve information. They do not modify resources.
Official AWS IAM CLI documentation: https://awscli.amazonaws.com/v2/documentation/api/latest/reference/iam/index.html

# The "whoami" in AWS
`aws sts get-caller-identity` Returns details about the IAM user or role whose credentials are used to call the operation. While not an IAM command (it's an STS command), this is often the first command that gets used for IAM enumeration which is why it's included here. 
No policy can deny issuing this command since it gives the same information when access is denied. More on that here: https://awscli.amazonaws.com/v2/documentation/api/latest/reference/sts/get-caller-identity.html

# General IAM account information
`aws iam get-account-authorization-details` Gives a snapshot of the configuration of IAM permissions (users, groups, roles, and  policies) and their relationships in your account.

# User enumeration
`aws iam list-users`

`aws iam list-service-specific-credentials` Get service-specific credentials associated with the IAM user

`aws iam get-user --user-name <username>` Get metadata of user. Includes permissions boundaries!

`aws iam list-access-keys [--user-name <value>]` List existing access keys for a specific user (your current user being the default if no value is specified)

`aws iam list-user-policies --user-name <username>` Get inline policies for a user

`aws iam get-user-policy --user-name <username> --policy-name <policyname>` Get details about an inline policy

`aws iam list-attached-user-policies --user-name <username>` Get attached (managed) policies (instead of inline policies). These can be either AWS-managed or Customer-managed policies

`aws iam get-login-profile --user-name` Checks to see if the user has a login profile (password to log into the AWS Console). Returns the UserName and CreatedDate, which reflects the creation date of the password

# Groups Enumeration 
`aws iam list-groups` # Get groups in account

`aws iam list-groups-for-user --user-name <username>` Get groups related to a user

`aws iam get-group --group-name <name>` Get a group's details

`aws iam list-group-policies --group-name <username>` Get inline policies of a group

`aws iam get-group-policy --group-name <username> --policy-name <policyname>` Get inline policy details

`aws iam list-attached-group-policies --group-name <name>` Get attached (managed) policies for a group (instead of inline policies). These can be either AWS-managed or Customer-managed policies

# Enumerate roles
`aws iam list-roles` Get roles in the AWS account

`aws iam get-role --role-name <role-name>` Get details about a specific role

`aws iam list-role-policies --role-name <name>` Get inline policies of a role

`aws iam get-role-policy --role-name <name> --policy-name <name>` Get inline policy details

`aws iam list-attached-role-policies --role-name <role-name>` Get attached (managed) policies for a role (instead of inline policies). These can be either AWS-managed or Customer-managed policies

# Enumerate policies
`aws iam list-policies [--only-attached] [--scope Local]` List policies `[--only-attached]` is optional and will list policies only if they're attached to an IAM user/group/role `[--scope local]` is optional and will only list policies that are Customer-managed, not AWS-managed. `--scope AWS` would be the opposite

`aws iam list-policies-granting-service-access --arn <identity> --service-namespaces <svc>` Get list of policies that a user/group/role can use to access a specific service

*Example: aws iam list-policies-granting-service-access --arn arn:aws:iam::123456789012:user/UserName --service-namespaces eks*

`aws iam get-policy --policy-arn <policy_arn>` Get details for a specific policy

`aws iam list-policy-versions --policy-arn <arn>` Returns information about policy versions for a specific managed policy, including which one is the current default version

`aws iam get-policy-version --policy-arn <arn:aws:iam::123456789012:policy/example-policy> --version-id <v1 or v2 or ...>` Get details for a specific policy version since policies can have multiple versions

`aws iam list-entities-for-policy --policy-arn <value>` Lists all the IAM users/groups/roles that the managed policy is attached to

# MFA
`aws iam list-mfa-devices [--user-name <username>]` Get list of all MFA devices for a specific user. If no user is provided, AWS auto determines the user based on the access key ID

`aws iam list-virtual-mfa-devices [--assignment-status <value>]` Get list of all MFA devices in the AWS account
Can use optional `--assignment-status` to filter by Assigned, Unassigned, or Any (the default)