## Create and edit alerts using Prometheus

When deciding what alerts you want to receive, consider:

- reading the [Alerting principles][6] in the GDS Way
- looking at [existing alerting rules][2] used by other GDS teams
- talking to us for advice using the [#re-prometheus-support][7] Slack channel

### How to create or update alerting rules

You should first read [Prometheus' alerting rules documentation][4] to understand how alerting works.

You will also need to understand how to write an expression in [PromQL][5] for your alerting rules.

### Finding your metrics

Prometheus contains metrics related to other teams which may not be relevant to you.

To see your team's available metrics, run the following queries to return a list of metric names available for your PaaS organisation or metric exporter.

`sum by(\_\_name\_\_) ({org="<<ORG_NAME>>"})` for example `sum by(\_\_name\_\_) ({org="govuk-notify"})`

`sum by(\_\_name\_\_) ({job="<<EXPORTER_APP_NAME>>"})` for example `sum by(\_\_name\_\_) ({job="notify-paas-postgres-exporter"})`

It's not currently possible to order these results alphabetically.

### Writing your alerting rule PromQL expression

Use the [Prometheus dashboard][1] to experiment writing your alert as a [PromQL][5] expression.

An example PromQL expression is:

```
rate(requests{org="gds-tech-ops", job="observe-metric-exporter", status_range="5xx"}[5m])
```
The above query means "amount of requests with status 5xx within the last 5 minutes for org `gds-tech-ops` and job `observe-metrics-exporter`.  

To make it into an alert (that is, something that triggers if the data values are higher or lower than expected), the expression requires a threshold to be compared against:

```
rate(requests{org="gds-tech-ops", job="observe-metric-exporter", status_range="5xx"}[5m]) > 1
```

Your expression should contain an `org` label, which refers to your PaaS organisation. This ensures you only use the metrics from your team. Although the `job` label may serve the same purpose, it is not guaranteed to be unique to your team.

You should only include timeseries for the PaaS space you wish to alert on, for example only including production using the `space="production"` label.

### Decide your alerting thresholds

Queries need thresholds added to them to make them into alerts. You can work out an alert's threshold value from historical data. To do this, use your current monitoring system's thresholds, averages and spikes for each alert.

For new alerts, experiment with different thresholds until you find one that fits your:

- chosen type of alert
- alerting priorities
- metric's patterns
- Service Level Objective

### Create the alerting rule

Alerting rules are defined in YAML format in a config file in the [prometheus-aws-configuration-beta][2] repository. Each product team should use their own file for their alerting rules.

Alerting rules should be prefixed with your team name, for example `registers_RequestsExcess5xx` or `DGU_HighDiskUsage`. This makes your alert easier to identify.

You must add a `product` label to your alerting rule under `labels` so if the alert is triggered, Prometheus will alert the correct team.

You may have to iterate your alerting rules to make them more useful for your team. For example, you may get alerts that do not require any action as the threshold is too low (false positives). 

For more information about creating alerts, see the [prometheus-aws-configuration-beta README][13] for an explanation of each field's meaning and an example alert you can customise.

### Create a PR with your alerting rule

Create a pull request for changes to your alerting rules file. Your commit should explain what the alert is and why you picked the threshold value. This is so future team members have the context they need to confidently change the alerting rule and other teams can learn from your alerting rules.

Share your pull request in the [#re-prometheus-support Slack channel][7] channel so we can review it. We will try to merge and deploy your pull request as quickly as possible and will let you know when your alerting rule is live.

### How to receive alerts

Once Prometheus triggers an alert, it sends the alert to [Alertmanager][10]. Alertmanager is then responsible for forwarding alerts to receivers such as [Pagerduty][11] or [Zendesk][12].

Alerts are forwarded to the appropriate team and receiver using the [Alertmanager config file][9] which uses the alert labels to direct the alert to the right team and receiver.

If you have not yet set up a receiver or would like to set up additional receivers use the [#re-prometheus-support Slack channel][7].


[0]: https://prometheus.io/
[1]: https://prom-2.monitoring.gds-reliability.engineering
[2]: https://github.com/alphagov/prometheus-aws-configuration-beta/tree/master/terraform/modules/app-ecs-services/config/alerts
[3]: https://github.com/alphagov/prometheus-aws-configuration-beta/
[4]: https://prometheus.io/docs/prometheus/latest/configuration/alerting_rules/
[5]: https://prometheus.io/docs/prometheus/latest/querying/basics/
[6]: https://gds-way.cloudapps.digital/standards/alerting.html#alerting
[7]: https://gds.slack.com/messages/re-prometheus-support
[8]: https://github.com/alphagov/paas-metric-exporter
[9]: https://github.com/alphagov/prometheus-aws-configuration-beta/blob/master/terraform/modules/app-ecs-services/templates/alertmanager.tpl
[10]: https://prometheus.io/docs/alerting/alertmanager/
[11]: https://www.pagerduty.com/
[12]: https://www.zendesk.com/
[13]: https://github.com/alphagov/prometheus-aws-configuration-beta/blob/master/terraform/modules/app-ecs-services/config/alerts/README.md
