## Get your /metrics page scraped by Prometheus

[Prometheus][] uses service discovery to decide what it monitors, so for apps running on [GOV.UK PaaS][] you'll need to:

1. Grant Prometheus read-only access to your PaaS spaces.
2. Bind your apps to the Prometheus service.

### Grant Prometheus read-only access to your PaaS spaces

By giving the `prometheus-for-paas` user the [`SpaceAuditor`][] role you allow it to monitor each instance of your app and respond to events, for example start, stop, scaling.

`cf set-space-role prometheus-for-paas@digital.cabinet-office.gov.uk <org-name> <space-name> SpaceAuditor`

### Bind your apps to the Prometheus service

You can find Prometheus in the PaaS marketplace.

```
❯ cf marketplace
service          plans        description
gds-prometheus   prometheus   GDS internal Prometheus monitoring alpha https://reliability-engineering.cloudapps.digital/#metrics
```

If you're unable to see `gds-prometheus` in the output of `cf marketplace` please contact us through the [#re-prometheus-support Slack channel].

Create a Prometheus service within your PaaS space and allow it to bind to apps running there. Do this by following these steps:

1. Create a Prometheus service instance in each space you have Prometheus instrumented apps deployed
  - `cf create-service gds-prometheus prometheus <service-instance-name>`
2. Either [update your app's manifest.yml] to bind your new service or bind it using the CLI
  - `cf bind-service <app-name> <service-instance-name>`
3. If your app uses a custom domain, make sure the [authorization header and other app headers][] are being forwarded to the app, or the basic auth to the metrics endpoint will fail and your app won't have access to your other headers.
  - `cf update-service <cdn-route-service> \
    -c '{"domain": "<custom-domain>", "headers": ["Accept", "Authorization", "<other app header values>"]}'`

Within 10 minutes Prometheus will start scraping your application for metrics, you can validate this by checking [Grafana][].

### App route configuration

Whether or not you're using a custom domain the Prometheus service broker will only scrape your app's first route.

For example, some apps may have multiple routes especially if they use custom domains such as `custom.domain.gov.uk, an-interesting-app.cloudapps.digital`. Only the first route will be picked up.

If there are no routes to your app the Prometheus service will default the route to:

`<app-name>.cloudapps.digital`

[authorization header and other app headers]: https://docs.cloud.service.gov.uk/deploying_services.html#forwarding-headers
[GOV.UK PaaS]: https://www.cloud.service.gov.uk/
[Grafana]: https://grafana-paas.cloudapps.digital
[Prometheus]: https://prometheus.io/
[#re-prometheus-support Slack channel]: https://gds.slack.com/messages/CAF5H4N4Q/
[`SpaceAuditor`]: https://docs.cloud.service.gov.uk/orgs_spaces_users.html#space-auditor
[update your app's manifest.yml]: https://docs.cloudfoundry.org/devguide/deploy-apps/manifest.html#services-block
