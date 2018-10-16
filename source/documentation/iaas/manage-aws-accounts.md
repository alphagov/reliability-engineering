## Manage AWS accounts

If you’re setting up a new AWS account and your team want to use cross-account access use the `gds-users` account as your base account.

Use multiple AWS accounts to:

* enforce administrative isolation between workloads
* minimize the impact of security breaches
* isolate audit data in separate accounts

By having separate AWS accounts for production and test workloads teams can restrict the security impact of a compromised AWS credential.

To avoid managing several sets of AWS credentials for multiple accounts, teams should use roles and cross-account access. AWS describe this approach as [multiple account security strategy][].

### Cross account access

AWS cross account access allows user accounts held in the `gds-users` base account to access resources in target AWS account’s created by your team.

Access to resources within a target AWS account is controlled using [AWS Identity and Access Management (IAM)][] roles managed within that AWS account.

IAM roles establish trust relationships between the target’s (trusting) AWS account and the GDS base (trusted) AWS account.

#### Manage Access to Resources

Users exist in the GDS base account and have permission to assume a role into a target account. The target account defines the roles users in the base account can assume, for example a Data Analyst:

![Assume Role Diagram](../images/assume-role.svg)

#### The Trust Relationship (Assume Role Policy)

The trust relationship describes what entities can assume the role (principles) and imposes conditions on how and when those entities can assume the role. An [AWS principle][] can be a user account, an AWS account or a role, principle entities are expressed as an [Amazon Resource Name (ARN)][].

Use conditions to enforce the presence of Multifactor authentication (MFA), for example restricting access to particular IP addresses or particular times of the day.

It’s strongly recommended not to trust an AWS base account. Trusting an AWS base account allows all of the entities within that base account to assume that particular role.

Example Trust Relationship policy:

    {
      "Version": "2012-10-17",
      "Statement": {
        "Effect": "Allow",
        "Principal": { "AWS": "arn:aws:iam::123456789012:user/example.user@digital.cabinet-office.gov.uk" },
        "Action": "sts:AssumeRole",
        "Condition": { "Bool": { "aws:MultiFactorAuthPresent": "true" } }
      }
    }

#### Resource Permissions (Policies)

Access to resources are managed through [IAM Policies][] which can be custom built or defined by Amazon. Design your roles around the permissions people need to complete a task.

Amazon provides some [examples of AWS policies][] related to IAM permissions.

#### Manage "admin" permissions

It is strongly recommended that anyone supporting an AWS account in an
engineering capacity should have admin access.  Admin access should allow an
engineer to completely recreate any given resource running in the account.

In AWS one can restrict access to resources by only allowing only certain permissions:

```
data "aws_iam_policy_document" "admin" {
  statement {
    actions = [
      "route53:*",
    ]
     resources = ["*"]
    effect    = "Allow"
  }
}
```

This should be done where we do not want services to be run for proprietary
service proliferation reasons, not for security reasons. The use case for this
is so that we can manage what AWS services we consume, for instance not
allowing people to spin up an AWS Neptune database.

Services can be managed at the organisation level using service control
policies, but this is not something we currently use. More information can be
found [here](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_about-scps.html).

It is an anti-pattern for administrators to not have full IAM permissions.  The
reasoning behind this decision is that it prevents untracked access to things.
However in practice it leads to the use of shared users and roles rather than
the creation of a single user and/or role for a given purpose, which violates
the principle of least privilege.

#### Name IAM roles

For IAM roles attached to human IAM users, they should have descriptive names
and ideally conform to the following (except for extenuating circumstances):

- Admin: Can do everything in the account including IAM administration
- Trusted user: Can do most things in the account excluding IAM administration
- Read only: Can read things in the account but not perform any side-effects;
            should NOT be able to do things like `ssm:DescribeParameters`

[multiple account security strategy]: https://aws.amazon.com/answers/account-management/aws-multi-account-security-strategy/
[accessing aws accounts]: access-aws-accounts.html
[AWS Identity and Access Management (IAM)]: https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html
[AWS principle]: https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html
[Amazon Resource Name (ARN)]: https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html
[IAM Policies]: https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html
[examples of AWS policies]: https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html
