# Prometheus

## PromQl

### Data Types

- instant vector = A set of time series containing a single sample for each time series, all sharing the same timestamp.
  (giving a single value of each timeseries of single sample)
  Ex, prometheus_http_requests_total

- Range vector = A set of time series containing a range of data points over time for each time series.
  Ex, prometheus_http_requests_total[1m]

- Scalar = A simple numeric floating point value.
  Ex: 15.21

- String - A simple string value, currently unused.

### Selecors and Matchers

When you don't want the unwanted metrics. You can filters them using labels done by selectors.
Ex: process_cpu_seconds_total{job='node_exporter'}
job='node_exporter' filter in above example is called Matcher.
for multiple conditions use ','.

Four types of matchers.
- Equality matcher(=) select labels that are exactly equal to the provied string.

- Negative Equality matcher (!=) select labels that are not equall to provided string.

- Regular expression matcher (=~) 
  Ex, prometheus_http_requests_total{handler=~"/api.*"}

- Negative regular expression matcher (!~)

### Operators

#### Binary Operators
  
- Arithmetic Binary Operators
  - defined between scalar/scalar, vector/scalary, vector/vector value pairs.
  - Ex, node_momory_Active_bytes/8

- Comparison Binary Operator
  - scalar/scalar, vector/scalar, vector/vector 

- Logical/set Binary Operator
  - and, or, unless(complement)
  - between instant vectors only.

ignorign(label)
prometheus_http_requests_total and ingoring(handler) promthttp_metric_handler_requests_total

on(code) 
will match only the code label.

#### Aggregation Operators

- sum(prometheus_http_requests_total)by(code)
- max()
- min()
- count()
