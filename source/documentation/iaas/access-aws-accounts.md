## Access AWS Accounts

People joining GDS do not automatically have access to [Amazon Web Services (AWS)][]. To grant a user access to AWS you’ll need to add them to the gds-users base account.

To add a user to the gds-users base account, complete the Request user access section of the [request an AWS account form][] form.

After a user is added to the GDS base account it’s the individual teams’ responsibility to grant that user access to any [team specific roles][].

Once setup is complete users can access AWS using the AWS Console or the command line.

### Use AWS Console

<ul>
  <li>
    Sign in to the GDS base account at:
    <a href="https://gds-users.signin.aws.amazon.com/console">https://gds-users.signin.aws.amazon.com/console</a>
    <details>
      <summary><small>Screenshot of the sign in page</small></summary>
      <img src="/images/aws-base-account-signin-form.png" alt="Screenshot of the sign in page">
    </details>
  </li>
  <li>
    From the top right dropdown menu, select **Switch Role**
    <details>
      <summary><small>Screenshot of the switch role menu item</small></summary>
      <img src="/images/aws-switch-role-menu.png" alt="Screenshot of the sign in page">
    </details>
  </li>
  <li>
    Complete **Account** with account or <a href="https://docs.aws.amazon.com/IAM/latest/UserGuide/console_account-alias.html#AboutAccountAlias">account alias</a> and the role you're switching to
    <details>
      <summary><small>Screenshot of the switch role page</small></summary>
      <img src="/images/aws-switch-role-form.png" alt="Screenshot of the sign in page">
    </details>
  </li>
</ul>

Teams manage their own lists of accounts and roles, for example [GOV.UK](https://github.com/alphagov/govuk-aws-data/blob/master/docs/govuk-aws-accounts.md),
[Verify](https://github.com/alphagov/verify-blackbox-passwords/blob/master/aws-accounts.info),
and [Digital Marketplace](https://alphagov.github.io/digitalmarketplace-manual/infrastructure/aws-accounts.html#available-roles).

###  Use the command line

There are several command line tools you can use to work with AWS, such as:

* [AWS Command Line Interface (AWS CLI)][] - to manage your AWS services
* [AWS Shell][] - an integrated shell for working with the AWS CLI
* [Terraform][] - for building, changing, and versioning infrastructure

These tools need credentials to access AWS. We recommend using [gds-cli][] to manage your credentials.

#### Installing gds-cli

[gds-cli][] needs to be installed and available on your `PATH`. The
easiest way to do this is via Homebrew: `brew install alphagov/gds/gds-cli`.

When you run `gds aws <some-role>` for the first time, it will prompt for your
Access Key ID and Secret Access Key for AWS access and store them
using [aws-vault][].

You can find your `aws_access_key_id` and `aws_secret_access_key` by logging into the GDS base account using the [AWS Console][].

If you get MFA codes from your phone, run `gds config yubikey false`

#### Using gds-cli

To use `gds-cli`, run `gds aws` to list all available accounts, then
select one to access with `gds aws <some-role>`.

Further usage instructions can be found in the [gds-cli README](https://github.com/alphagov/gds-cli#usage).

[Amazon Web Services (AWS)]: https://aws.amazon.com/
[process called assuming roles]: https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-console.html
[request an AWS account form]: https://gds-request-an-aws-account.cloudapps.digital/
[AWS Command Line Interface (AWS CLI)]: https://aws.amazon.com/cli/
[AWS Shell]: https://github.com/awslabs/aws-shell
[Terraform]: https://www.terraform.io/
[AWS Console]: https://gds-users.signin.aws.amazon.com/console
[creating, modifying, and viewing Access Keys (Console)]: https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html#Using_CreateAccessKey
[Multi-Factor Authentication (MFA)]: https://aws.amazon.com/iam/details/mfa/
[Terraform]: https://www.terraform.io/
[AWS Security Token Service (STS)]: https://docs.aws.amazon.com/STS/latest/APIReference/Welcome.html
[AWS CLI]: https://aws.amazon.com/cli/
[AWS CLI documentation]: https://docs.aws.amazon.com/cli/latest/reference/sts/assume-role.html
[Creating an AWS credentials file]: #creating-an-aws-credentials-file
[aws-vault]: https://github.com/99designs/aws-vault#readme
[gds-cli]: https://github.com/alphagov/gds-cli
[maximum session duration]: https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use.html#id_roles_use_view-role-max-session
[team specific roles]: https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-console.html
