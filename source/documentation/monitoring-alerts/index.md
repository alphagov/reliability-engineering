# Metrics and Alerting

The Reliability Engineering team is running a beta service for GDS using [Prometheus](https://prometheus.io/) for operational metrics, and provides client libraries which wrap Prometheus's own libraries to:

- provide an easy metrics choice for GDS teams
- supply consistent metrics and naming across different runtimes
- solve problems like, how to get metrics from all worker processes not just one
- guard the `/metrics` API behind HTTP basic auth for GOV.UK PaaS apps
- ease configuration by using framework-specific things such as [Railties](https://github.com/rails/rails/tree/master/railties) or [Dropwizard](https://www.dropwizard.io/1.3.2/docs/) bundles

Contact us on the [#re-prometheus-support][] Slack channel to find out more.

[#re-prometheus-support]: https://gds.slack.com/messages/re-prometheus-support
