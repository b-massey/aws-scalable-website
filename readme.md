# aws-scalable-website

This is repository providing templates to create a scalable AWS web solution. Included are

  - CFN templates to create base 
  - Template wesite site files
  
# Step 1 
Create VPC Stack

The folder cfn-templates has the required files to create the VPC and related components
vpc-stack.yml: the CloudFormation template to create the base VPC, Subnets, NAT Gateways, etc which will be used by the main stack.
vpc-params.json: is the parameters file which contains the parameter values for the CFN template. Update the values as appropriate if required
Go to root directory of repo and execute the following AWS CLI command to create CloudFormation stack for VPC.

```
aws cloudformation create-stack --stack-name TestSite-BaseStack --template-body file://cfn-templates/vpc-stack.yml --parameters file://cfn-templates/vpc-params.json
```

# Step 2

CFN template for website is desgined to download the website files from an S3 bucket. The Sample website files are available in the directory 'site-files'. To upload the files to S3,  go to the root directory and execute the  AWS CLI Commands 

Create a new S3 bucket:
```
aws s3 mb s3://my-testwebsite-files
```

Copy files to newly s3 bucket
```
aws s3 cp ./site-files s3://my-testwebsite-files --recursive
```
# Step 3
Create the Website stack

The folder cfn-templates has the required files to create the stack to host the site  and required components.  
cfn-scalable-website.yml: the CloudFormation template to create the components of a stack hosting a sclabale web application.
teststack-params.json: is the parameters file which contains the parameter values for the CFN template. Update the values for the *VpcId, PrivateSubnet1, PrivateSubnet2, PublicSubnet1, PublicSubnet2* based on the values in the output section of the base VPC stack created in Step 1. Update *WebsiteFilesS3Bucket* to the S3 bucket created in Step 2. Update *KeyPairName* value with an existing key pair or create a new key pair and use it.

Go to root directory of repo and execute the following AWS CLI command to create CloudFormation stack for VPC.

```
aws cloudformation create-stack --stack-name TestSite-MainStack --template-body file://cfn-templates/cfn-scalable-website.yml --parameters file://cfn-templates/teststack-params.json --capabilities CAPABILITY_IAM
```