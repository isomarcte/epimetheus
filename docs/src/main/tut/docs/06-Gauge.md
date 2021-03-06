---
layout: docs
number: 6
title: Gauge
---

# {{page.title}}

Gauge metric, to report instantaneous values.

A `Gauge` is a metric that represents a single numerical value that can arbitrarily go up and down.

Gauges are typically used for measured values like temperatures or current memory usage, but also "counts" that can go up and down, like the number of concurrent requests.

Labelled versions can be aggregated and processed together much more easily in the Prometheus
server than individual metrics for each labelset.

Imports

```tut:silent
import io.chrisdavenport.epimetheus._
import cats.effect._
import shapeless._
```

An Example of a Gauge with no labels:

```tut:book
val noLabelsGaugeExample = {
  for {
    cr <- CollectorRegistry.build[IO]
    gauge <- Gauge.noLabels(cr, Name("gauge_total"), "Example Gauge")
    _ <- gauge.inc
    _ <- gauge.inc
    _ <- gauge.dec
    currentMetrics <- cr.write004
  } yield currentMetrics
}

noLabelsGaugeExample.unsafeRunSync
```

An Example of a Gauge with labels:

```tut:book
val labelledGaugeExample = {
  for {
    cr <- CollectorRegistry.build[IO]
    gauge <- Gauge.labelled(
      cr,
      Name("gauge_total"),
      "Example Gauge",
      Sized(Name("foo")),
      {s: String => Sized(s)}
    )
    _ <- gauge.label("bar").inc
    _ <- gauge.label("baz").inc
    _ <- gauge.label("bar").inc
    _ <- gauge.label("baz").inc
    _ <- gauge.label("bar").dec
    currentMetrics <- cr.write004
  } yield currentMetrics
}

labelledGaugeExample.unsafeRunSync
```