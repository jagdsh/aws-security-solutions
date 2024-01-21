# Launch instance in Public 1A
aws ec2 run-instances --image-id <value> --instance-type <value> --security-group-ids sg-0d38b9976ae1c55ca --subnet-id subnet-05806aed5d185fbc8 --key-name jags --user-data <value>

aws ec2 run-instances --image-id ami-0005e0cfe09cc9050 --instance-type t2.micro --security-group-ids sg-0eb3ebf012f4ecf76 --subnet-id subnet-05806aed5d185fbc8 --key-name jags --user-data file://user-data-subnet-id.txt


# Launch instance in Public 1B


# Launch instance in Private 1B


# Terminate instances

aws ec2 terminate-instances --instance-ids <value> <value>