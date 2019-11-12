## Summary

We have a number of different tools in use across GDS for CI/CD purposes:
- Jenkins
- Concourse
- Travis
- Github Actions
- CircleCI

and possibly others.

We would ideally be moving towards a smaller set of approved tools which we
document in the GDS Way, where the current advice (2019/11/12) is:

> ...set up any tests to run in a public continuous integration environment
> using tools such as Travis CI, CircleCI or Jenkins

The benefits of reduced number of tools is:

- reduced toil managing user accounts and authorisation for each tool
- more consistent tooling and configuration
- consolidated billing

## Proposal

@tlwr @stephenharker to write comments from GitHub thread into proposal / outcome.
## Problem

Given the recent move towards using Concourse and the adoption in some places
of the new Github Actions, we are seeing a proliferation of tools rather than a
consolidation.

In Techops we want to be able to assess and procure a small set
of tools that have been approved by IA and have a commercial agreement in place
for the use across the whole of GDS.

Github Actions, in particular, we will lose access to shortly without an
Enterprise agreement in place with Github which will incur a significant
increase in costs. This is currently being assessed but it is by no means
definite that we will be able to use Github Actions going forward.

## Proposal

@tlwr @stephenharker to write comments from GitHub thread into proposal / outcome.
