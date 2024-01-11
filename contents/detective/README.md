# What is it?

- Amazon Detective analyzes, investigates, and quickly identifies the root cause of security issues or suspicious activities using ML and graphs.
- Automatically collects and processes events from VPC Flow Logs, CloudTrail, and GuardDuty to create a unified view.
- The main purpose is to produce visualizations with context details to find the root cause.


## Possible Architecture

- ![Architecture](./possible_architecture.png)

Guard Duty / Security Hub will send a finding like **Stealth:IAMUser/CloudTrailLoggingDisabled** and then Amazon Detective will gather information to mark it as True or false positive with manual intervention.

Detective helps to do the following steps

- Triage
    - Determine wheather it is True or False Positive
- Scoping
    - What systems/users are compromised?
    - Where did the attack originate?
    - How long has the attack been going on?
- Response
    - Stop the attack
    - Minimize the damage
    - Prevent similar attacks