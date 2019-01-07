## Add authorisation to your /metrics page

We suggest you do not make your /metrics page available to the public. There are two ways you can do this.

### 1. Automatic basic auth for GDS client libraries

The [Reliability Engineering Prometheus client libraries]() protect their endpoints with HTTP basic auth by default. You do not need to do anything to enable this behaviour.

However, if you are using a zero downtime deployment process (also known as blue-green deployment) for your app, in order to minimise gaps in metrics between deployments we suggest you configure the client library to disable the basic auth and instead IP whitelist your app.

### 2. IP whitelist your app

IP whitelist is a security feature often used for limiting and controlling access to an app to trusted users. It works by only allowing traffic through from a list of trusted IP addresses or IP ranges.

By using the `re-ip-whitelist-service` you will only allow traffic from GDS Prometheis and [GDS Office IPs][].

#### IP whitelist only the /metrics page of your app

If you are using the [Reliability Engineering Prometheus client libraries](), as opposed to a standalone exporter app like the [paas-prometheus-exporter](), then you will only want to protect the /metrics page and not all other routes so users can still access your service.

1. Register the IP whitelist route service as a user-provided service in your PaaS space.

    `cf create-user-provided-service re-ip-whitelist-service -r https://re-ip-whitelist-service.cloudapps.digital`


2. Update your `manifest.yml` to map a new route to the metrics path and then redeploy your app.

    ```
    routes:
    ...
    - route: app-to-protect.cloudapps.digital/metrics
    ```


3. Register the route service for routes you want to protect.

    `cf bind-route-service cloudapps.digital re-ip-whitelist-service --hostname <your paas app route> --path metrics`

    For example, `app-to-protect.cloudapps.digital` would be:

    `cf bind-route-service cloudapps.digital re-ip-whitelist-service --hostname app-to-protect --path metrics`


#### IP whitelist your entire exporter

If you are running a standalone exporter app such as the [paas-prometheus-exporter]() then

1. Register the IP whitelist route service as a user-provided service in your PaaS space.

    `cf create-user-provided-service re-ip-whitelist-service -r https://re-ip-whitelist-service.cloudapps.digital`

2. Register the route service for routes you want to protect.

    `cf bind-route-service cloudapps.digital re-ip-whitelist-service --hostname <your paas app route>`

    For example, `app-to-protect.cloudapps.digital` would be:

    `cf bind-route-service cloudapps.digital re-ip-whitelist-service --hostname app-to-protect`

[Reliability Engineering Prometheus client libraries]: #expose-app-level-metrics-using-client-libraries)
[GDS Office IPs]: https://sites.google.com/a/digital.cabinet-office.gov.uk/gds-internal-it/news/aviationhouse-sourceipaddresses
[paas-prometheus-exporter]: https://github.com/alphagov/paas-prometheus-exporter
