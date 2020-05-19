## Expose app level metrics using client libraries

Libraries exist to aid in exposing metrics for Prometheus to scrape in a number
of different runtimes:

- [Ruby](https://github.com/prometheus/client_ruby/)
- [Java](https://github.com/prometheus/client_java)
- [Python](https://github.com/prometheus/client_python)

Links to those and other client runtimes are given in the [Prometheus
documentation](https://prometheus.io/docs/instrumenting/clientlibs/).

**Note**: publishing metrics may lead to the leakage of secure information so you
may want to consider protecting the metrics path (usually `/metrics` by
convention) with IP safelisting or HTTP basic authentication.
