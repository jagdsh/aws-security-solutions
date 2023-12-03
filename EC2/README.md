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



