## Workers

Each Concourse team has 1 or more worker nodes assigned to it, of potentially different instance types.

Sometimes a worker node will begin to exhibit strange behaviour and cause some pipelines to cease functioning properly. Without needing to involve the Automate team, you may find you are able to get rid of the worker by going to your team's `info` pipeline and running `start-worker-refresh`. This should launch a job in the background for AWS to replace the worker node, though it does rely on some basic level of functionality from the existing workers and may not do the trick in all cases. Note that there may be disruption to your team's running tasks while this is ongoing.
Future work may allow us to provide a more reliable way to replace your team's worker nodes.
