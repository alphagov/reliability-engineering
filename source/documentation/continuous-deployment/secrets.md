## Secrets

The Reliability Engineering Concourse delegates secrets management to AWS
Systems Manager Parameter Store and Key Management Service.

Secrets can be managed by permitted users using the [GDS
CLI](https://github.com/alphagov/gds-cli):

```
gds cd secrets -t $TEAMNAME add [$PIPELINE/]$SECRETNAME $SECRETVALUE
```

Similar `rm`, `ls` and `get` subcommands exist to perform other operations.

Concourse injects secrets into pipelines at runtime using double parentheses
syntax:

<pre><code>resources:
  - name: my-repository
    type: git
    source:
      uri: git@github.com:alphagov/my-repository.git
      branch: master

      # This is a secret
      private_key: |
        ((github-ssh-key))
</code></pre>

### Pre-populated secrets

For your convenience when writing Concourse pipelines, there are also some
variables provided for you which are generated automatically and are immutable
(hence the `readonly_` prefix).

- `readonly_access_key_id`, `readonly_secret_access_key`, `readonly_session_token` - AWS credentials corresponding to your team.  Can be used with things like the `semver` resource to access your S3 bucket, or with `registry-image` for authenticating to ECR.

- `readonly_private_bucket_name` - the name of the private AWS S3 bucket
- `readonly_private_bucket_arn` - the ARN of the private AWS S3 bucket
- `readonly_private_bucket_domain_name` - the domain name of the private AWS S3 bucket


- `readonly_public_bucket_name` - the name of the public AWS S3 bucket
- `readonly_public_bucket_arn` - the ARN of the public AWS S3 bucket
- `readonly_public_bucket_domain_name` - the domain name of the public AWS S3 bucket


- `readonly_private_ecr_repo_name` - the name of the private AWS ECR
- `readonly_private_ecr_repo_arn` - the ARN of the private AWS ECR
- `readonly_private_ecr_repo_url` - the URL of the private AWS ECR
- `readonly_private_ecr_repo_registry_id` - the ID of the private AWS ECR

- `readonly_codecommit_pool_uri` - the URI of the pool resource git repository
- `readonly_codecommit_private_key` - the private key for the pool resource git repository

- `readonly_team_name` - the name of the Concourse team
- `readonly_local_user_password` - the password for a local user of the Concourse team, which is used for updating pipelines

- `readonly_secrets_path_prefix` - the secrets path prefix, which is used for managing secrets
- `readonly_secrets_kms_key_id` - the AWS KMS Key ID, which is used for encrypting secrets at rest

### Efficient use of secrets

AWS accounts are throttled in the rate they can make secrets requests, and approaching this
rate limit may cause occasional pipeline failures for random users during surges. Therefore
it's helpful if users can avoid certain patterns that are known to cause inflated numbers
of secrets requests.

- Requests for non-existent secrets actually result in 4 requests behind the scenes,
  and concourse's secret caching time is significantly reduced if a secret is not found. This
  means that untended pipelines requesting missing secrets can account for a significant
  amount of our allowance.
- Resources that use secrets in their configuration will continue using those secrets
  to check that resource whether or not that pipeline is heavily used. It's helpful to pause
  pipelines which are rarely needed.

A "perfect storm" is for a pipeline to be left untended with a resource using a missing
secret. This can result in a continuous high rate of unnecessary requests.
