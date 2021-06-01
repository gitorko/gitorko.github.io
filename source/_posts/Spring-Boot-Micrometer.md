---
title: Spring Boot Micrometer
date: 2019-04-08 00:00:00
tags: 
- prometheus
- jmx
- micrometer
- grafana
categories:
- [MicroMeter]
- [Grafana]
- [Prometheus]
---

A Spring boot application integration with micrometer. Micrometer provides vendor neutral application metrics facade that can integrate with various monitoring systems like Prometheus, Wavefront, Atlas, Datadog, Graphite, Ganglia, Influx, JMX etc. We will look at integrations with prometheus and grafana in this example.

Github: [https://github.com/gitorko/project68](https://github.com/gitorko/project68)

<!-- more -->

<!-- toc -->

## Micrometer

Traditional systems which monitored JMX attributes could only do so at a particular instance of time. With the arrival of time series database we can now use that data and visualize it over a period in time. Writing the integration to various monitoring systems is time consuming, hence micrometer and spring simplyfy it. Underlying metrics are exposed by Spring Boot Actuator and then Micrometer provides a facade that can be used to either push or pull metrics to monitoring systems.

Every meter has a name (hierarchical) and tag. There are 4 main types of meters.
Timers  - Time taken to run something.
Counter - Number of time something was run.
Guages - Report data when observed. Gauges can be useful when monitoring stats of cache, collections
Distribution summary - Distribution of events.

MeterRegistryCustomizer, you can customize the whole set of registries at once or individual implementation.

## Prometheus

To start the prometheus docker instance run the command.

```bash
docker run --name pormetheus -d -p 9090:9090 -v c:\\project68\\prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus
```

Ensure you change the target to an acutall IP address and not use localhost when using docker container.

{% ghcode https://github.com/gitorko/project68/blob/master/prometheus.yml %}

Prometheus [http://localhost:9090](http://localhost:9090)

## Spring boot

{% ghcode https://github.com/gitorko/project68/blob/master/src/main/java/com/demo/project68/Application.java %}

{% ghcode https://github.com/gitorko/project68/blob/master/src/main/resources/application.yaml %}

Run the project

```bash
./gradlew bootRun
```

Hit the rest endpoint couple of times.
[http://localhost:8080/api/hello](http://localhost:8080/api/hello)

Ensure that your custom metrics are populated in 
[http://localhost:8080/actuator/prometheus](http://localhost:8080/actuator/prometheus)

You should see metrics similar to 

```json
hello_api_count_total{application="myapp",type="order",} 27.0
hello_api_time_seconds_count{application="myapp",exception="None",method="GET",outcome="SUCCESS",status="200",uri="/api/hello",} 27.0
hello_api_time_seconds_sum{application="myapp",exception="None",method="GET",outcome="SUCCESS",status="200",uri="/api/hello",} 102.162818601
hello_api_time_seconds_max{application="myapp",exception="None",method="GET",outcome="SUCCESS",status="200",uri="/api/hello",} 0.0
```

Prometheus should now start pulling data from the spring application. Click on status -> targets on prometheus dashboard to confirm that endpoint is up.

{% asset_img image01.JPG %}

Query the metric hello_api_count_total and view as graph

{% asset_img image02.JPG %}

## Grafana

The dashboard in promethues are minimal, to add more complex dashboard and visualization you can look at Grafana. To start the grafana docker instance run the command.

```bash
docker run --name grafana -d -p 3000:3000 grafana/grafana
```
[http://localhost:3000/](http://localhost:3000/)

Login as admin/admin

Add the prometheus data source

{% asset_img image03.JPG %}

Import a dashboard, Downlaod the json file for micrometer dashboard and import it in grafana and open the dashboard.

[https://grafana.com/dashboards/4701](https://grafana.com/dashboards/4701)

{% asset_img image04.JPG %}

{% asset_img image05.JPG %}

Create a dashboard on the 'hello_api_count_total' metric

{% asset_img image06.JPG %}

## References
Micrometer Reference Document : [https://micrometer.io/docs](https://micrometer.io/docs)
Prometheus : [https://prometheus.io/](https://prometheus.io/)
