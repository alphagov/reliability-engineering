## Monitoring

The Reliability Engineering team's Concourse is monitored using:

- [Prometheus](https://prom-1.monitoring.cd.gds-reliability.engineering) - collection of metrics
- [Grafana](https://grafana.monitoring.cd.gds-reliability.engineering) - alerting and dashboards
- Splunk - audit logs

Concourse emits metrics which can be queried to get metrics about pipelines.

For example, the following query shows how often the `internal-apps` pipeline is run over a 12 hour period.

<pre><code>sum
  without (instance, status) (
    increase(
      concourse_builds_finished{team="autom8", pipeline="internal-apps"}[12h]
    )
  )
</code></pre>
