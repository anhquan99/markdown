# Kibana
-  Kibana is an open source UI that is used to visual and analyze ES data.
- Kibana consists of apps.
## Apps
### Kibana solutions
- Discover: a playground for running ad-hoc queries against data
- Visualize: build and manage visualization, such as pie charts, line charts, ...
- Dashboard: an orchestration of visualizations
- Canvas: same as dashboard but provides more control over the visual appearance
- Maps: show data on the map
- Machine learning
- Graph: an interface for visualizing connections of data
### Observability solutions
- Metrics: infrastructure monitoring
- Logs: same as metrics but work with logs
- APM (Application Performance Management): process of monitoring how a given application performs.
- Uptime
### Security solutions
- TBD
## Kibana Query Language (KQL)
- KQL is a simple text-based query language for filtering data, it only filters data, and has no role in aggregating, transforming, or sorting data.
## Visualizations
- Metric
- Vertical bar
- Area chart
- Line chart
- Pie chart
- Histogram
- Heat map
- Tag clouds
- Goal and Gauges
## Dashboard security
### User
- Each user have their own account save into ES via Kibana.
### Spaces
- It is where user with appropriate role can access and read data.
### Role
- Each user can be set to number of appropriate roles.
- Role can be inherited from space.
