
## Automated Security Assessments

**only for EC2 instances, Container Images & Lambda functions**

- For EC2 instances
    - Leveraging the AWS System Manager (SSM) agent
    - Analyze against unintended network accessibility
    - Analyze the running OS against known vulnerabilities
- For Container Images push to Amazon ECR
    - Assessment of Container Images as they are pushed
- For Lambda Functions
    - Identifies software vulnerabilities in function code and package dependencies
    - Assessment of functions as they are deployed
- Reporting & integration with AWS Security Hub
- Send findings to Amazon EventBridge

## Workflow
- Continuous scanning of the infrastructure, only when needed
- Package vulnerabilities (EC2, ECR & Lambda) – database of CVE
    - Whenever there is a update of the CVE database Amazon Inspector runs the scanning again.
- Risk Score is associated with all vulerabilituy for prioritization


## Trouble shooting

When we enable Amazon Inspector and we get Unmanged instance status like the following 
![Unmanaged Status](./unmanaged_instance.png)

It means that the instance is not managed by SSM automation. 
Then we run the AWS Support using the troubleshoot the Managed Instance using the following workflow which asks the InstanceID

    AWS Systems Manager -> Automation -> AWS Support TroubleshootManagedInstance

When there is a error called "GetEC2InstanceProperties" -> Instance is not managed by Systems Manager (SSM)

- We can install the SSM using Host Management in the Systems Manager dashboard
- Then it 

![Error In AWS Execute Script](./unmanaged_instance_troubleshooting.png)
