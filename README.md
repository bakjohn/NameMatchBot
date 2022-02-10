# NameMatchBot
Bot for matching names against a database of names. 

## Installation Instructions


To recreate the bot and names database used for the blog post you will need 2 unique buckets in your AWS Account. One is used for the Lambda Layer which imports the PyPhonetics library and the second is used to populate the Names database.  

1. Upload the Lambda Layer for PyPhonetics library to S3
    1. Create s3 bucket with a unique name
        1. aws s3 mb s3://<unique-bucket-name-1>
    2. Upload Lambda Layer to your S3 bucket from previous step.
        1. aws c3 cp PyPhoneticsLayer.zip s3://<unique-bucket-name-1>
2. Execute the CloudFormation script
    1. aws cloudformation create-stack —stack-name namesblogpoststack —template-body file://./BlogPost-Infra-CFN.json —parameters ParameterKey=PyPhoneticsLayerBucketName,ParameterValue=*<unique-bucket-name-1>*, ParameterKey=BucketName,ParameterValue=*<unique-bucket-name-2>*  —capabilities CAPABILITY_NAMED_IAM
3. Upload the names.csv file to the S3 Bucket
    1. aws s3 cp names.csv s3://<unique-bucket-name-2>

