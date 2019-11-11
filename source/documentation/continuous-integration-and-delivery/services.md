## Services

For convenience, the Reliability Engineering team provision the following
resources for each Concourse team:

- A private AWS S3 bucket (publicly readable from the internet by anyone)
- A public AWS S3 bucket
- A private AWS Elastic Container Registry (ECR)

which can be accessed only by the individual Concourse team's worker AWS IAM roles.

Each Concourse team's worker instances have a specific AWS IAM role, this
allows Concourse to assume roles in other AWS accounts, if those accounts have
a role which Concourse is permitted to assume.