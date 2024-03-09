- Provides governance, compliance and audit for your AWS Account.
- Enabled by default.
- Can put logs from CloudTrail into S3 or CloudWatch Logs.
- A trail can be applied to All Regions (default) or a single region.
- If a resource is deleted in AWS, investigate CloudTrail first.
# Events
- <mark style="background: #BBFABBA6;">Management Events:</mark>
	- Operations that are performed on resources in your AWS account.
	- By default, trails are configured to log management events.
	- Can separate Read Events (that don't modify resources) from Write Events (that may modify resources).
- <mark style="background: #BBFABBA6;">Data Events:</mark>
	- By default, data events are not logged (because of high volume operations).
	- Example:
		- S3 object-level activity
		- Lambda function execution activity
- Insights Events:
	- Capture unusual API call rate or error rate activity in you AWS account by analyzing CloudTrail management activity.
# CloudTrail insights
- Enable insights to detect unusual activity in your account:
	- Inaccurate resource provisioning
	- Hitting service limits
	- Bursts of AWS IAM actions
	- Gaps in periodic maintenance activity
- Analyzes normal management events to create a baseline and then continuously analyzes write events to detect unusual patterns
	- Anomalies appear in the CloudTrail console
	- Event is sent to S3
	- An event is generated in EventBridge
# Event retention
- Stored for 90 days, to keep events beyond this period, log them to S3 and use Athena.