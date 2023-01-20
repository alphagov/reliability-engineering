## Request AWS user access

People joining GDS do not automatically have access to [Amazon Web Services
(AWS)][]. To grant a user access to AWS you’ll need to add them to the
`gds-users` base account. Once the user has an IAM user in the `gds-users`
account they can assume roles in team specific accounts using [Amazon's
cross-account access pattern][].

### Requesting a new AWS user

To add a user to the `gds-users` base account, complete the "Request user access"
section of the [request an AWS account form][]. The user can request this
themselves or someone else can request it on their behalf.

Once the request is submitted it will be reviewed by members of the [AWS user
management codeowners team][]. If accepted the request will be merged and a
new IAM user will be added to the `gds-users` account.

The user will then receive an automated email containing instructions on how to
log in and set their credentials.

### Signing in to your `gds-users` user for the first time

The automated welcome email will contain instructions on how to access your new
AWS user. It also provides an initial password that you will need to change the
first time you sign in. You must also add an MFA device to your AWS user.

Start by signing in to [the AWS console][].

You will be prompted to change your password after you first sign in. Please
note __the new password must be at least 16 characters long__. If your choose a
password shorter than 16 characters you will get an error saying you don’t have
permission to change password.

### Adding an MFA device

You must enable Multi-factor Authentication (MFA) on your `gds-users` user,
otherwise you will not be able to assume roles in other AWS accounts.

You may add up to 8 MFA devices. However, note that __MFA device names must be
prefixed with your IAM username (usually your email addres)__, otherwise you
will receive a permissions error.

You can add MFA devices by clicking your username in the top right corner and
selecting "Security credentials".

![Top right menu image](../images/request-access-to-aws/top-right-menu.png)

On the "My security credentials" page scroll down to the "Multi-factor
authentication (MFA)" section and click "Assign MFA device".

The device name _must_ be prefixed with your IAM username, which is usually your
email address. If you do not do this you will receive a permissions error.

You will usually want to add the "Authenticator app" device type for things like
Google Authenticator and Yubikeys, but you may want to choose "Security Key" if
you are adding a FIDO device.

Click Next.

![Assign MFA device image](../images/request-access-to-aws/assign-mfa-device.png)

On the "Set up device" page you can choose to either display the QR code for
apps like Google Authenticator or "Show secret key" if you are using something
like a Yubikey with ykman.

Once you have added the code to your MFA device or app you will need to input
two consecutive one-time codes from your device and click "Add MFA".

![Set up MFA device image](../images/request-access-to-aws/set-up-mfa-device.png)

If you have done this correctly you should now see a device listed in the MFA
section of the "My security credentials" page.

![MFA devices image](../images/request-access-to-aws/mfa-devices.png)

You should test that you can sign out and back in again successfully, and that
you are prompted for an MFA code.

Further guidance on adding MFA can be found [here](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa_enable_virtual.html).

[Amazon Web Services (AWS)]: https://aws.amazon.com/
[Amazon's cross-account access pattern]: https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_cross-account-with-roles.html
[request an AWS account form]: https://gds-request-an-aws-account.cloudapps.digital
[AWS user management codeowners team]: https://github.com/orgs/alphagov/teams/aws-user-management-account-users-codeowners
[the AWS Console]: https://gds-users.signin.aws.amazon.com/console
