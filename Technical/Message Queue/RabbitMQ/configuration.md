# Configuration
## Windows service
- On Windows, RabbitMQ can be enabled via service which can be installed by Windows scripts.
- The script folder is in `/sbin`.
### Environment variable
- `RABBITMQ_BASE`: folder for storing the data of RabbitMQ
- `ERLANG_HOME`: points to installed Erlang version
### Scripts
- `rabbitmq-plugins.bat`: used for installing the plugin
- `rabbitmq-server.bat`: used for starting RabbitMQ from cmd
- `rabbitmq-service.bat`: used for running as service