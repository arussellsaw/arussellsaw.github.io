---
layout: post
title: Telemetry
---

[repo](http://github.com/arussellsaw/telemetry)

*Metrics for your Go applications*

I've been working on a [caching proxy](http://github.com/arussellsaw/postcache)
in my free time, for caching [KairosDB](http://github.com/kairosdb/kairosdb)
when used with our [Grafana](http://github.com/grafana/grafana) dashboards. In
working on this project, i had a thought that it would be great if i could pull
our some metrics in order to track performance and usage of the proxy, and thanks
to Go's fantastic packaging model, i had the motivation to make a dedicated
library, so that my future projects, and hopefully others can have an easy workflow
for metrics and alerting.



Here's a little snippet that shows how easily you can pull out function timing
using telemetry.

----------

{% highlight go %}
package main

import(
    "time"
    "net/http"

    "github.com/arussellsaw/telemetry"
    )

func main() {
    tel, metricsHandler := telemetry.New((5 * time.Second))
    http.HandleFunc("/metrics", metricsHandler.ServeHTTP)
    go http.ListenAndServe(":80", nil)

    tel.Average.New("demo.timing", (60 * time.Second))

    start := time.Now()
    somethingYouWantToTime()
    tel.Average.Add("demo.timing", float32(time.Since(start).Nanoseconds()))
}

{% endhighlight %}

----------

The largest utility that telemetry provides is the time constrained metrics, it's
easy enough to echo out increasing counters, but telemetry has a built in metric
manager that culls old stats, this allows you to provide 'per minute' metrics,
meaning it's very easy to have metrics that work well in things like Grafana.


[![grafana-dashboard](https://i.imgur.com/KNpGgoO.jpg)](https://i.imgur.com/KNpGgoO.jpg)


Please email me or get in contact via twitter if you end up using the library, i'd
love to see what other people are doing with my work!
