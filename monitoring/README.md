#Windows CloudWatch Monitoring

##Prerequisites

1. Configure [Ec2 Service](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/UsingConfig_WinAMI.html#send_logs_to_cwl) on instance

2. Powershell installed

3. Local ec2 profile zone is set correctly


## Custom Plugins

####Local disk space, cpu, and memory monitoring
1. Copy 'Windows Monitors.zip' over to target instance and unzip at desktop.

2. Copy 3x ps1 files to Desktop

3. Open up task scheduler and import 3 xml files into task scheduler

4. Ensure tasks are started, and output will populate CloudWatch Metrics 'Custom Metrics: DMT Metrics' section with data.

## CloudWatch Metrics/Alarms
1. (Optional) Create SNS Topic and subscription:

```bash

aws sns create-topic --name <custom>
aws sns subscribe --topic-arn <arn output from create-topic> --protocol email --notification-endpoint <email address>

```

2. Add CloudWatch alarms to account for each monitor:

```bash

	aws cloudwatch put_metric_alarm --alarm-name <custom> --alarm-actions <sns arn for notifications> --metric-name <custom> --namespace <custom> --statistic <SampleCount||Average||Sum||Minimum||Maximum> --dimensions "Name=<custom>,Value=<custom>..." --period 300 --evaluation-periods 2 --threshold <custom> --comparison-operator <GreaterThanOrEqualToThreshold||GreaterThanThreshold||LessThanThreshold||LessThanOrEqualToThreshold>

```


###Command Syntax

--alarm-name (string)

The descriptive name for the alarm. This name must be unique within the user's AWS account

--alarm-actions (list)

The list of actions to execute when this alarm transitions into an ALARM state from any other state. Each action is specified as an Amazon Resource Name (ARN).
Valid Values: arn:aws:automate:region (e.g., us-east-1) :ec2:stop | arn:aws:automate:region (e.g., us-east-1) :ec2:terminate | arn:aws:automate:region (e.g., us-east-1) :ec2:recover
Valid Values (for use with IAM roles): arn:aws:swf:us-east-1:{customer-account }:action/actions/AWS_EC2.InstanceId.Stop/1.0 | arn:aws:swf:us-east-1:{customer-account }:action/actions/AWS_EC2.InstanceId.Terminate/1.0 | arn:aws:swf:us-east-1:{customer-account }:action/actions/AWS_EC2.InstanceId.Reboot/1.0
Note: You must create at least one stop, terminate, or reboot alarm using the Amazon EC2 or CloudWatch console to create the EC2ActionsAccess IAM role for the first time. After this IAM role is created, you can create stop, terminate, or reboot alarms using the CLI.
Syntax:

--metric-name (string)

The name for the alarm's associated metric.
--namespace (string)

The namespace for the alarm's associated metric.

--statistic (string)

The statistic to apply to the alarm's associated metric.
Possible values:
SampleCount
Average
Sum
Minimum
Maximum

--dimensions (list)

The dimensions for the alarm's associated metric.
Shorthand Syntax:

Name=string,Value=string ...
JSON Syntax:

[
  {
    "Name": "string",
    "Value": "string"
  }
  ...
]
--period (integer)

The period in seconds over which the specified statistic is applied.

--evaluation-periods (integer)

The number of periods over which data is compared to the specified threshold.

--threshold (double)

The value against which the specified statistic is compared.

--comparison-operator (string)

The arithmetic operation to use when comparing the specified statistic and threshold . The specified statistic value is used as the first operand.
Possible values:
GreaterThanOrEqualToThreshold
GreaterThanThreshold
LessThanThreshold
LessThanOrEqualToThreshold
--cli-input-json (string) Performs service operation based on the JSON string provided. The JSON string follows the format provided by --generate-cli-skeleton. If other arguments are provided on the command line, the CLI values will override the JSON-provided values.
