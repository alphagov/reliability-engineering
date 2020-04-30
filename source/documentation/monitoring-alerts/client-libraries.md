## Expose app level metrics using client libraries

Reliability Engineering provide client libraries which wrap Prometheus's own libraries to:

- provide an easy metrics choice for GDS teams
- supply consistent metrics and naming across different runtimes
- solve problems like, how to get metrics from all worker processes not just one
- guard the `/metrics` API behind HTTP basic auth for GOV.UK PaaS apps
- ease configuration by using framework-specific things such as [Railties](https://github.com/rails/rails/tree/master/railties) or [Dropwizard](https://www.dropwizard.io/1.3.2/docs/) bundles

You can setup GDS metrics for your GOV.UK PaaS app using the Ruby, Python and Java Dropwizard guides on GitHub to instrument:

- [Ruby](https://github.com/prometheus/client_ruby/) in Ruby on Rails or Rack apps
- [Java with Dropwizard](https://github.com/alphagov/gds_metrics_dropwizard)  in Dropwizard based apps
- [Python](https://github.com/alphagov/gds_metrics_python) in Python/Flask based apps
