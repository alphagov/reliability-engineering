## Secrets

The Reliability Engineering Concourse delegates secrets management to AWS
Systems Manager Parameter Store and Key Management Service.

Secrets can be managed by users with the `member` role using the [GDS
CLI](https://github.com/alphagov/gds-cli):

```
gds cd secrets add
```

and

```
gds cd secrets remove
```

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

There are also some convenience variables provided for you:

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


- `readonly_team_name` - the name of the Concourse team
- `readonly_local_user_password` - the password for a local user of the Concourse team, which is used for updating pipelines


- `readonly_secrets_path_prefix` - the secrets path prefix, which is used for managing secrets
- `readonly_secrets_kms_key_id` - the AWS KMS Key ID, which is used for encrypting secrets at rest
