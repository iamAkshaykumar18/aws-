#!/bin/bash
############
# Author: Akshaykumar
# This script will report the AWS resource usage
############
set -x

# Define the directory and report output path
REPORT_DIR="/home/ubuntu/aws_reports"
REPORT_PATH="$REPORT_DIR/aws_resource_report.txt"

# Create the directory if it does not exist
mkdir -p $REPORT_DIR

# Function to gather AWS resource information
generate_report() {
    {
        echo "AWS Resource Report"
        echo "==================="
        echo "Date: $(date)"
        echo
        echo "S3 Buckets:"
        aws s3 ls
        echo
        echo "EC2 Instances:"
        aws ec2 describe-instances --query 'Reservations[*].Instances[*].[InstanceId,InstanceType,State.Name,PublicIpAddress]' --output table
        echo
        echo "Lambda Functions:"
        aws lambda list-functions --query 'Functions[*].[FunctionName,Runtime,LastModified]' --output table
        echo
        echo "IAM Users:"
        aws iam list-users --query 'Users[*].[UserName,UserId,CreateDate]' --output table
    } > $REPORT_PATH
}

# Function to send email using AWS SES
send_email() {
    SUBJECT="Daily AWS Resource Report"
    EMAIL="akshayraavi7@gmail.com"
    BODY=$(cat $REPORT_PATH | sed 's/"/\\"/g' | sed 's/$/\\n/' | tr -d '\n')

    aws ses send-email \
        --from "your_verified_email@example.com" \
        --destination "ToAddresses=$EMAIL" \
        --message "Subject={Data=$SUBJECT,Charset=utf8},Body={Text={Data=$BODY,Charset=utf8}}"
}

# Generate the report
generate_report

# Send the report via email
send_email
