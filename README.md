#!/bin/bash
############
#author:Akshaykumar
#this script will report the aws resource usage####
set -x
#aws s3
#aws ec2
#aws lamda
#aws iam user
############
#list s3 buckets
echo"print s3 buckets"
aws s3 ls
#list ec2 instance
echo "print ec2 instance"
aws ec2 describe-instances
#########
#list lamda
echo"print lamda instances"
aws lamda list-functions
#list iam user
echo "print iam users"
aws list-iam users
#

