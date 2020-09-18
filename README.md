# Dynatrace Google Cloud Integration

This is the home of `Dynatrace Google Cloud Integration` which provides the mechanism to pull [Google Cloud metrics](https://cloud.google.com/monitoring/api/metrics_gcp) into Dynatrace.
To help with function deployment you can use automation scripts available in this repo.
Maintaining its lifecycle places a burden on the operational team.


## Overview
`Dynatrace Google Cloud Integration` is a [Cloud Function](https://cloud.google.com/functions) written in Python that pulls metrics for configured services from [Cloud Monitoring API](https://cloud.google.com/monitoring/api/v3). Function execution is triggered by [Pub/Sub](https://cloud.google.com/pubsub) upon 1 minute schedule defined in [Cloud Scheduler](https://cloud.google.com/scheduler). Authentication token to query for metrics is retrived for the scope of [Service account](https://cloud.google.com/iam/docs/service-accounts) that is created during installation proccess. Once the timeseries are collected, the values are pushed to [Dynatrace Metrics API v2](https://www.dynatrace.com/support/help/dynatrace-api/environment-api/metric-v2/) using Dynatrace API token and URL to Dynatrace tenant environment stored in [Secret Manager](https://cloud.google.com/secret-manager).

In addition to metrics `Dynatrace Google Cloud Integration` is calling Service specific API's to get instance details, that are not reported as Resource Labels in [Cloud Monitoring API](https://cloud.google.com/monitoring/api/v3) .  Particulary the function try to retrieve endpoint addresses (FQDN's, IP addresses).

![Architecture](./img/architecture.svg)

## Supported Google Cloud services
| Google Cloud service                 | Metric pulling | Pre-defined dashboards | Pre-defined alerts |
| --------------------------  | ---- | ---- | ---- |
| Google Cloud APIs           |  Y   |  N   |  N   |
| Google Cloud Function       |  Y   |  N   |  N   |
| Google Cloud SQL            |  Y   |  N   |  N   | 
| Google Cloud Datastore      |  Y   |  N   |  N   |
| Google Cloud Filestore      |  Y   |  N   |  N   |
| Google Cloud VM Instance    |  Y   |  N   |  N   |
| Google Cloud Storage        |  Y   |  N   |  N   |
| Google Cloud Load Balancing |  Y   |  N   |  N   |
| Google Cloud Pub/Sub        |  Y   |  N   |  N   |

## Quick Start
You should deploy `Dynatrace Google Cloud Integration` into the project that will be monitored.


### Install with Google Cloud Shell

Run the `Dynatrace Google Cloud Integration` installation script
```
wget -O GITHUB_BUILD_URL ; chmod a+x setup.sh ; ./setup.sh
```

Installation script will prompt for following parameters:
| Parameter   | Description                                   |
| ----------- | --------------------------------------------- |
| GCP project | Google Cloud project, where `Dynatrace Google Cloud Integration` should be deployed to. By default, current project set for gcloud CLI. |
| Dynatrace tenant URI | The URL to Your Dynatrace SaaS or Managed environemnt |
| Dynatrace API token | Dynatrace API token. You can learn how to generate token [Dynatrace API - Tokens and authentication](https://www.dynatrace.com/support/help/dynatrace-api/basics/dynatrace-api-authentication) manual. Integration requires `Ingest metrics using API V2` Token permission.



### Install with Bash
Install [Google Cloud SDK](https://cloud.google.com/sdk/docs) following the instructions [Using the Google Cloud SDK installer](https://cloud.google.com/sdk/docs/downloads-interactive#linux)

Initialize Google Cloud Shell following the instructions [Initializing Cloud SDK](https://cloud.google.com/sdk/docs/initializing)

Run the `Dynatrace Google Cloud Integration` installation script
```
wget -O GITHUB_BUILD_URL ; chmod a+x setup.sh ; ./setup.sh
```

Installation script will prompt for following parameters:
| Parameter   | Description                                   |
| ----------- | --------------------------------------------- |
| GCP project | Google Cloud project, where `Dynatrace Google Cloud Integration` should be deployed to. By default, current project set for gcloud CLI. |
| Dynatrace tenant URI | The URL to Your Dynatrace SaaS or Managed environemnt |
| Dynatrace API token | Dynatrace API token. You can learn how to generate token [Dynatrace API - Tokens and authentication](https://www.dynatrace.com/support/help/dynatrace-api/basics/dynatrace-api-authentication) manual. Integration requires `Ingest metrics using API V2` Token permission.

### Monitoring GCP instances in Dynatrace

Installation script deploy Dashboards for all of supported services within provided Dynatrace tenant. You can browse them in Dynatrace UI.

Dashboads list:

![Dashboards list](./img/gcp_dashboards_list.png)

Sample dashboard for GCP API:

![Sample dashboard](./img/gcp_api_dashboard.png)

### Monitoring and troubleshooting `Dynatrace Google Cloud Integration`

`Dynatrace Google Cloud Integration` reports self-monitoring metrics as Google Metrics. This allow to track eventual problems with communication between the function and Dynatrace cluster. 

Self monitoring metrics:
| Metrics | Description |
| ------- | ----------- |
| MINT lines ingested | Amount of lines of metrics (data points) ingested into Dynatrace Metric INTerface for given interval. |
| Dynatrace connectivity | Status (1 -ok) indicating the connectivity between monitoring function and Dynatrace cluster. Connectivity can be broken due to wrong Dynatrace URL provided, wrong API token or network connectivity issues |
| Dynatrace failed requests count | Amount of requests that were rejected by Dynatrace Metric INTerface. The reason for failure can be unexpected data point value or reaching request quota for metric ingest |
| Dynatrace requests count | Amount of requests that were send to Dynatrace cluster.  |

Installation script deploy `dynatrace-gcp-function Self Monitoring` dashboard upon installation process to help tracking health of the solution.

![Self monitoring](./img/self_monitoring.png)


## Contributing

See [CONTRIBUTING](CONTRIBUTING.md) for details on submitting changes.

## License

`Dynatrace Google Cloud Integration` is under Apache 2.0 license. See [LICENSE](LICENSE) for details.