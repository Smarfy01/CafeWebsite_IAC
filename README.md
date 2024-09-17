**Deploying a Static Website with AWS CloudFormation**

Overview
Deploying cloud services can be daunting, especially when using the traditional manual approach with the AWS Management Console. 
Although the UI may seem user-friendly, the numerous configuration options and default settings can be overwhelming. 
This guide demonstrates how to leverage AWS CloudFormation to simplify the deployment of a static website. 
By using Infrastructure as Code (IaC), CloudFormation enables you to define and manage AWS resources through code, ensuring 
consistency and reducing the risk of human error.

In this guide, we will complete the deployment in just 7 steps, showcasing how CloudFormation can streamline managing cloud resources.

**Steps to Deploy the CloudFormation Template from VSCode Bash Terminal**

**STEP 1: IAM User with Admin Rights**

Ensure you have an IAM user with admin access. Download the credentials for this user, as they will be used to configure the AWS CLI.

**STEP 2: Create a New Working Directory**
Create a new directory named CafeWebsite_IAC to organize your CloudFormation template and website files. This helps keep your project structured.

bash
Copy code
mkdir CafeWebsite_IAC
cd CafeWebsite_IAC

**STEP 3: Prepare the CloudFormation Template**
Create a new file named s3-cafe-website.yaml in your working directory. This file will contain the CloudFormation 
template for your S3 bucket and website configuration. Copy and paste the following code into the file:

yaml
Copy code
AWSTemplateFormatVersion: "2010-09-09"
Description: "Template to create and configure S3 bucket for static website hosting."

Resources:
  WebsiteConfiguration:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: cafebucket-smarfy # Use a unique name identifier when naming your S3 bucket
      WebsiteConfiguration:
        IndexDocument: index.html
        ErrorDocument: error.html

Outputs:
  BucketName:
    Description: "The S3 bucket name."
    Value: !Ref WebsiteConfiguration  # Outputs the bucket name
  WebsiteURL:
    Description: "The URL of the static website hosted on the S3 bucket."
    Value: !Sub "http://${WebsiteConfiguration}.s3-website-${AWS::Region}.amazonaws.com"  # Constructs the website URL
    
**STEP 4: Configure the AWS CLI**
In your VSCode bash terminal, run the following command to set up the AWS CLI with the credentials of the IAM user:

bash
Copy code
aws configure
Validate the CloudFormation template:

bash
Copy code
aws cloudformation validate-template --template-body file://s3-cafe-website.yaml
Deploy the stack:

bash
Copy code
aws cloudformation create-stack --stack-name WebsiteBucket-stack --template-body file://s3-cafe-website.yaml
STEP 5: Download the Website Files
Clone the source code to download the website files:

bash
Copy code
git clone https://github.com/smarfy01/cafe-static-website.git
STEP 6: Upload Website Files to the S3 Bucket
Set the public access block settings on the bucket:

bash
Copy code
aws s3api put-public-access-block --bucket cafebucket-smarfy --public-access-block-configuration "BlockPublicAcls=false,RestrictPublicBuckets=false,IgnorePublicAcls=false,BlockPublicPolicy=false"
Change your working directory to the cloned source code folder:

bash
Copy code
cd cafe-static-website
Upload the website files to the S3 bucket and set their ACL policy to public-read:

bash
Copy code
aws s3 cp --recursive . s3://cafebucket-smarfy/ --acl public-read
You should see upload messages indicating that files are being uploaded.

STEP 7: Retrieve the Website URL from the CloudFormation Output
After successfully uploading the website files, retrieve the output parameters:

bash
Copy code
aws cloudformation describe-stacks --stack-name WebsiteBucket-stack --query "Stacks[0].Outputs"
This command will return the WebsiteURL, which you can use to access your static website hosted on S3.

Conclusion
This guide walks you through deploying a static website with AWS CloudFormation from the VSCode bash terminal. You’ve configured the AWS CLI, deployed the CloudFormation stack, downloaded and uploaded website files, and retrieved the website URL.

Feel free to modify and adapt this guide for your own use. Thank you!
