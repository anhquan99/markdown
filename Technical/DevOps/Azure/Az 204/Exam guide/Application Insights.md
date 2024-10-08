## Azure Monitor
- Azure App Service diagnostic settings:
	- App Service Logs: collects information about Application logging, Web server logging, detailed error messages and failed requests tracing.
	- Log Stream.
	- Diagnostics settings: performance metrics will be collected in Azure Table Storage. There is a significant delay, of several minutes, before the logged data shows up in the files and tables.
- Azure Monitor for Azure Web Apps.
- Azure Service Health:
	- Another useful metric of many `IaaS` and `PaaS` services is Resource health.
	- When you design your solution in Azure, remember the whole solution's SLA can be lower than the SLA of its services. Example, the total SLA of an Azure Web Apps (Basic tier) and Azure SQL Database should be calculated with the following formula: 99.95% (Azure Web App) * 99.99% (Azure SQL Database) = 99.94%. This means about 1 minute a day and 7 minutes a week of possible downtime when service health issues affect the application.
### Resource logs and metrics:
- Activity log: subscription-level events that track operations for each Azure resource.
- Platform metrics: numerical values describe some aspect of a resource at a particular time.
- Resource logs: provide insight into operations that were performed by Azure resource.
- VM guest metrics and logs.
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
- Monitoring:
	- Alerts.
	- Metrics.
	- Diagnostic settings.
	- Logs.
	- Workbooks.
- Azure SQL Insights can only track query performance for all application requests. Profiler only helps for functions running from code.
### Application Map 
- Helps you spot performance bottlenecks or failure hotspots across all components of your distributed application. Each node on the map represents an application component or its dependencies and has health KPI and alert status. You can select any component to see more detailed diagnostics, such as Application Insights events.
- Components are independently deployable parts of your distributed/microservices application. Developers and operations teams have code-level visibility or access to telemetry generated by these application components.
	- Components are different from "observed" external dependencies such as SQL, Event Hubs, ... which you may not have access to (code or telemetry).
	- Components run on any number of server/role/container instances.
	- Components can be separate Application Insights instrumentation key. The preview map experience shows the components regardless of how they're set up.
	
## Monitor workbooks
- Azure workbooks can use sophisticated KQL queries, where you can combine different sources of data, support Markdown, and interact with chars, such as with filtering or ordering. Azure workbooks are based on reporting visuals for simplifying observation and printing reports. Notice that updates are not loaded automatically into a workbook. You need to refresh the workbook to observe the latest metric values. Meanwhile, some charts from the workbook do not exist on Azure dashboards and provide rich configuration settings to adjust the context and time interval. Moreover, Azure workbooks have a template you can use instead of starting from scratch.
```ad-note
- An instrumentation key uniquely designates an Application Insights resource and is the only piece of information required to provide authorized access for the purpose of uploading telemetry from monitored applications to Application Insights.
- Access keys are used by a variety of Azure resources, such as Azure Storage, but not Application Insights.
- Azure AD system-assigned managed identities and Azure AD user-assigned managed identities are not supported as an authorization mechanism by Application Insights.
- The `TrackEvent` API call tracks user action and other events. It is used to track user behavior or to monitor performance.
- The `TrackMetric` API call is used to track performance measurements such as queue length.
- The `TrackTrace` API call is used to capture Resource Diagnostic log messages and can also be used to capture third-party logs.
- You plan to use Application Insights to monitor the performance of an on-premises web application. You need to identify a configuration that satisfies the following requirements:
	- Minimize the volume of data ingested into Application Insights
	- Maximize the accuracy of the collected metrics.
	- **Solution**: Using standard metrics both minimizes the volume of data ingested into Application Insights and maximizes the accuracy of the collected metrics. Applying sampling and filtering would negatively affect the accuracy of the collected metrics. Using log-based metric does not minimize the volume of data ingested into Application Insights.
```
