## Display create and edit dashboards using Grafana

Reliability Engineering provides [Grafana dashboards](https://grafana.com/grafana) for teams to view [Prometheus](https://prometheus.io/) scraped metrics. You can use our example dashboards or create your own based on your team’s needs.

Sign in with Grafana at [grafana-paas.cloudapps.digital](https://grafana-paas.cloudapps.digital) using your GDS Google account.

### Display dashboards

Team dashboards are organised into separate Grafana folders.

Select **Home** in the top left of the Grafana **Home Dashboard** to choose your team’s dashboard folder and view your dashboards.

#### Display using your team TV

You should use your TV’s Google Chromebit user to display your Grafana dashboard. The Google Chromebit user only has read access to Grafana.

You should not use your personal Google account to display your Grafana dashboard on your team’s TV. Your personal Google account may have editing or admin permissions.

### Create and edit dashboards

Contact Reliability Engineering using the [#re-prometheus-support Slack channel](https://gds.slack.com/messages/CAF5H4N4Q/#) to request:

* admin permissions to create or edit a dashboard in your team folder
* a new team folder and the admin rights to manage it
* copies of our example dashboards to customise

### Use our example dashboards

You can use the default and template dashboards in the **General** folder of the Grafana **Home Dashboard**. For example, the GDS Application Metrics Default Dashboard and the GDS Container Metrics Default Dashboard.

You can use these dashboards to get started customising your own dashboards, or if you want to check your monitoring works as you expect.

#### GDS Application Metrics Default Dashboard

The [GDS Application Metrics Default Dashboard](https://grafana-paas.cloudapps.digital/d/000000011/gds-default-dashboard?orgId=1) displays application metrics produced by [Ruby](https://github.com/alphagov/gds_metrics_ruby), [Java with Dropwizard](https://github.com/alphagov/gds_metrics_dropwizard) and [Python](https://github.com/alphagov/gds_metrics_python) clients.

If you’ve configured your application with one of these libraries you can view its metrics. You can do this by selecting your application from the **Available Apps** dropdown.

If the dropdown does not include your application, check the instructions for [setting up metrics](monitoring-alerts.html#bind-your-exporter-to-prometheus) and make sure any changes are deployed to [GOV.UK Platform as a Service (PaaS)](https://docs.cloud.service.gov.uk/).

#### GDS Container Metrics Default Dashboard

The [GDS Container Metrics Default Dashboard](https://grafana-paas.cloudapps.digital/d/E2dUvczmz/container-metrics?orgId=1) displays container metrics produced by the [paas-metric-exporter](https://github.com/alphagov/paas-metric-exporter). Select your application from the **App** dropdown to view metrics for your application when you run the paas-metric-exporter.

If the dropdown down does not include your application, check the instructions for [setting up the PaaS metric exporter app with Prometheus](monitoring-alerts.html#bind-your-exporter-to-prometheus) and make sure any changes are deployed to [GOV.UK Platform as a Service (PaaS)](https://docs.cloud.service.gov.uk/).

#### Official and community built dashboards

You can import [official and community built Grafana dashboards](https://grafana.com/dashboards). For example, you could display backing service metrics such as [ElasticSearch](https://grafana.com/dashboards/266) or [PostgreSQL](https://grafana.com/dashboards/455).

Learn [how to import dashboards](http://docs.grafana.org/reference/export_import/#importing-a-dashboard).

#### Troubleshooting

Grafana is configured to point to one of our Prometheus instances by default. There are three, and unless all three go down no one will be paged out of hours. In a case where one or two prometheus instances are down but Grafana is up and you'd like to view your dashboards, you can change the data source of your panels to "Prometheus beta 2" or "Prometheus beta 3". The default is also known as just "Prometheus beta".