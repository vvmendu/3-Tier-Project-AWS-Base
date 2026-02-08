# 3-Tier-Project-AWS-Base
3 Tier Project AWS Project base

#NOTES:
----------
#1. IAM Roles:  (( One EC2 = One Role. ))
--------------------------------------------
IAM ---> Role ----> Create Role

1. Type the name of each policy in the search bar and check the box next to each one.

AmazonSSMManagedInstanceCore
AmazonS3FullAccess
CloudWatchAgentServerPolicy
CloudWatchLogsFullAccess

Click Next: Tags (optional) to add any tags if necessary.
Click Next: Review.

2. Give your role a name (e.g., EC2SSM-S3-CloudWatch-Role).
Click Create role.

3. Attach the Role to Your EC2 Instance
Go to the EC2 console.
Select the instance you want to attach the role to.
Click on Actions → Security → Modify IAM role.

In the IAM role dropdown, select the role you just created.
Click Update IAM role.

Now, your EC2 instance should have the necessary permissions for SSM, S3 Full Access, and CloudWatch.

To add new Permissions as you go to the existing rule. 

-------------------------------------------------------------------------------------------------------------------------

#2. CloudWatch Setup :
------------------------------
Install agent: 
sudo yum install amazon-cloudwatch-agent -y

Create a Configuration File using below command 
sudo vi /opt/aws/amazon-cloudwatch-agent/bin/config.json

{
  "logs": {
    "logs_collected": {
      "files": {
        "collect_list": [
          {
            "file_path": "/var/log/*",
            "log_group_name": "ec2-all-logs",
            "log_stream_name": "{instance_id}-all-logs"
          }
        ]
      }
    }
  }
}

Start the agent using the configuration file:
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json -s
View Logs: 
CloudWatch ---> Logs ---> Log Management --->

Create an S3 Bucket for archiving logs older than 30 days, 
Bucket  Permissions  Edit Bucket Policy.. 

Bucket Policy for Logs :
Add the following policy:

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowCloudWatchLogs",
            "Effect": "Allow",
            "Principal": {
                "Service": "logs.amazonaws.com"
            },
            "Action": [
                "s3:PutObject",
                "s3:GetBucketAcl",
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::my-cloudwatch-logs-bucket-pr/*",
                "arn:aws:s3:::my-cloudwatch-logs-bucket-pr"
            ]
        }
    ]
}

-------------------------------------------------------------------------------------------------------------------------

#3. Frontend and Backend Code: 
https://github.com/vvmendu/3-Tier-Project-AWS-Base.git

Setup Frontend and Backend apps and test connectivity.

-------------------------------------------------------------------------------------------------------------------------


