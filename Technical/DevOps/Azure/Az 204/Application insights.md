- Application insights is an extension of Azure Monitor provides Application Performance Monitoring (APM) tools are useful to monitor applications from development, test, and production in following ways:
	- Proactively understand how an application os performing.
	- Reactively review application execution data to determine the cause of an incident.
# Feature:
- Live metrics.
- Availability.
- Github or Azure DevOps integration.
- Usage.
- Smart detection.
- Application map.
- Distributed tracing.
# Monitors:
- Request rates, response times and failure rates.
- Dependency rates, response times, and failure rates
- Exceptions.
- Page views and load performance.
- AJAX calls.
- User and session counts.
- Performance counters.
- Host diagnostics.
- Diagnostics trace logs.
- Custom evnts and metrics.
# Log-based metrics:
## Log-based metrics:
- Metrics are translated into Kusto queries from stored events.
- Log-based metrics have more dimensions, which makes them a superior option for data analysis and ad-hoc diagnostics.
- Developers can send events manually by using the SDK or use the automatic collection of events from auto-instrumentation.
- Application Insight collects events as logs.
- For large volume of telemetry, Application Insights implements several telemetry volume reduction techniques, such as sampling and filtering to reduce the number of collected and stored events. But this lower the accuracy of the metrics.
## Standard metrics (pre-aggregated):
- Metrics are stored as pre-aggregated time series so they have better performance at query time, faster retrieving data and requires less compute power.
- It's a better choice for dashboarding and in real-time alerting.
- For SDKs that don't implement pre-aggregation, the Application Insights still populates the new metrics by aggregating the events received by the Application Insights event collection endpoint.
## Instrument an app for monitoring
- Application Insights is enabled through either Auto-Instrumentation (agent) or by adding the Application Insights SDK to your application code.
### Auto-instrumentation
- Is the preferred instrumentation method. It required no developer investment and eliminates future overhead related to updating the SDK.
- It's also the only way to instrument an application in which you don't have access to the source code.
- The list of services supported by auto-instrumentation changes rapidly.
### Via Application Insights SDKs
- Enable when:
	- Require custom events and metrics.
	- Require control over the flow of telemetry.
	- Auto-instrumentation isn't available.
# Availability test
- URL ping test
- Standard test: similar URL ping test but include SSL certificate validity, proactive lifetime check.
- Custom TrackAvailability: create a custom application o run availability tests, you can use the `TrackAvailability()` method to send the result to Application Insights.
- Multi-step test (only available through Visual Studio 2019: is the long term supported solution request for multi request or authentication test scenarios.
- <mark style="background: #ADCCFFA6;">Note:</mark> URL ping test relies on the DNS infrastructure of the public internet to resolve the domain names of the tested endpoints. If you can not  resolve the DNS, you can use the TrackingAvailability test instead.