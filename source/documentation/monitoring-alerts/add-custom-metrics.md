## Set up custom metrics

Using the Prometheus client libraries, both [Java](https://github.com/alphagov/gds_metrics_dropwizard) and [Ruby](https://github.com/prometheus/client_ruby/) versions, we are allowed to create our own custom metrics to measure things specific to our applications in addition to the generic metrics offered by the libraries. The libraries include the Prometheus `simpleclient` offering four metric types.

### Counter

A counter is a cumulative metric that represents a single numerical value that only ever goes up. A counter is typically used to count requests served, tasks completed, errors occurred, etc. Counters should not be used to expose current counts of items whose number can also go down, e.g. the number of currently running threads. Use gauges for this use case.

### Gauge

A gauge is a metric that represents a single numerical value that can arbitrarily go up and down.

Gauges are typically used for measured values like temperatures or current memory usage, but also "counts" that can go up and down, like the number of running threads.

### Histogram

A histogram samples observations (usually things like request durations or response sizes) and counts them in configurable buckets. It also provides a sum of all observed values.

A histogram with a base metric name of `<basename>` exposes multiple time series during a scrape:

* cumulative counters for the observation buckets, exposed as `<basename>_bucket{le="<upper inclusive bound>"}`
* the total sum of all observed values, exposed as `<basename>_sum`
* the count of events that have been observed, exposed as `<basename>_count (identical to <basename>_bucket{le="+Inf"} above)`

### Summary

Similar to a histogram, a summary samples observations (usually things like request durations and response sizes). While it also provides a total count of observations and a sum of all observed values, it calculates configurable quantiles over a sliding time window.

A summary with a base metric name of `<basename>` exposes multiple time series during a scrape:

* streaming **φ-quantiles** (0 ≤ φ ≤ 1) of observed events, exposed as `<basename>{quantile="<φ>"}`
* the total sum of all observed values, exposed as `<basename>_sum`
* the count of events that have been observed, exposed as `<basename>_count`

### How to add custom metrics

  More detailed explanations on the different types of metrics can be found in the [Prometheus documentation](https://prometheus.io/docs/concepts/metric_types/).

  Instructions on how to add new metrics using the Java implementation can be found [in the library documentation](https://github.com/prometheus/client_java#instrumenting). Ruby users can find similar documentation in the [client_ruby documentation](https://github.com/prometheus/client_ruby#metrics).
