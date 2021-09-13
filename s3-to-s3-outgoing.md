# How to import data from GSQ using S3 to S3 sync

You can request to transfer one or more files directly to your own S3 instance rather than downloading them in the [Geoscience Open Data Portal](https://geoscience.data.qld.gov.au/).  This is good if you want to export very large files, or a lot of files, from the GSQ system directly into your S3 environment. If you wish to download files to a local machine it is often easier to just download directly rather than completing an S3 to S3 transfer.  
To lodge a request within the Open Data Portal add the files you wish to request to your cart and then click the 'request delivery via S3' button

![S3 Button](https://github.com/geological-survey-of-queensland/share-data-with-gsq/blob/Update/model/S3%20Button.jpg)

Once a request to extract files has been submitted you will be contacted by the support team to facilitate this request.  You can also contact the GSQ via [email](mailto:gsqopendata@resources.qld.gov.au) to request an S3-S3 transfer.


## Step1: Send an email to GSQ with your request

1. Send an email to GSQOpenData@resources.qld.gov.au with the following information:  
    a.  Your contact details (or details of the represntative we should contact at your organisation).  
    b.  What data you want to transfer.  
    c.  Your AWS ARN number (click [here](https://github.com/geological-survey-of-queensland/share-data-with-gsq/blob/Update/model/get-acct-num.png) for reference on this).  
    d.  Your *destination* bucket region (e.g. ap-southeast-2).  
    e.  Your *destination* bucket name.  

2. GSQ will then send you a personalised bucket policy to add to your S3 bucket.  

## Step 2: Attach a policy to your *destination* S3 bucket

Attaching this policy to the *destination* S3 bucket allows the GSQ *source* account to perform the ListBucket and GetObject commands on the *source* S3 bucket, and to copy the objects across.  

1. Sign in to your *destination* AWS account.  
2. Attach the S3 bucket policy sent to you by GSQ to the *destination* bucket (see [how-to](https://docs.aws.amazon.com/AmazonS3/latest/dev/example-bucket-policies.html)).  
3. An example of the bucket policy is as per the below code. However, the policy we send to you will have the value of **Principal** set to GSQ's ARN and will be customised to have your destination bucket name listed.

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
