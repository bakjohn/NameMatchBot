# NameMatchBot
Bot for matching names against a database of names. 

## Installation Instructions

To recreate the bot and names database used for the blog post you will need 2 unique buckets in your AWS Account. One is used for the Lambda Layer which imports the PyPhonetics library (unique-bucket-1) and the second (unique-bucket-2) is used to populate the Names database.  

1. Upload the Lambda Layer for PyPhonetics library to S3.
    a. Create s3 bucket with a unique name, replace *unique-bucket-name-1* with a unique name of your choice. This bucket will be used to store our Python dependency for the Bot Lambda. 
        
        aws s3 mb s3://unique-bucket-name-1 --region <region>
        
    b. Upload Lambda Layer to your S3 bucket from previous step.
    
        aws s3 cp PyPhoneticsLayer.zip s3://unique-bucket-name-1
        
    c. Upload Cloud Formation Template to the S3 bucket from step a. 
    
        aws s3 cp BlogPost-Infra-CFN.json s3://unique-bucket-name-1
        
2. Execute the CloudFormation script. Replace unique-bucket-name-1 with the name from the previous step and use a second unique bucket name for unique-bucket-name-2. This second bucket will be used to upload a CSV file of first and last names to populate our DynamoDb table of names. 
    
    ```aws cloudformation create-stack —stack-name namesblogpoststack —template-file s3://<replace unique-bucket-name-1>/BlogPost-Infra-CFN.json —parameters ParameterKey=PyPhoneticsLayerBucketName,ParameterValue=<replace unique-bucket-name-1>, ParameterKey=BucketName,ParameterValue=<replace unique-bucket-name-2>  —capabilities CAPABILITY_NAMED_IAM```
    
3. Verify the CloudFormation script has completed successfully. The cloudformation stack will take several minutes to finish executing. After a few minutes then execute the following command.

    ``` aws cloudformation describe-stacks --stack-name namesblogpoststack --region <region> | grep 'StackStatus'```
    
    When the status is reported back as ```CREATE_COMPLETE``` then proceed to step 4. 
    
4. When the cloud formation stack is created successfully then upload the names.csv file to the S3 Bucket. This step populates the NamesTable with 50 sample names and will trigger the bigram tables to be populated as well. 
    
    ```aws s3 cp names.csv s3://<replace unique-bucket-name-2>```


## Bot Usage

To interact with the Bot navigate to the Lex V2 Console. In the list of Bots, you should now see "NameMatchToDirectoryBot". Click on the Bot which will open the Bot Details page. On the left hand side, under English (US), click on Intents. At the bottom right side of the screen there is a "Test" button. Click on the test button which will open a window named "Test Draft Version". You can now use the textbox or the mic button to give input to the bot. The Bot's workflow will begin if you type "Ready" and hit Enter.  
