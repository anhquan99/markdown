## Azure Monitor
- Azure App Service diagnostic settings:
	- App Service Logs: collects information about Application logging, Web server logging, Detailed error messages and Failed requests tracing.
	- Log Stream.
	- Diagnostics settings: performance metrics will be collected in Azure Table Storage. There is a significant delay, of several minutes, before the logged data shows up in the files and tables.
- Azure Monitor for Azure Web Apps.
- Azure Service Health:
	- Another useful metric of many IaaS and PaaS services is Resource health.
	- When you design your solution in Azure, remember the whole solution's SLA can be lower than the SLA of its services. Example, the total SLA of an Azure Web Apps (Basic tier) and Azure SQL Database should be calculated with the following formula: 99.95% (Azure Web App) * 99.99% (Azure SQL Database) = 99.94%. This means about 1 minute a day and 7 minutes a week of possible downtime when service health issues affect the application.
## Application Insights
- It is a well-known telemetry framework that collects data from instrumented application code and tracks application activities, performance, custom metrics, and exceptions.
- Application Insight provides a 360-degree view of the performance of web applications by tracking the client code with JS objects executed in the browser and tracing requests and their metrics from the server side with the SDK.
- Security settings:
	- Networking isolation: enabling firewall rules to accept or not accept ingesting telemetry data and query requests from public networks. The firewall rules allow all or nothing from public traffic. For services running in Azure, such as the Azure Web Apps or Azure Virtual Network. Private Link will use only the Microsoft backend network.
	- Authentication for accessing reports: to configure the application to send telemetry, Application Insights provides a connection string with the instrumentation key. There is no other authentication required to send the insights to the service. Meanwhile, accessing collected telemetry requires authentication with your Microsoft or Azure AD account. Alternately, the application can obtain API keys to get access to the Application Insights reports from outside the Azure portal.
- Charting and dashboards:
	- Live metrics.
	- Performance.
	- Profiler: demonstrate the performance trace collected from the application for each executed request.
	- Usage.
	- Exception troubleshooting.
	- Availability test.
	- Application map.
## Monitor workbooks
- Azure workbooks can use sophisticated KQL queries, where you can combine different sources of data, support Markdown, and interact with chars, such as with filtering or ordering. Azure workbooks are based on reporting visuals for simplifying observation and printing reports. Notice that updates are not loaded automatically into a workbook. You need to refresh the workbook to observe the latest metric values. Meanwhile, some charts from the workbook do not exist on Azure dashboards and provide rich configuration settings to adjust the context and time interval. Moreover, Azure workbooks have a template you can use instead of starting from scratch.