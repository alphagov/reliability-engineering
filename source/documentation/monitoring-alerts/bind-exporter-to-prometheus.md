## Bind your exporter to Prometheus

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

#### When using zero downtime plugins or a `blue-green` deployment process

#### IP Whitelist your applications metrics endpoint

If you're using a [blue-green deployment process][] with a zero downtime plugin such as [autopilot][] you should disable the basic auth on the metrics endpoint when using the [Ruby gem][] or [Python library][] and instead protect the metrics endpoint using IP whitelisting in order to minimise gaps in metrics between deployments.

By using the `paas-ip-authentication-route-service` you will only allow traffic from GDS Prometheis and [GDS Office IPs][].

1. Map the route to the metrics path:

  - Update your `manifest.yml`:

```
      routes:
      ...
      - route: app-to-protect.cloudapps.digital/metrics
```

  - redeploy your app to map the route and path

2. Register the IP whitelist route service as a user-provided service in your PaaS space.

    `cf create-user-provided-service paas-ip-authentication-route-service -r https://paas-ip-authentication-route-service.cloudapps.digital`

3. Register the IP whitelist route service against the metrics path.

    `cf bind-route-service cloudapps.digital paas-ip-authentication-route-service --hostname app-to-protect --path metrics`

#### Update your Grafana panel to combine metrics for blue-green deployments

You should update your [Grafana][] panels to combine metrics from different deployment states, for example in order to show the number of healthy instances you can use regex:

    `sum(up{job=~"app-to-protect(-venerable)?"})`

- Note: if you are not using [autopilot][] you will have to substitute `-venerable` to whatever your zero downtime plugin is using during the app renaming.

### App route configuration

Whether or not you're using a custom domain the Prometheus service broker will only scrape your app's first route.

For example, some apps may have multiple routes especially if they use custom domains such as `custom.domain.gov.uk, an-interesting-app.cloudapps.digital`. Only the first route will be picked up.

If there are no routes to your app the Prometheus service will default the route to:

`<app-name>.cloudapps.digital`

[authorization header and other app headers]: https://docs.cloud.service.gov.uk/deploying_services/use_a_custom_domain/#forwarding-headers
[autopilot]: https://github.com/contraband/autopilot
[GOV.UK PaaS]: https://www.cloud.service.gov.uk/
[Grafana]: https://grafana-paas.cloudapps.digital
[Prometheus]: https://prometheus.io/
[#re-prometheus-support Slack channel]: https://gds.slack.com/messages/CAF5H4N4Q/
[`SpaceAuditor`]: https://docs.cloud.service.gov.uk/orgs_spaces_users.html#space-auditor
[update your app's manifest.yml]: https://docs.cloudfoundry.org/devguide/deploy-apps/manifest.html#services-block
[blue-green deployment process]: https://docs.cloudfoundry.org/devguide/deploy-apps/blue-green.html
[Ruby gem]: https://github.com/prometheus/client_ruby/
[Python library]: https://github.com/alphagov/gds_metrics_python#optional-configuration
[GDS office IPs]: https://sites.google.com/a/digital.cabinet-office.gov.uk/gds-internal-it/news/aviationhouse-sourceipaddresses
