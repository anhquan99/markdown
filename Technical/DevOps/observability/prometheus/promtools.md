# Promtools
## Definition
- Promtools is a utility shipped with Prometheus that can be used to:
	- Check and validate configuration
	- Validate metrics passed to it are correctly formatted
	- Perform queries on a Prometheus server
	- Debugging and Profiling a Prometheus server
	- Perform unit tests against Recording/Alerting rules
## Commands
```shell
# validate config
promtool check config {config-path}
```