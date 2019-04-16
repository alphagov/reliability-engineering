## Bootstrap GOV.UK AWS

Follow the instructions in https://reliability-engineering.cloudapps.digital/iaas.html to request a new AWS account.

The new account has a bootstrap role that you should be able to assume to start configuring the new environment.

### Setting up your work environment to assume AWS roles

There are several ways to do this

###### AWS-CLI

Make sure your laptop is configured with credentials to access the god-users account. If you can login in gds-users from the command line, you should be able to get the credentials of an assumed role with the following command:

```
aws sts assume-role \
  --role-session-name "$(whoami)-$(date +%d-%m-%y_%H-%M)" \
  --role-arn ${role_arn} \
  --serial-number arn:aws:iam::xxxxxxx:mfa/your.name@digital.cabinet-office.gov.uk \
  --duration-seconds 1800 \
  --token-code ${mfa_code}
```

###### AWS-VAULT

Download and install a copy of AWS Vault from the official repo:
https://github.com/99designs/aws-vault

Follow the instructions to set up your initial profile. A good tutorial that explains this step by step can be found here:
https://medium.com/devopslinks/step-by-step-aws-iam-assumerole-with-aws-vault-configuration-9d5986373c33

After following the tutorial you should have a ``` ~/.aws/config ``` file similar to the example below. You may have more or less profile entries depending on how many environments you have access to:
```
[profile readonly]
region=eu-west-1

[profile staging]
region=eu-west-1
source_profile = readonly
role_arn = arn:aws:iam::11111111111:role/staging
mfa_serial = arn:aws:iam::11111111111:mfa/your.name@email.provider

[profile production]
region=eu-west-2
source_profile = readonly
role_arn = arn:aws:iam::11111111111:role/production
mfa_serial = arn:aws:iam::11111111111:mfa/your.name@email.provider
```

After setting up aws-vault you can generate a token by running the following:
```
aws-vault exec environmentname -- env
```
For Example:
```
aws-vault exec staging -- env
```  
Some temporary credentials will be outputted to screen, copy the AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY and the AWS_SESSION_TOKEN

Create a new file (e.g. aws_temp_cred) somewhere on your local machine and paste the credentials into it, so it looks similar to the example below

```
export AWS_ACCESS_KEY_ID=YOURACCESSKEY
export AWS_SECRET_ACCESS_KEY=YOURSECRETKEY
export AWS_SESSION_TOKEN=YOURLONGSESSIONTOKEN
```
Now run the source command on the file to make the credentials available for Terraform, for example:

``` source ./aws_temp_cred ```

### Setting up Terraform work environment

Follow the instructions in govuk-aws [Getting Started](https://github.com/alphagov/govuk-aws/blob/master/doc/guides/getting-started.md) guide to start configuring and deploying resources in the AWS environment.

### Populate initial data for the new environment

In order to run the Terraform projects we will need valid data in the [govuk-aws-data](https://github.com/alphagov/govuk-aws-data) repository. Follow the instructions [Create new environment data](https://github.com/alphagov/govuk-aws-data/blob/master/docs/new-environment-data.md) in that repository to create the initial environment data.

### Provision the new environment

Follow the instructions in govuk-aws [Environment Provisioning](https://github.com/alphagov/govuk-aws/blob/master/doc/guides/environment-provisioning.md) to provision all the components of the environment.

