# Challenges of Cloud-native applications
- **App definition**: without careful documentation, developers may struggle to understand the components of a cloud-native app.
- **Communication**
- **Resiliency**
- **Distributed data**
- **Secrets**
# What is .NET Aspire
- A stack for building **resilient observable, and configurable** cloud-native apps with .NET.
- Includes telemetry, resilience, configuration, health checks, and composition.
- Makes it easy to discover, acquire and configure essential dependencies for cloud native apps.
# Orchestration
- In a .NET Aspire solution, there is an orchestration project named `<SolutionName>.AppHost` .
# Components
- Microservices rely on backing services to fulfill their functional requirements:
	- Data storage
	- Caching
	- Messaging
- .NET Aspire streamlines integrating these backing service into each microservice via standardized NuGet packages.
- These components guarantee reliable and uniform connections to the underlying services.
# Tolling
.NET Aspire enhances dev tools within VS with:
- Simplified creation of .NET Aspire solutions through new project templates.
- A dashboard accessible upon solution start-up, displaying microservices, backing services, and offering testing capabilities along with performance monitoring tools.
- Addition menu items for tasks such as adding .NET Aspire components and enabling project support for .NET Aspire orchestrator.
# Setup
- Install via CLI:
```
dotnet workload update
dotnet workload install aspire
dotnet workload list
```
# Benefits
- Orchestrating microservices
- Simplifying component use via NuGet packages
- Enhance developer productivity with tools and templates
# Compile time source generation
- Compile-time source generation with `ILogger` reduces logging costs by analyzing strings once instead of at each log request. It includes an ID for each log message type.
-  To use it partial logging methods with parameters and apply the `LoggerMessageAttribute`, .NET generates the complete logging method during compilation.
# Use cases
- Building a Microservices-Based API
- Monitoring and Telemetry