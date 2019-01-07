## Expose Cloud Foundry metrics using paas-prometheus-exporter

[Cloud Foundry][] provides metrics, for your PaaS apps.

Currently supported metrics include:

* CPU
* RAM
* disk usage data
* app crashes
* app requests
* app response times
* backing service metrics

### Set up the paas-prometheus-exporter

Before setting up the metrics exporter app, you'll need a live [Cloud Foundry account][] assigned to the spaces you want to receive metrics on.

Your new account should be separate to your primary Cloud Foundry account and use the [`SpaceAuditor`][] role beause it can view app data without modifying it.

To set up the metrics exporter app:

1. Clone the [paas-prometheus-exporter][] GitHub repository.
2. [Push the metrics exporter app][] to Cloud Foundry (without starting the app) by running
 * `cf push -f manifest.yml --no-start <app-name>`
3. Set the following mandatory environment variables in the metrics exporter app using
  * `cf set-env <app-name> NAME VALUE`

	Use the `cf set-env` command for these mandatory variables, this will keep secure the secret information contained in them.

	|Name|Value|
	|:---|:---|
	|`API_ENDPOINT`|Use `https://api.cloud.service.gov.uk`|
	|`USERNAME`|Cloud Foundry user|
	|`PASSWORD`|Cloud Foundry password|

	You could also set environment variables by amending the manifest file for optional environment variables that do not contain secret information. Read [paas-prometheus-exporter][] GitHub repository for more information.

4. Run `cf start <app-name>` to start your app
5. Check you're generating Prometheus metrics at the metrics endpoint
    - `https://<app-name>.cloudapps.digital/metrics`


[Cloud Foundry account]: https://docs.cloud.service.gov.uk/get_started.html
[Cloud Foundry]: https://docs.cloudfoundry.org/concepts/overview.html
[`SpaceAuditor`]: https://docs.cloud.service.gov.uk/orgs_spaces_users.html#space-auditor
[Push the metrics exporter app]: https://docs.cloud.service.gov.uk/deploying_apps.html
