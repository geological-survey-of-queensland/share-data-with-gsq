# Snowball data transfers into GSQ's S3 : User Guide

AWS Snowball is a secure physical device that can transfer data from your data centre or office network to GSQ.

<p align="center">
<img src="https://github.com/geological-survey-of-queensland/share-data-with-gsq/blob/master/model/aws-snowball-process.png" width="700px"><br>
Figure 1: How the AWS Snowball data transfer process works</p>

A Snowball is useful when you have very large file sizes or quantities to transfer to GSQ and have the data stored locally in your system. 
See here for details: https://aws.amazon.com/snowball/ 

Please note that there is a minimum $300 fee for this service.


## Step 1: Email GSQ and request a Snowball data transfer

Send an email to GSQOpenData@dnrme.qld.gov.au to request a Snowball data transfer.

Please include the following information:

 * The contact details of your contact person. 
 * What the data is (e.g. airborne survey flown for company, data relating to a lodged survey, etc.). 
 * What the data relates to (e.g. Permit EPM12345, Company Report CR6789 or Survey PID XXXX).
 * The approximate total amount of data to be transferred, in Gb/Tb.
 * Your Purchase Order details to cover the cost (for pricing details, see https://aws.amazon.com/snowball/pricing/ ).
 * The physical address for delivery of the Snowball device.

Please ensure that data related to each Report/Survey/Permit/Dataset is in separate, clearly-named folders prior to transfer.


## Step 2: Take delivery of the Snowball device

GSQ will arrange for a Snowball device to be delivered to your specified address.


## Step 3: Connect the Snowball to your system and load the files onto the device

Instructions for this can be found here: https://aws.amazon.com/snowball/getting-started/ 


## Step 4: Arrange for the Snowball device to be collected

Once your files have been loaded onto the device, arrange for the device to be collected. 
Please note that additional fees apply if this step occurs 10 days or more after the initial delivery date. Refer to the pricing link in Step 1.

Email GSQOpenData@dnrme.qld.gov.au to let us know when the device has been collected and the data is on it's way to us.

After collection, the device will be delivered to the AWS Data Centre and the files uploaded into GSQ's S3 bucket.


## Step 5: Confirmation

You will recieve an email from GSQ as confirmation once the data has been uploaded into our S3 bucket.



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

