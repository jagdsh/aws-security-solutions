# EC2 Key Pairs

- When you create EC2 instance the private keys are generated, downloaded to user and deleted in the EC2 instance. 
- Only Public keys are stored in the EBS Volume attached in the EC2 instance. 
- User uses only the Private key to SSH access.

Key format supported: ED25519 and 2048-bit SSH-2 RSA keys.

Note: We should be keeping the private key secure, we can cannot recover it.

![EC2 key Pairs](./key_pairs.png)


## EC2 Key troubleshooting

The key pairs, are persisted in the EC2 instance. Even when we delete it from AWS console, it remains in the mounted volume `~/.ssh/authorized_keys`

[Reference Diagram](./key_pairs_console.png)

- When EC2 machines are pre build using AMI the old keys wil be present in the instance along with the new key used while creating EC2 instance.

- The user can use old/new private key for SSH into EC2 instance

[Reference Diagram](./key_pair_old_new.png)


## Remediating EC2 Exposed Key Pair 

- Remove all the public keys in `~/.ssh/authorized_keys` file on EC2 instances
- Create a new Key Pair and add its public key to the `~/.ssh/authorized_keys` file on all EC2 instances
    - Note: Automate add/delete public keys on EC2 instances rule by running SSM Run Command

![Reference Diagram](./key_pair_exposed_remediating.png)


## EC2 SSH Connection workflow

1. Each EC2 instanse will be running a *EC2 instance connect agent*

2. When we connect from Browser or from CLI the connection goes through "EC2 Instance Connect API"

3. "EC2 Instance Connect API" will generate a temporary private and public key.
    - Public key will be valid for 60 secs only

4. "EC2 Instance Connect API" will push *one time temporary public key* to the EC2 instanse metadata, 

5. When "EC2 Instance Connect API" tries to ssh into the EC2 Instance, the SSH process will check the original key pair created when the instance is created and the temporary public key pushed by "EC2 Instance Connect API".

6. This is done to make sure all connetion is going through "EC2 Instance Connect API".

7. All the connection through "EC2 Instance Connect API" are logged in CloudTrail. 

**Only "EC2 Instance Connect API" will be connecting to EC2 instance not the browser or CLI directly.**

### Security Group
 - Since only the "EC2 Instance Connect API" is conneting to the EC2, we can create a security group with accepting inbound only from "EC2 Instance Connect API" range for the port 22.

> {
>
>   "ip_prefix": "18.206.107.24/29",
>
>   "region": "us-east-1",
>
>   "service": "EC2_INSTANCE_CONNECT",
>
>   "network_border_group": "us-east-1"
>
> }


    {
        "ip_prefix": "18.206.107.24/29",
        "region": "us-east-1",
        "service": "EC2_INSTANCE_CONNECT",
        "network_border_group": "us-east-1"
    }



https://ip-ranges.amazonaws.com/ip-ranges.json

![Reference Diagram](./instance_connect_working.png)


## EC2 Serial Console

    With the EC2 serial console, you have access to your Amazon EC2 instance's serial port, which you can use to troubleshoot boot, network configuration, and other issues. The serial console does not require your instance to have any networking capabilities. With the serial console, you can enter commands to an instance as if your keyboard and monitor are directly attached to the instance's serial port. The serial console session lasts during instance reboot and stop. During reboot, you can view all of the boot messages from the start.

Features:
 - Use with supported Nitro-based EC2 instance
 - Must setup OS User and Password
 - Only one active session per EC2 instance
 - Disabled by default (enable at AWS account level)

 ![Reference Diagram](./serial_console.png)

## Lost SSH EC2 Key Pair - Linux
    There many techniques to recover the lost key pair, I have captured few techniques here

### Technic-1: Using EC2 User data

Follow the below steps for recovering the SSH access to the EC2 instance 

1. Create a new Key pair locally
2. Stop the EC2 Instance from AWS console
3. Update the *EC2 User Data* which is a cloud config format, Sameple user data
    - It only adds new user doesn't override the existing public keys
    ```yaml
    Content-Type: multipart/mixed; boundary="//"
    MIME-version: 1.0

    --//
    Content-Type: text/cloud-config; charset="us-ascii"
    MIME-Version: 1.0
    Content-Transfer-Encoding: 7bit
    Content-Disposition: attachment; filename="aws-config.txt"

    #aws-config 
    cloud_final_modules:
    - [users-groups, once] 
    users:
    - name: ec2-user 
        ssh-authorized-keys:
            - ssh-rsa AAAAB3NzaClyc2EAAAADAQABAAAAgQC3kfkTYXvPi2+..
    ```
4. Start the instance
5. Connect with private key
6. Delete the EC2 User Data 

![Reference Diagram](./lost_key_pair_recovery_1_user_data.png)

### Technic-2: Using Systems Manager

Follow the below steps for recovering the SSH access to the EC2 instance 

1. The SSM agent has to be installed in the EC2 instance.
2. The SSM automation which has an IAM role policy attched to it to access the EC2 instance
3. We can run the `AWSSupport-ResetAccess` command it generates a new private & public key.
4. It store the private key to SSM parameter in encrypted format, in the path `/ec2rl/openssh/`${\color{red}instance\_id}$`/key`
5. It uploads the public key to the EC2 instance

![Reference Diagram](./lost_key_pair_recovery_2_ssm.png)

### Technic-3: Using EC2 Instance Connect

Follow the below steps for recovering the SSH access to the EC2 instance 

1. EC2 Instance connect agent must be installed in the EC2 instance 
    - already installed on Amazon Linux 2 and Ubuntu 16.04 or later
2. Connect to the EC2 instance using the "EC2 Instance connect API" in EC2 console or EC2 Instance Connect CLI
3. Etablish a connection using the temporary session.
4. Add the newly generated Public key in the `~/.ssh/authorized_keys`


![Reference Diagram](./lost_key_pair_recovery_3_ec2_connect_api.png)

### Technic-4: Using EC2 Serial Console

Follow the below steps for recovering the SSH access to the EC2 instance 
1. EC2 serial console access has to be enabled at account level.
2. It should be a Nitro-based EC2 instances
3. Connect using Serial Console to the Ec2 instance using OS User & Password
4. Add the newly generated Public key in the `~/.ssh/authorized_keys`

Advantages in this method:
1. Connect to your instance without a working network connection

![Reference Diagram](./lost_key_pair_recovery_4_serial_console.png)

### Technic-5: Using EBS Volume Swap

Follow the below steps for recovering the SSH access to the EC2 instance 
1. Create a new Key Pair
2. Stop the original EC2 instance
3. Detach the EBS root volume
4. Attach the EBS volume to a temporary new EC2 instance as a secondary volume
5. Add the new public key to `~/.ssh/authorized_keys` on the original volume
6. Shutdown the new temporary EC2 instance
7. Re-attach the volume to the original EC2 instance
8. Restart the instance

![Reference Diagram](./lost_key_pair_recovery_5_ebs_volume_swap.png)


## Lost EC2 Password - Windows

    There many techniques to recover the lost key pair, I have captured few techniques here

### Technic-1: Using EC2Launch v2

Follow the below steps for recovering the Lost Password access to the Windows EC2 instance 
1. First verify "EC2Launch v2 service" is running
    - Windows AMIs with the EC2Launch v2 service
2. Detach the EBS root volume
3. Attach the volume to a temporary new EC2 instance as a secondary volume
4. ${\color{yellow}Delete file `\%ProgramData\%/Amazon/EC2Launch/state/.run-once` }$
5. Re-attach the volume to the original instance, then restart the instance
6. Now you are prompted to enter a new password, since you are running the instance first time.

![Reference Diagram](./lost_password_recovery_ec2_launch_v2.png)

### Technic-2: Using EC2Config

Follow the below steps for recovering the Lost Password access to the Windows EC2 instance 
1. Verify EC2Config service is running
    - Windows AMIs before Windows Server 2016
2. Detach the EBS root volume
3. Attach the volume to a temporary instance as a secondary volume
4. ${\color{yellow}Modify file `\ProgramFiles\Amazon\Ec2ConfigService\Settings\config.xml`}$
5. Set EC2SetPassword to Enabled
6. Reattach the volume to the original instance, then restart the instance

![Reference Diagram](./lost_password_recovery_ec2_config.png)

### Technic-3: Using EC2Launch

Follow the below steps for recovering the Lost Password access to the Windows EC2 instance 

1. Windows Server 2016 and later AMIs that doesn’t include EC2Launch v2
2. Detach the EBS root volume
3. Attach the volume to a temporary instance as a secondary volume
4. ${\color{yellow}Download and install EC2Rescue Tool for Windows Server}$
5. Select Offline Instance Option -> Diagnose and Rescue -> Reset Administrator Password
6. Reattach the volume to the original instance, then restart the instance


![Reference Diagram](./lost_password_recovery_ec2_launch.png)

### Technic-4: Using Systems Manager

Must have SSM Agent installed
1. Method 1
    - Use *AWSSupport-RunEC2RescueForWindowsTool* Run Command Document
    - Install and run EC2Rescue Tool for Windows Server
    - Command is set to ResetAccess
2. Method 2
    - Use *AWSSupport-ResetAccess* Automation Document
    - Works for both Linux and Windows
3. Method 3
    - Manually *AWS-RunPowerShellScript* Run Command Document
    - Command: net user Administrator <PASSWORD>

Follow the below steps for recovering the Lost Password access to the Windows EC2 instance 

![Reference Diagram](./lost_password_recovery_system_manager.png)

## EC2 Rescue tool
