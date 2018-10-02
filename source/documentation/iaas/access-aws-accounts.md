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
and [Digital Marketplace](https://alphagov.github.io/digitalmarketplace-manual/aws-accounts.html#available-roles).

###  Use the command line

There are several command line tools you can use to work with AWS, such as:

* [AWS Command Line Interface (AWS CLI)][] - to manage your AWS services
* [AWS Shell][] - an integrated shell for working with the AWS CLI
* [Terraform][] - for building, changing, and versioning infrastructure

These tools need credentials to access AWS. We recommend using [aws-vault][] to manage your credentials.

#### Installing aws-vault

[aws-vault][] needs to be installed and available on your `PATH`. Follow [their
instructions](https://github.com/99designs/aws-vault#installing) to do this.

#### Add your credentials to aws-vault

To add your credentials to aws-vault, run the command `aws-vault add
gds-users`. This will prompt you for your Access Key ID and your Secret Access
Key, and then create a new keychain to store these - you will be prompted to
set a passphrase for this new keychain. Once done you should see the message:

```
Added credentials to profile "gds-users" in vault
```

You can find your `aws_access_key_id` and `aws_secret_access_key` by logging into the GDS base account using the [AWS console][].

AWS has more instructions for [creating, modifying, and viewing Access Keys (Console)][] to do this.

#### Setup cross-account profile access

For each role you need to assume, add a section like the following to `~/.aws/config`:

```
[profile target-account]
source_profile = gds-users
region = eu-west-1
role_arn = <role arn>
mfa_serial = <mfa device arn>
```

`<role arn>` - is from your team's list of accounts and roles, for example [GOV.UK](https://github.com/alphagov/govuk-aws-data/blob/master/docs/govuk-aws-accounts.md),
[Verify](https://github.com/alphagov/verify-blackbox-passwords/blob/master/aws-accounts.info),
and [Digital Marketplace](https://alphagov.github.io/digitalmarketplace-manual/aws-accounts.html#available-roles).

`<mfa device arn>` - is your user's assigned [Multi-Factor Authentication (MFA)][] device.

<details>
  <summary><small>How to find your MFA device ARN</small></summary>
  <li>sign into the GDS base account using the AWS console</li>
  <li>navigate to **IAM** > **Users** > `$your-user`</li>
  <li>select the **Security credentials** tab</li>
  <li>look for the **Assigned MFA device**</li>
  </ul>
</details>

Once this is all in place, you should be able to run `aws-vault list` and see output something like:

```
Profile                  Credentials              Sessions
=======                  ===========              ========
gds-users                gds-users                -
target-account           gds-users                -
```

#### Use aws-vault to invoke commandline tools

You can now use `aws-vault exec` to invoke the AWS CLI or other tools (eg [Terraform][]) as follows:

```
aws-vault exec target-account -- aws sts get-caller-identity
```

aws-vault will prompt you for the passphrase that you set above to unlock the
store (if it hasn't been accessed recently), and then prompt for your MFA
token.

#### Optional: add some shortcuts to your shell config

If you find the above `aws-vault exec ...` invocation too verbose, you can add
something like the following to your shell startup script:

```
for profile in $(awk '/^\[profile / { gsub("]", "", $2); print $2 }' ~/.aws/config); do
  alias "aws-${profile}"="aws-vault exec ${profile} --"
done
```

This sets up an alias for each profile in your `~/.aws/config` so that you can
then invoke AWS commands as follows:

```
aws-target-account aws sts get-caller-identity
aws-target-account terraform plan
etc...
```

[Amazon Web Services (AWS)]: https://aws.amazon.com/
[process called assuming roles]: https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-console.html
[request an AWS account form]: https://gds-request-an-aws-account.cloudapps.digital/
[AWS Command Line Interface (AWS CLI)]: https://aws.amazon.com/cli/
[AWS Shell]: https://github.com/awslabs/aws-shell
[Terraform]: https://www.terraform.io/
[AWS console]: https://gds-users.signin.aws.amazon.com/console
[creating, modifying, and viewing Access Keys (Console)]: https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html#Using_CreateAccessKey
[Multi-Factor Authentication (MFA)]: https://aws.amazon.com/iam/details/mfa/
[Terraform]: https://www.terraform.io/
[AWS Security Token Service (STS)]: https://docs.aws.amazon.com/STS/latest/APIReference/Welcome.html
[AWS CLI]: https://aws.amazon.com/cli/
[AWS CLI documentation]: https://docs.aws.amazon.com/cli/latest/reference/sts/assume-role.html
[Creating an AWS credentials file]: #creating-an-aws-credentials-file
[aws-vault]: https://github.com/99designs/aws-vault#readme
[maximum session duration]: https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use.html#id_roles_use_view-role-max-session
[team specific roles]: https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-console.html
