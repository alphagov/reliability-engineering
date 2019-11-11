## Tenancy model

The Reliability Engineering team's Concourse is multi-tenanted:

- Concourse is configured with many teams

- Each teams workloads run on separate infrastructure

- Users in one team cannot interact with the pipelines of another team
  - (unless the user is a member of both teams)

By default, all Concourse workers share the same egress IP addresses, unless configured otherwise.
If your team needs separate egress IP addresses, then please ask Reliability Engineering to make the configuration changes.

The Reliability Engineering team's Concourse is managed as follows:

- Concourse is configured to delegate secrets management to AWS Systems Manager Parameter Store

- Concourse worker instances are recreated every morning to ensure they have the latest security patches

- Users authenticate using GitHub SSO
  - (if you need Google SSO instead of GitHub, ask the Reliability Engineering team)

- Concourse is accessible from the GDS network (Whitechapel and the VPN)
