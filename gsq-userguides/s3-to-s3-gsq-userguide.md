# S3 to S3 GSQ User Guide

Customers can share with GSQ large files, or a lot of files, using AWS' S3 to S3 copy.

## Copy files from customer's S3 to GSQ's S3

We use Amazon's AWS platform for cloud data storage. We store data objects (files) in S3 buckets (simple storage service).

## Step 1: Get GSQ's 12 digit AWS account number (the *destination*)

<p align="center">
<img src="https://github.com/geological-survey-of-queensland/share-data-with-gsq/blob/master/model/get-acct-num.png" width="700px"><br>
Figure 1: How to get the GSQ AWS Account number</p>

1. Sign into the GSQ account for the *destination* S3 bucket.
1. Click **Support** -> **Support Center** and copy the 12 digit AWS account number. This number is the Amazon Resource Name (ARN) of the destination account.

## Step 2: Update the S3 bucket policy to give to the customer

Attaching this policy to the *source* S3 bucket allows the GSQ *destination* account to perform the ListBucket and GetObject commands on the *source* S3 bucket.  

By default, an S3 object is owned by the account that uploaded the object. That's why granting the destination account the permissions to perform the cross-account copy makes sure that the destination owns the copied objects.  

1. Update the policy to change the value of the **Principal** to GSQ's ARN obtained in Step 1.  
2. Change the **SOURCE_BUCKET_NAME** to the name as supplied by the customer.
3. Send this policy as a JSON file to the customer.
4. Request the customer to attach the policy to the *source* bucket (see [how-to](https://docs.aws.amazon.com/AmazonS3/latest/dev/example-bucket-policies.html)).  

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DelegateS3Access",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::GSQ_ARN_ACCOUNT_NUMBER:root"
            },
            "Action": [
                "s3:ListBucket",
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::SOURCE_BUCKET_NAME/*",
                "arn:aws:s3:::SOURCE_BUCKET_NAME"
            ]
        }
    ]
}
```

## Step 3: Attach a policy to the *destination* GSQ Admin IAM role

Attaching this policy in the GSQ account allows users in the GSQ Admin Role to copy objects from the *source* bucket to the *destination* bucket.  

1. Sign in to *destination* GSQ AWS account and change Role to the GSQ Admin Role.  
2. Create the following policy in the IAM Concolse (see [how-to](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create-console.html)).  
3. Change the value of the **SOURCE_BUCKET_NAME** and **DESTINATION_BUCKET_NAME**.
4. Name the policy **Temp_S3_Sync_ddmmyyyy**.
5. Enter in the description **Delete this policy on ddmmyyyy** where the date is today + 5 business days.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetObject"
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
                "arn:aws:s3:::DESTINATION_BUCKET_NAME",
                "arn:aws:s3:::DESTINATION_BUCKET_NAME/*"
            ]
        }
    ]
}
```

## Step 4:  Sync S3 objects to destination

1. Open the AWS CLI.  
2. Copy S3 buckets from the *source* to *destination* using the following AWS CLI command:

```cmd
aws s3 sync s3://SOURCE_BUCKET_NAME s3://DESTINATION_BUCKET_NAME
```

### Step 4a: How to copy to a specific destination folder

The example below shows how to sync to a specific folder in the destination S3.

```cmd
aws s3 sync s3://SOURCE_BUCKET_NAME s3://DESTINATION_BUCKET_NAME/FOLDER_PATH/SUB_FOLDER_PATH
```

### Step 4b: How to include or exclude folders or files

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

### Step 4b: How to copy across regions

Add the source and destination region names as per below.

```cmd
aws s3 sync s3://SOURCE-BUCKET-NAME s3://DESTINATION-BUCKET-NAME --source-region SOURCE-REGION-NAME --region DESTINATION-REGION-NAME
```

### Step 4c: More help with the s3 sync command

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
