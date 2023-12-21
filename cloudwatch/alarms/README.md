# CloudWatch Alarms

- Alarms are used to trigger notifications for any metric
- Various options (sampling, %, max, min, etc…)
- Alarm States:
  - OK -> The alarm will not be triggered
  - INSUFFICIENT_DATA -> Need more data to trigger it
  - ALARM -> Send a alarm
- Period:
  - Length of time in seconds to evaluate the metric
  - High resolution custom metrics: 10 sec, 30 sec or multiples of 60 sec

## Actions

- Notification Action
- Auto Scaling Action
- EC2 Action
- System Manager Action

## Targets

- Stop, Terminate, Reboot, or Recover an EC2 Instance
- Trigger Auto Scaling Action
- Send notification to SNS (from which you can do pretty much anything)

![Targets](./targets.png)

## Composite Alarms

- CloudWatch Alarms are on a single metric
- Composite Alarms are monitoring the states of multiple other alarms
- AND and OR conditions
- Helpful to reduce “alarm noise” by creating complex composite alarms

![Composite Alarm](./composite_alarms.png)

## EC2 Instance Recovery

- Status Check:
  - Instance status = check the EC2 VM
  - System status = check the underlying hardware

![Target Alarm Recovery](./target_ec2_recovery.png)

Based on the alarm status the EC2 instance can be moved to different from one host to another with the recovery process

- Recovery: Same Private, Public, Elastic IP, metadata, placement group

## More to know

- Alarms can be created based on CloudWatch Logs Metrics Filters

![Alarm based on Metric Filter](./metric_filter_alarm.png)

- To test alarms and notifications, set the alarm state to Alarm using CLI

${\color{yellow}aws cloudwatch set-alarm-state --alarm-name "myalarm" --state-value ALARM --state-reason "testing purposes"}$