# Customer S3 to GSQ S3 : User Guide for GSQ Staff

Customers can share large files, or a lot of files, with GSQ using AWS' direct S3 to S3 copy. These instructions are a guide for GSQ staff to copy data into GSQ's S3 buckets.

## Copy files from customer's S3 to GSQ's S3

We use Amazon's AWS platform for cloud data storage. We store data objects (files) in S3 buckets (simple storage service).

## Step 1: Get GSQ's 12 digit AWS account number (the *destination*)

<p align="center">
<img src="https://github.com/geological-survey-of-queensland/share-data-with-gsq/blob/master/model/get-acct-num.png" width="700px"><br>
Figure 1: How to get the GSQ AWS Account number</p>

1. Sign into the GSQ account for the *destination* S3 bucket.
1. Click **Support** -> **Support Center** and copy the 12 digit AWS account number. This number is the Amazon Resource Name (ARN) of the destination account.

## Step 2: Update the Source S3 bucket policy to give to the customer

Attaching this policy to the *source* S3 bucket allows the GSQ *destination* account to perform the ListBucket and GetObject commands on the *source* S3 bucket.  

By default, an S3 object is owned by the account that uploaded the object. That's why granting the destination account the permissions to perform the cross-account copy makes sure that the destination (ie, GSQ) owns the copied objects.  

1. Update the policy to change the value of the **Principal** to GSQ's ARN obtained in Step 1.  
2. Ask the customer to supply the name of their S3 bucket and change the **SOURCE_BUCKET_NAME** to the name as supplied by the customer.
3. Send this policy as a JSON file to the customer (copy and paste into Notebook, modify the details as above then save as 'Source-S3-Policy.json').
4. Request the customer to attach the policy to the *source* bucket (see [how-to](https://docs.aws.amazon.com/AmazonS3/latest/dev/example-bucket-policies.html)).  

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DelegateS3Access",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::GSQ_ARN_ACCOUNT_NUMBER:XA-DataAdmin"
            },
            "Action": [
                "s3:ListBucket",
                "s3:GetObject",
                "s3:GetObjectTagging"
            ],
            "Resource": [
                "arn:aws:s3:::SOURCE_BUCKET_NAME/*",
                "arn:aws:s3:::SOURCE_BUCKET_NAME"
            ]
        }
    ]
}
```

## Step 3: Attach a policy to the *destination* GSQ S3 bucket, using the XA-DataAdmin IAM role

Attaching this policy in the GSQ account allows users in the GSQ XA-DataAdmin Role to copy objects from the *source* bucket to the *destination* bucket.  

Currently, attaching policies to buckets is restricted to SRA staff, so you'll need to email the GSQOpenData@dnrme.qld.gov.au account and ask them to ask SRA 
to attach the following policy to the destination S3 bucket. 
Copy and paste the below policy into Notebook, modify the *source bucket* name and save as 'GSQ-S3-Policy.json'.
For all data coming into GSQ, the *destination* bucket is **gsq-datashare**

SRA staff will follow these steps to attach the policy to our bucket:
1. Sign in to *destination* GSQ AWS account and change Role to the XA-DataAdmin Role.  
2. Create the following policy in the IAM Console (see [how-to](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create-console.html)).  
3. Change the value of the **SOURCE_BUCKET_NAME** and **DESTINATION_BUCKET_NAME**.
4. Name the policy **Temp_S3_Sync_ddmmyyyy**.
5. Enter in the description **Delete this policy on ddmmyyyy** where the date is today + 5 business days.
6. Attach this policy to the GSQ Admin role that you are using with the S3 CLI.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetObject",
                "s3:GetObjectTagging"
            ],
            "Resource": [
                "arn:aws:s3:::SOURCE_BUCKET_NAME",
                "arn:aws:s3:::SOURCE_BUCKET_NAME/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:PutObject",
                "s3:PutObjectAcl"
            ],
            "Resource": [
                "arn:aws:s3:::gsq-datashare/",
                "arn:aws:s3:::gsq-datashare/*"
            ]
        }
    ]
}
```

## Step 4:  Sync S3 objects to destination

Once the policies have been attached, we can transfer the files into GSQ.

1. Open the terminal to run AWS CLI.  
2. Set up the 2hr session token to access higher Roles
3. Copy S3 buckets from the *source* to *destination* using the following AWS CLI command, inserting the source company name:

```cmd
aws s3 sync s3://SOURCE_BUCKET_NAME s3://gsq-datashare/Incoming/COMPANY_NAME/ --profile XA-DataAdmin
```

### Step 4a: Setting up your Session Token to access Higher Roles

Run the following command in the terminal

```cmd
aws sts get-session-token --profile <YOUR_AWS_LOGIN_NAME> --serial-number arn:aws:iam::667011718570:mfa/<YOUR_AWS_LOGIN_NAME> --token-code <YOUR_CURRENT_6-digit_MFA_CODE>
```

This will generate the keys needed to access the Higher Roles. The keys expire after 2 hours so need to be renewed for each session.
Open your *credentials* file in Notebook and copy/paste the *aws_access_key_id =*, *aws_secret_access_key =* and *aws_session_token =* under your YOUR_AWS_LOGIN_NAME profile. Save the file.

When you've finished the session, open your *credentials* file, delete the saved session keys and replace them with the default account keys (*this will save you login trouble next time*)


### Step 4b: How to copy to a specific destination folder

Like the above in Step 4, the example below shows how to sync to a specific folder in the destination S3. 
For all examples below, the session token and --profile options must be set.

```cmd
aws s3 sync s3://SOURCE_BUCKET_NAME s3://DESTINATION_BUCKET_NAME/FOLDER_PATH/SUB_FOLDER_PATH
```

### Step 4c: How to include or exclude folders or files

The example below shows how we can include a specific file. All other files are excluded. You can pass an --include or --exclude argument multiple times. When there are multiple filters, the rule is the filters that appear later in the command take precedence over filters that appear earlier in the command. For instance, if the order was `--include "*.txt" --exclude "*"` then all files would be excluded.

```cmd
aws s3 sync s3://SOURCE_BUCKET_NAME s3://DESTINATION_BUCKET_NAME --exclude "*" --include "myfile.txt"
```

Syncing only files of type LAS

```cmd
aws s3 sync s3://SOURCE_BUCKET_NAME s3://DESTINATION_BUCKET_NAME --exclude "*" --include "*.las"
```

The example below shows how we can exclude the S3 folders SEISMIC and GEOCHEMISTRY. All other folders are copied.

```cmd
aws s3 sync s3://SOURCE_BUCKET_NAME s3://DESTINATION_BUCKET_NAME --exclude "SEISMIC/*" --exclude "GEOCHEMISTRY/*"
```

See the full list of filters here: https://docs.aws.amazon.com/cli/latest/reference/s3/index.html#use-of-exclude-and-include-filters

### Step 4d: How to copy across regions

Add the source and destination region names as per below.

```cmd
aws s3 sync s3://SOURCE-BUCKET-NAME s3://DESTINATION-BUCKET-NAME --source-region SOURCE-REGION-NAME --region DESTINATION-REGION-NAME
```

### Step 4e: More help with the s3 sync command

* Simple guide: https://docs.amazonaws.cn/en_us/cli/latest/userguide/cli-services-s3-commands.html
* Detailed guide: https://docs.aws.amazon.com/cli/latest/reference/s3/sync.html

## Step 5: Delete the IAM policy

1. Delete the IAM policy created in Step 3.

## Important

* If the source or destination bucket has [default encryption](https://docs.aws.amazon.com/AmazonS3/latest/dev/bucket-encryption.html) with AWS Key Management Service (AWS KMS) enabled, then you must also modify the permissions related to the AWS KMS key. For instructions, see [My Amazon S3 bucket has default encryption using a custom AWS KMS key. How can I allow users to download from and upload to the bucket?](https://aws.amazon.com/premiumsupport/knowledge-center/s3-bucket-access-default-encryption/)  

## Licence

The content of this repository is licensed for use with the [Creative Commons 4.0 License](https://creativecommons.org/licenses/by/4.0/). See the [license deed](LICENSE) for details.

## Contacts

*owner*:  
**Mark Gordon**  
*Director - Geoscience Information*  
Geological Survey of Queensland  
<mark.gordon@dnrme.qld.gov.au>  

*author*:  
**Matthew Greenwood**  
*Manager, Regional Compilations, Mineral Geoscience*  
Geological Survey of Queensland  
<Matthew.Greenwood@dnrme.qld.gov.au>
