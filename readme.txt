EC2 Instance - Open port checker.

M.Chambers 13/06/16

This is not as straight forward as you might think... :)

We evaluate SECURITY GROUPS, but report back on INSTANCES.

If the INSTANCE changes, we evaluate is based on its security groups.

But if a SECURITY GROUP changes we need to make sure that a compliant 
evaluation of the security group does not incorrectly evaluate an otherwise 
in-compliant instance.  Therefore, when one security group changes we need 
to evaluate ALL the security groups for ALL the related instances.

Therefore we need to trigger this rule for EITHER security group changes OR instance changes 
(e.g. if the instance adds or removes security groups we need to evaluate based on instance, 
if a security group changes rules we need to be triggered by the change in security group.)

SETUP INFO:

Lambda Role Policy:

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Resource": "arn:aws:logs:*:*:*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeInstances",
                "ec2:DescribeSecurityGroups"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "config:Put*"
            ],
            "Resource": "*"
        }
    ]
}

AWS Config Rule Settings:

Trigger type = Configuration changes
Resources = EC2:SecurityGroup, EC2:Instance

Key: port1, Value: [portNumber] e.g. 80 and or
Key: port2, Value: [portRange]  e.g. 0-1024