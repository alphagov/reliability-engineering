## Teams

The Reliability Engineering team configured Concourse so that the workloads of
different teams run on different infrastructure. This design decision ensures
that:

- the time taken to schedule and run workloads stays predictable
- the workloads of each team are isolated to separate pools of resources

Teams do not have to represent actual organisational units, and can instead be
used for fine-grained control over permissions.

For example: a service team could have two teams:

- `service-team-dev`
- `service-team-deploy`

Where:

- `service-team-dev` is for pull-requests and management of development environments
- `service-team-deploy` is for deploying trusted code to production

Where:

- `service-team-dev` includes all service team members
- `service-team-deploy` includes only team members who are trusted to deploy code
