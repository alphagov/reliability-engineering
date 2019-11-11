## Roles

Concourse has 5 roles, which are explained in depth
[in the documentation](https://concourse-ci.org/user-roles.html).

When the Reliability Engineering team configure your Concourse team, you should
decide which Concourse team role should be configured. The role you choose will
be allocated to GitHub teams, so that anyone in the GitHub team is given that
role.

The roles you should choose between are usually:

- member
- pipeline-operator

Members can manipulate pipelines and update the configuration of pipelines from
the command line. This role should be used in most cases.

Pipeline-operators can only interact with pipelines, but cannot update their
configuration. This role should be used where a strict 2-pairs-of-eyes code
review policy is enforced.
