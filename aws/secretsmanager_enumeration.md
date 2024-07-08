# AWS Secrets Manager Enumeration CLI commands
Retrieves general and specific Secrets Manager information including listing secrets, retrieving resource-policy permissions, and retrieving stored secrets. These are meant to be non-destructive enumeration commands. They only retrieve information. They do not modify resources However, keep in mind these actions will still get logged and potentially trigger alerts if the AWS account has monitoring & logging enabled.

Documentation: https://awscli.amazonaws.com/v2/documentation/api/latest/reference/secretsmanager/index.html#cli-aws-secretsmanager

`aws secretsmanager list-secrets [--include-planned-deletion | --no-include-planned-deletion]` 
Retrieves a list of secrets stored in Secrets Manager in that AWS account
Does not include secrets marked for deletion unless you use optional `[--include-planned-deletion]`

`--include-planned-deletion`: This option tells the system that when you ask for a list of secrets, it should also show you the ones that are set to be deleted in the future

`--no-include-planned-deletion`: This option is the opposite. It tells the system that when you ask for a list of secrets, you don't want to see the ones that are planned to delete in the future.
To issue this command, you must have *secretsmanager:ListSecrets* access

`aws secretsmanager list-secret-version-ids --secret-id <value>` 
Lists the versions for a specific secret
To issue this command, you must have *secretsmanager:ListSecretVersionIds* access

`aws secretsmanager get-resource-policy --secret-id <value>` 
Secrets in Secrets Manager can have resource-based permissions policies attached (this is optional but a recommended security practice). This command retrieves it for a particular secret.
To issue this command, you must have *secretsmanager:GetResourcePolicys* access

`aws secretsmanager describe-secret --secret-id <value>` 
Gets the details for a secret, but doesn't include the secret value
It does provide the Arn, Name, Description, KmsKeyId, whether rotation is enabled, and more
To issue this command, you must have *secretsmanager:DescribeSecret* access

`aws secretsmanager get-secret-value --secret-id <value> [--version-id <value>] [--version-stage <value>]` 
This retrieves the secret value as either SecretString or SecretBinary
If you don't specify the optional `[--version-id]`, it will grab the current version
To issue this command, you must have *secretsmanager:GetSecretValue* access