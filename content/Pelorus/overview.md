---
title: "Overview"
date: 2022-07-08T14:34:05-06:00
draft: false
---
Pelorus helps IT organizations measure their impact on the overall performance of their organization by gathering team and organizational behaviors metrics over time. It focuses on key areas of IT that have been shown to impact the value they deliver to the organization as a whole. Some of the key outcomes Pelorus can focus on are:

* Software Delivery Performance
* Product Quality and Sustainability
* Customer experience

For more information see the [Pelorus documentation site](https://pelorus.readthedocs.io/en/latest/).

## Pelorus architecture
The following diagram shows the basic architecture, components, and traffic flows in the Pelorus ecosystem.

![](/Pelorus/PelorusArchComp.png)

Pelorus is composed of the following open source components:
* Prometheus Operator
* Prometheus
* Grafana Operator
* Grafana
* Thanos (backed by Object Store)
* Pelorus Exporters
* Commit Time
* Deploy Time
* Failure

### Prometheus and Grafana

Prometheus monitors and stores time-series data and Grafana provides dashboard visualization of the metrics.  Pelorus is built on these open source tools and focuses on the core differentiators, methods of gathering them, and building information radiators from those metrics.

#### Thanos
Thanos is a sub-set of Prometheus components that provide high availability and long term data storage. Thanos gives Pelorus dashboards the ability to look back over months or years of organizational data.

### Pelorus exporters
Exporters are Prometheus bot that gathers and exposes data. Pelorus uses the exporter framework to build integrations with common IT systems to gather the relevant dashboard data.

## Multi-cluster architecture (production)
Pelorus typically needs to be installed across multiple Kubernetes clusters in production environments. In most cases, the key clusters are the Development cluster where builds are happening, and Production.

Below is an example of Pelorus Multi-Cluster architecture.

![](/Pelorus/PelorusMultiCluster.png)

[Source](https://github.com/konveyor/konveyor.github.io/blob/main/content/Pelorus/overview.md)
