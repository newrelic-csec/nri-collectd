[![Community Project header](https://github.com/newrelic/open-source-office/raw/master/examples/categories/images/Community_Project.png)](https://github.com/newrelic/open-source-office/blob/master/examples/categories/index.md#community-project)

# New Relic integration for CollectD

This New Relic integration monitors and reports metric data for configured CollectD devices.

## Requirements

The CollectD client must be running on the same host as the infra agent on which the `nr-collectd-plugin` is installed.

Configure the client to send metric out using the CollectD "network" plugin. Usually the collectd.conf file must be updated to add/uncomment this snippet.

```sh bash
    <Plugin network>
        Server "127.0.0.1"
    </Plugin>
```

At this time, the customization of the network plugin is not supported.

## Install

Follow these steps after you've cloned this repository:

1. Create a CollectD integration config file

    ```sh bash
    sudo cp collectd-plugin-config.yml.sample collectd-plugin-config.yml
    ```

1. Copy the integration files to the infrastructure agent integrations folder

    ```sh bash
    sudo cp nri-collectd /var/db/newrelic-infra/custom-integrations/
    sudo cp collectd-plugin-definition.yml /var/db/newrelic-infra/custom-integrations/
    sudo cp collectd-plugin-config.yml /etc/newrelic-infra/integrations.d/
    ```

1. Note down the port number used in the `/etc/collectd/collectd.conf` file network stanza. Use the same port number in the `collectd-plugin-config.yml` file.

1. Complete the configuration (see the Configuration section). To obtain an API key, see [Register an Insert API key](https://docs.newrelic.com/docs/insights/insights-data-sources/custom-data/introduction-event-api#register). 

1. Stop and restart the infrastructure agent.

    ```sh bash
    sudo systemctl stop newrelic-infra | sudo service newrelic-infra stop
    sudo systemctl start newrelic-infra | sudo service newrelic-infra start
    ```

1. Check if the `nri-collectd` integration is running.

    ```sh bash
    sudo ps -ef | grep nri-collectd
    ```

Data should start flowing into your New Relic account. See [Understand and use data from Infrastructure integrations](https://docs.newrelic.com/docs/integrations/infrastructure-integrations/get-started/understand-use-data-infrastructure-integrations).

## Configuration

* `port`: UDP port to listen for metrics on.
* `dim` (boolean): Reports output as [dimensional metrics](https://docs.newrelic.com/docs/data-ingest-apis/get-data-new-relic/metric-api/introduction-metric-api).
* `interval`: Interval to report dimensional metrics, formatted in golang [time.Duration](https://golang.org/pkg/time/#Duration). 
    > Only used if `dim` is set to `true`, otherwise the interval is set within `/var/db/newrelic-infra/custom-integrations/collectd-plugin-definition.yml`.
* `key`: Insert API Key. Only used if `dim` is set to `true`.
* `metric_api_url`: If `dim` is true, use this to set the [Metric API endpoint](https://docs.newrelic.com/docs/data-apis/ingest-apis/metric-api/report-metrics-metric-api/#api-endpoint). The default is to use the US endpoint.

## Sample CollectD configuration file

```sh bash
integration_name: com.newrelic.collectd-plugin

instances:
  - name: sample-collectd
    command: metrics
    arguments:
      port: 25826
      key: "Your Insights Inserts API Key"
      #interval: "30s"
      dim: true
    labels:
      role: collectd
```

## Compatibility

* Supported OS: Linux
* collectd-plugin versions: 1.0

## Building

Golang is required to build the integration. We recommend Golang 1.11 or higher.

After cloning this repository, go to the directory of the CollectD integration and build it:

```bash
go mod download
go mod tidy
go build

```

The command above executes the tests for the CollectD integration and builds an executable file called `nri-collectd` in the root directory. 

To start the integration, run `nri-collectd`:

```bash
$ ./nri-collectd
```

If you want to know more about usage of `./nri-collectd`, pass the `-help` parameter:

```bash
$ ./nri-collectd -help
```

External dependencies are managed through the [govendor tool](https://github.com/kardianos/govendor). Locking all external dependencies to a specific version (if possible) into the vendor directory is required.

## Testing

TBD

## Support

Should you need assistance with New Relic products, you are in good hands with several support diagnostic tools and support channels.

> This [troubleshooting framework](https://discuss.newrelic.com/t/troubleshooting-frameworks/108787) steps you through common troubleshooting questions.

> New Relic offers NRDiag, [a client-side diagnostic utility](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/troubleshooting/new-relic-diagnostics) that automatically detects common problems with New Relic agents. If NRDiag detects a problem, it suggests troubleshooting steps. NRDiag can also automatically attach troubleshooting data to a New Relic Support ticket.

If the issue has been confirmed as a bug or is a Feature request, please file a Github issue.

**Support Channels**

* [New Relic Documentation](https://docs.newrelic.com): Comprehensive guidance for using our platform
* [New Relic Community](https://discuss.newrelic.com): The best place to engage in troubleshooting questions
* [New Relic Developer](https://developer.newrelic.com/): Resources for building a custom observability applications
* [New Relic University](https://learn.newrelic.com/): A range of online training for New Relic users of every level

## Privacy

At New Relic we take your privacy and the security of your information seriously, and are committed to protecting your information. We must emphasize the importance of not sharing personal data in public forums, and ask all users to scrub logs and diagnostic information for sensitive information, whether personal, proprietary, or otherwise.

We define “Personal Data” as any information relating to an identified or identifiable individual, including, for example, your name, phone number, post code or zip code, Device ID, IP address and email address.

Review [New Relic’s General Data Privacy Notice](https://newrelic.com/termsandconditions/privacy) for more information.

## Contributing

We encourage your contributions to improve the CollectD integration! Keep in mind when you submit your pull request, you'll need to sign the CLA via the click-through using CLA-Assistant. You only have to sign the CLA one time per project.

If you have any questions, or to execute our corporate CLA, required if your contribution is on behalf of a company,  please drop us an email at opensource@newrelic.com.

**A note about vulnerabilities**

As noted in our [security policy](/SECURITY.md), New Relic is committed to the privacy and security of our customers and their data. We believe that providing coordinated disclosure by security researchers and engaging with the security community are important means to achieve our security goals.

If you believe you have found a security vulnerability in this project or any of New Relic's products or websites, we welcome and greatly appreciate you reporting it to New Relic through [HackerOne](https://hackerone.com/newrelic).

If you would like to contribute to this project, please review [these guidelines](./CONTRIBUTING.md).

To all contributors, we thank you!  Without your contribution, this project would not be what it is today.

## License
nri-collectd is licensed under the [Apache 2.0](http://apache.org/licenses/LICENSE-2.0.txt) License.
