# Alerting
## Motivation
- We need some sort of mechanism to alert administrators when problems occur.
## Behind the scene
- Prometheus supports the creation of alerting rules that evaluate using standard PromQL expressions. When the expression returns one or more vector elements - that is, when the condition is met - Prometheus triggers an alert.
- Prometheus is solely responsible for firing alerts. It does not send notifications such as emails or SMS messages directly.
- Instead, Prometheus relays alert data to Alertmanager, a dedicated component that handles notifications by integrating with various communication tools like email, Slack, or paging services. This separation of concerns allows a single Alertmanager to manage alerts from multiple Prometheus servers across different environments.
```yaml
groups:
  - name: node
    interval: 15s
    rules:
      - record: node_memory_memFree_percent
        expr: 100 - (100 * node_memory_MemFree_bytes{job="node"} / node_memory_MemTotal_bytes{job="node"})
      - alert: LowMemory
        expr: node_memory_memFree_percent < 20
        for: 3m
```
## Alert status
- Inactive: expression has not returned any results.
- Pending: expression returned results, but it hasn't been long enough to be considered firing.
- Firing: active for more than the defined for clause.
## Labels and annotations
### Labels
- Use for Classifying and Routing Alerts
- Labels are essential for classifying and matching alerts in Alertmanager. By adding key-value pairs as labels in your alert rule configuration, you can filter and route alerts according to specific criteria.
### Annotations
- Used for adding descriptive context.
- Annotations complement labels by providing additional descriptive details about an alert. They do not influence alert matching or routing but instead offer valuable context for understanding the alert’s condition.
- Annotations use the Go templating language, enabling dynamic insertion of alert-related information.
## Alert manager
- Responsible for receiving alerts generated from Prometheus and converting them into notifications.
- Port: 9093
- Notifications:
	- pages
	- webhooks
	- email messages
	- chat messages
### Architecture
![](/image/Pasted%20image%2020260520203653.png)
- **Dispatcher**: alert arrives at the dispatcher, which routes alert to appropriate processing nodes.
- **Inhibition**: define rules to suppress specific alert specific alerts when certain other alerts are active. Ex: if Alert X is firing, you can configure a rule to automatically suppress Alert Y.
- **Silencing**: allows you to mute alerts temporarily, which is particularly useful during planned maintenance windows. This prevents unnecessary notifications when known conditions are being actively managed.
- **Routing**: the routing engine evaluates rules to decide which team or integration should receive the notification. The engine maps alerts to the appropriate destinations based on the alert’s characteristics and predefined configurations.
- **Notification**: integrates with third-party services—such as email providers, pager systems, and chat platforms—to deliver notifications to users effectively.
### Configuration
```yaml
global:
  smtp_smarthost: 'mail.example.com:25'
  smtp_from: 'test@example.com'
route:
  receiver: staff # default route
  group_by: ['alertname', 'job']
  routes:
    - match_re:
        job: (node|windows)
      receiver: infra-email
    - matchers:
        job: kubernetes
      receiver: k8s-slack
receivers:
  - name: k8s-slack
    slack_configs:
      - channel: '#alerts'
        text: 'https://exampl.com/alerts/{{ .GroupLabels.app }}'
```
#### `global`
- Default configuration sets default parameters for the entire Alertmanager setup.
#### `route`
- Provides a set of rules to determine what alerts get matched up with which receivers.
- At the top level there is a fallback/default route.
- Use `matchers` or `match_re` - regex to match specific labels.
- Sub-routes can be used for further route matching, which allows you to group alert by a broad category first, and then apply more specific rule inside that category.
	- A parent route can first match alerts with a specific label, and sub-routes can further refine the selection based on additional criteria.
```yaml
# Example of Nested Routes
routes:
  - matchers:
      - team="infrastructure"
    receiver: "infra-slack-channel" # Default for all infra alerts
    
    # --- NESTED ROUTES START HERE ---
    routes: 
      - matchers:
          - severity="critical"
        receiver: "pagerduty-oncall" # Overrides the default if it's critical
```
- Use `continue` to send an alert to multiple receiver.
```yaml
# Example of Continue
routes:
  - matchers:
      - service="database"
    receiver: "dba-team-email"
    continue: true
    
  - matchers:
      - severity="critical"
    receiver: "global-incident-slack"
```
- Alert grouping consolidates multiple alerts into a single notification based on specified label values. The `group_by` field controls how alerts are bundled, which can be particularly useful when organizing alerts by team or other criteria.
```yaml
route:
  receiver: fallback-pager
  group_by: [team]
routes:
  - match:
      team: infra
    group_by: [region, env]
    receiver: infra-email
    routes:
      - match:
          severity: page
        receiver: infra-pager
```
#### `receiver`
- Responsible for taking grouped alerts and producing notifications.
- Contains one or more notifiers to forward alerts to users.
### Restart Alertmanager to reload config
- Restart
- Send a SIGHUP: `sudo killall -HUP alertmanager`
- HTTP Post to `/-/reload` endpoint
### Notification template
- The notifier enables customization of notification message using the Go templating system.
![](/image/Pasted%20image%2020260520222430.png)