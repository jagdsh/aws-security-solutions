# Amazon EventBridge (formerly CloudWatch Events)

- Schedule: Cron jobs (scheduled scripts)

![Cron Job Trigger](./cronjob_trigger.png)

- Event Pattern: Event rules to react to a service doing something

![Rule filter](./filter_action_trgger.png)

- Trigger Lambda functions, send SQS/SNS messages
- Parterened event sources are pre defined in the console.

![Different Bus](./different_bus.png)

- Event buses can be accessed by other AWS accounts using Resource-based Policies
- You can archive events (all/filter) sent to an event bus (indefinitely or set period)
- Ability to replay archived events

![IAM Policy to Trigger From Other AWS](./trigger_events_from_other_aws.png)

- Manage permissions for a specific Event Bus
- Example: allow/deny events from another AWS account or AWS region
- Use case: aggregate all events from your AWS Organization in a single AWS account or AWS region

## Rules

![Example Sources and Destincation](./rules.png)

## Schema Registry

![Schema Registry](./schema_registry.png)

- EventBridge can analyze the events in your bus and infer the schema
- The Schema Registry allows you to generate code for your application, that will know in advance how data is structured in the event bus
- Schema can be versioned

## Sandbox

- There is a sandbox for trying out various rule pattern for setting up events in the event bus
-