---
id: availablemetrics
title: Available metrics
sidebar_label: Available metrics
---
The available metrics are:
* `SizeMetric` - count the number of rows in your dataset
* `SumValuesMetric` - sum up a given column in your dataset
* `CountDistinctValuesMetric` - count the distinct values across a given set of columns
* `ComplianceMetric` - calculate the fraction of rows that comply with the given condition
* `DistinctnessMetric` - calculate the fraction of rows that are unique
* `MinValueMetric` - calculate the minimum value in a given column. Returns None if no rows in the dataset
* `MaxValueMetric` - calculate the maximum value in a given column. Returns None if no rows in the dataset

With most metrics a filter can be applied before the metric gets calculated - you can see an example of this below. 

## Size Metric
Size metrics are very straightforward and just count the number of rows in your dataset. A filter can be applied
to the dataset before the rows are counted.
e.g.
```scala mdoc:compile-only
import com.github.timgent.dataflare.metrics.MetricDescriptor.SizeMetric
import com.github.timgent.dataflare.metrics.MetricFilter
import org.apache.spark.sql.functions.col

SizeMetric(MetricFilter(col("fullName").isNotNull, "fullName is not null"))
```
A MetricFilter takes a filter condition and a descriptive string which is used for persistence.

## SumValuesMetric
SumValuesMetric sums the values in a single column. A filter can be provided (the default is no filter)
e.g.
```scala mdoc:compile-only
import com.github.timgent.dataflare.metrics.MetricDescriptor.SumValuesMetric
import com.github.timgent.dataflare.metrics.MetricFilter
import com.github.timgent.dataflare.metrics.MetricValue.LongMetric

SumValuesMetric[LongMetric]("numberOfItems", MetricFilter.noFilter)
```
Note the type parameter. This is required to specify the type that should be used for the metric. For example if you
have fractional values you should use a `DoubleMetric`, but for whole numbers a `LongMetric` is more appropriate.

## ComplianceMetric
ComplianceMetric tells you what proportion of rows in a dataset comply with the given constraint. Again a filter can
be applied before the metric is calculated. For example:
```scala mdoc:compile-only
import com.github.timgent.dataflare.metrics.{ComplianceFn, MetricFilter}
import com.github.timgent.dataflare.metrics.MetricDescriptor.ComplianceMetric
import org.apache.spark.sql.functions.col

ComplianceMetric(ComplianceFn(col("fullName").isNotNull, "fullName is not null"), MetricFilter.noFilter)
```

## CountDistinctValuesMetric
CountDistinctValuesMetric counts the number of distinct values in the given columns. A filter can be applied before the
metric is calculated. For example:
```scala mdoc:compile-only
import com.github.timgent.dataflare.metrics.MetricDescriptor.CountDistinctValuesMetric
import com.github.timgent.dataflare.metrics.MetricFilter

CountDistinctValuesMetric(List("firstName", "surname"), MetricFilter.noFilter)
```

## DistinctnessMetric
DistinctnessMetric calculates, for a given set of columns, how unique those are across the whole dataset. A filter can
be applied before the metric is calculated. For example:
```scala mdoc:compile-only
import com.github.timgent.dataflare.metrics.MetricDescriptor.DistinctnessMetric
import com.github.timgent.dataflare.metrics.MetricFilter

DistinctnessMetric(List("firstName", "surname"), MetricFilter.noFilter)
```

## MinValueMetric and MaxValueMetric
These calculate the minimum/maximum value of a given column in a Dataset. You must specify if they operate on columns
with Ints or Longs (by using OptLongMetric) or Doubles (by using OptDoubleMetric). A filter can be provided For example:
```scala mdoc:compile-only
import com.github.timgent.dataflare.metrics.MetricDescriptor.MinValueMetric
import com.github.timgent.dataflare.metrics.MetricFilter
import com.github.timgent.dataflare.metrics.MetricValue.OptLongMetric

MinValueMetric[OptLongMetric]("age", MetricFilter.noFilter)
```
The reason these checks use OptLongMetric or OptDouble metric is because in the case of an empty Dataset the only
sensible metric value to calculate is None. These types of metric value calculate metrics that are options to
acknowledge this possibility.