# How to share data with GSQ using S3 to S3 sync

You can share files in your AWS S3 buckets directly into GSQ's AWS S3 buckets through AWS sync. This is good if you have large files, or a lot of files, that you need to share with GSQ.  

## Step1: Send an email to GSQ with your request

1. Send an email to GSQOpenData@resources.qld.gov.au with the following information:  
    a.  The contact details of your contact person. 
    b.  What the data is (e.g. airborne survey flown for company, data relating to a lodged survey, etc.).
    c.  What the data relates to (e.g. Permit EPM12345, Company Report CR6789 or Survey PID XXXX).  
    d.  Your *source* bucket region (e.g. ap-southeast-2).  
    e.  Your *source* bucket name.  
    f.  The PATH and name of S3 folder(s) that contain the data you want to transfer.  
      * We prefer transferring all contents of an S3 folder rather than transferring individual files by filename.  
      * Please ensure that data related to each Report/Survey/Permit/Dataset is in separate, clearly-named folders  
2. GSQ will then send you a personalised bucket policy to add to your S3 bucket.  

## Step 2: Attach a policy to your *source* S3 bucket

Attaching this policy to the *source* S3 bucket allows the GSQ *destination* account to perform the ListBucket and GetObject commands on the *source* S3 bucket, and to copy the objects across.  

1. Sign in to your *source* AWS account.  
2. Attach the S3 bucket policy sent to you by GSQ to the *source* bucket (see [how-to](https://docs.aws.amazon.com/AmazonS3/latest/dev/example-bucket-policies.html)).  
3. An example of the bucket policy is as per the below code. However, the policy we send to you will have the value of **Principal** set to GSQ's ARN and will be customised to have your source bucket name listed.

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

## Step 3: GSQ will copy your files directly into our S3 bucket

You will be notified by email once this has been completed successfully.


## Important

* If your source bucket has [default encryption](https://docs.aws.amazon.com/AmazonS3/latest/dev/bucket-encryption.html) with AWS Key Management Service (AWS KMS) enabled, then you must also modify the permissions related to the AWS KMS key. For instructions, see [My Amazon S3 bucket has default encryption using a custom AWS KMS key. How can I allow users to download from and upload to the bucket?](https://aws.amazon.com/premiumsupport/knowledge-center/s3-bucket-access-default-encryption/)  

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
