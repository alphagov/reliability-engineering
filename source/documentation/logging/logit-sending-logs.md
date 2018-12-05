## Send logs to Logit

Logit offers logstash endpoints. In their default mode there is no authentication on them, this means anyone who has the logstash address could inject fake logs into your stack.

### Provision a new stack

After signing into Logit.io create a new stack by selecting **Add stack** from the Stacks section of the dashboard.

If you do not see the **Add stack** button, it means you do not have permission to add a new stack. The tech lead for your team should have permissions to add a new stack.

Contact Reliabilty Engineering using the  [#reliability-engineering](https://gds.slack.com/messages/reliability-engineering) Slack channel for help with permissions.

#### Stack name

When you choose a stack name, other teams will use this name to identify your stack from their own.

Your team will also need to differentiate between stacks they own. Your stack name should be specific, concise, show who owns it and which environment it serves.

For example:

* good - `GOV.UK Verify Hub Production (Green)`
* bad -  `New dev environment 2`

#### Stack version

In the **Stack version** field select the latest Elasticsearch, Logstash and Kibana (ELK) version from the displayed drop-down menu, unless you have specific reasons to prefer an earlier version.

Complete the **Daily logs** and **Retention fields** as appropriate then choose **continue**. Confirm your stack details are correct and select **Create Stack**.

Once created, return to the Logit.io Dashboard.

### Configure a new stack

#### Id and Stack API Key

An Id and Stack API Key will have been generated for your new stack. To view this information, choose **Settings** from the Logit.io Dashboard.

The Id and Stack API Key will both be UUIDs. We'll use the UUID below in our examples for the remainder of this manual.

`f046ca39-8388-4c49-a536-19f95a555905`

#### Logstash endpoint

The Logstash endpoint is formed from the stack ID. Our example stack's Logstash endpoint is `f046ca39-8388-4c49-a536-19f95a555905-ls.logit.io`.

Logstash speaks many different protocols and these are all assigned to sequential TCP ports which are visible from **Logstash Inputs** under the stack **settings** page.

TCP ports are assigned on a per-stack basis at stack creation time and will be different per stack.

## Send logs from PaaS to logit

You can [send logs from PaaS to logit](https://docs.cloud.service.gov.uk/monitoring_apps.html#set-up-the-logit-io-log-management-service)

## Ship logs using Amazon S3

In some cases such as logging for Amazon services (like, CloudTrail, VPC FlowLogs) it can make sense to use an S3 bucket as a log "source" for your Logit stack. This differs from other ways of using Logit as logs are "pulled" into your stack by a process in the Logit infrastructure.

### Share access to resources in your Amazon account ###

Amazon [recommend using IAM Roles](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_common-scenarios_third-party.html) to share access to resources in your account. This method doesn't require the creation of shared secrets.

Currently Logit don't support this, however they do plan to support it in the future. Right now the only way to grant them access is by issuing them access keys which can access the S3 bucket in question.

When setting this up you make sure that

  * The access key is only able to ListObjects and GetObject from the S3 bucket containing the logs
  * You are able to migrate to Role based access in the future when it is supported by Logit

### Configure Logit

Currently pulling logs from an S3 bucket requires a Logit support engineer to configure it for your stack. To start this process, add a "Source" to your Logit stack and select "S3". Complete the form and a Logit support engineer will contact you once it has been configured.

Logs pulled from S3 will be processed as usual by Logstash.

### Shut down unsecure inputs

Logit stacks have a non-restrictive security configuration by default. Each of the inputs listed will be behind a TCP port that is open to the internet at large, and will not authenticate connecting clients.

The only inputs we are interested in are **Beats-SSL** and **Syslog-SSL**, you will need to log a support request with Logit to close the TCP or UDP ports for all other protocols.

If you know in advance which IP addresses or address ranges you will be sourcing logging traffic from, include these in your request so that the **Beats-SSL** and **Syslog-SSL** ports can be whitelisted.

## Configure Filebeat

Filebeat is a Logstash client that reads log files from the filesystem and ships them to Logstash as they are being written to. Please refer to the [Filebeat documentation](https://www.elastic.co/products/beats/filebeat) for details on installation, general configuration and operation.

The following is a sample configuration fragment for shipping to Logit.  The port number in the `hosts` specification must be the TCP port assigned to the `Beats-SSL` input in your stack.

```yaml
output.logstash:
  enabled: true
  hosts:
    - f046ca39-8388-4c49-a536-19f95a555905-ls.logit.io:14303
  ssl:
    enabled: true
```

## Configure Rsyslog

Rsyslog version 8.x is known to work, we have not tried earlier versions. Although Ubuntu Trusty ships with 7.x, packages of 8.x for Trusty are available on the Rsyslog vendor's [PPA](https://launchpad.net/~adiscon/+archive/ubuntu/v8-stable). You must also install the optional `rsyslog-gnutls` package.

Then, load the `gtls` "stream driver" and configure it for operation with Logit:

```
$DefaultNetstreamDriver gtls
$ActionSendStreamDriverAuthMode x509/name
$ActionSendStreamDriverPermittedPeer f046ca39-8388-4c49-a536-19f95a555905-ls.logit.io
$ActionSendStreamDriverMode 1
```

Declare an action on matching messages that uses the `omfwd` module to forward them to Logit:

```
*.*;local2.none action (
  type="omfwd"
  target="f046ca39-8388-4c49-a536-19f95a555905-ls.logit.io"
  name="logstash"
  template="ForwardFormat"
  protocol="TCP"
  port="14305"
  streamdriver="gtls"
  streamdrivermode="1"
  queue.spoolDirectory="/var/spool/rsyslog"
  queue.filename="logstash.queue"
  queue.type="LinkedList"
  queue.saveOnShutdown="on"
  queue.timeOutEnqueue="1"
  queue.maxDiskSpace="1g"
  action.resumeRetryCount="-1"
)
```

Note the `streamdriver="gtls"` and `streamdrivermode="1"` are required here,
despite the earlier config fragment implying that the default behaviour has
been changed.

## Proxy patterns

Architectural constraints around egress security often mean that having each of your servers connect directly to Logit's endpoint is not an option.

This section covers some possible approaches to securely relaying Beats and Syslog traffic to Logit at your environment's boundary.

#### Proxy Beats-SSL (Lumberjack protocol)

The Lumberjack protocol is not HTTP based, and as such, you can't use an HTTP proxy. Filebeat does, however, support SOCKS negotiation.

In the following example, `10.1.0.0/24` is the private network CIDR range of the log-generating servers that you're proxying for, and `12345` is the TCP port of the Beats-SSL input on the `f046ca39-8388-4c49-a536-19f95a555905-ls.logit.io` Logstash endpoint.

Sample, minimal configuration for a Dante socks proxy:


```
logoutput: syslog

internal: eth0 port = 1080
external: eth0

method: none
clientmethod: none

user.privileged: proxy
user.notprivileged: nobody
user.libwrap: nobody

client pass {
  from: 10.1.0.0/24
  to: 10.1.0.0/24
  method: none
  log: connect disconnect error
}

client block {
  from: 0.0.0.0/0
  to: 0.0.0.0/0
  log: connect error
}

pass {
  from: 10.1.0.0/24 to: f046ca39-8388-4c49-a536-19f95a555905-ls.logit.io port = 12345
  protocol: tcp
  command: connect
  log: connect disconnect error
}
```

#### Proxy Syslog

This section is under review.

The Syslog protocol is designed to be relayed; Rsyslog fully supports this.

The configuration example in the above section will already do the egress-proxy-to-Logit part of the relay unmodified, and this should be applied to your egress proxy's rsyslogd config.

However, you may need some additional configuration to allow your egress proxy's rsyslogd to accept syslog messages from your internal hosts. For example:

```
$ModLoad imudp
$UDPServerAddress 0.0.0.0
$UDPServerRun 514
```
