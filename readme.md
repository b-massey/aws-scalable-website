# aws-scalable-website

This repository provides AWS cloud formation templates to create sample stack to host a sclable web sites. Included files are

  - CFN templates to create VPC Stack, aand main website stack and related components
  - Template wesite site files
 

# Create the Stack
Follow the steps 

### Step 1 
Create VPC Stack

The folder cfn-templates has the required files to create the VPC and related components
vpc-stack.yml: the CloudFormation template to create the base VPC, Subnets, NAT Gateways, etc which will be used by the main stack.
vpc-params.json: is the parameters file which contains the parameter values for the CFN template. Update the values as appropriate if required
Go to root directory of repo and execute the following AWS CLI command to create CloudFormation stack for VPC.

```
aws cloudformation create-stack --stack-name TestSite-BaseStack --template-body file://cfn-templates/vpc-stack.yml --parameters file://cfn-templates/vpc-params.json
```

### Step 2

CFN template for website is desgined to download the website files from an S3 bucket. The Sample website files are available in the directory 'site-files'. To upload the files to S3,  go to the root directory and execute the  AWS CLI Commands 

Create a new S3 bucket:
```
aws s3 mb s3://my-testwebsite-files
```

Copy files to newly s3 bucket
```
aws s3 cp ./site-files s3://my-testwebsite-files --recursive
```
### Step 3
Create the Website stack

The folder cfn-templates has the required files to create the stack to host the site  and required components.  
cfn-scalable-website.yml: the CloudFormation template to create the components of a stack hosting a sclabale web application.
teststack-params.json: is the parameters file which contains the parameter values for the CFN template. Update the values for the *VpcId, PrivateSubnet1, PrivateSubnet2, PublicSubnet1, PublicSubnet2* based on the values in the output section of the base VPC stack created in Step 1. Update *WebsiteFilesS3Bucket* to the S3 bucket created in Step 2. Update *KeyPairName* value with an existing key pair or create a new key pair and use it.

Go to root directory of repo and execute the following AWS CLI command to create CloudFormation stack for VPC.

> _Note The CFN template is configured to accept only T2 instance types, in regions eu-west-1,eu-west-2, us-west-1, us-east-2, us-east-1, and us-east-2._
```
aws cloudformation create-stack --stack-name TestSite-MainStack --template-body file://cfn-templates/cfn-scalable-website.yml --parameters file://cfn-templates/teststack-params.json --capabilities CAPABILITY_IAM
```

# Application Maintenance

### Monitoring

The CloudWatch Alarm 'TestWebsite ALB Healthy Host Count' monitors the healthy host count, a SNS notificattion can be created to be notified if this in in _Alarm_ state.

### Scaling

* __At Stack Creation__ - The stack is configured to create one web instance at creation. To Change thi, update the paramter *WebServerCapacity* to new value (If more than 5 instances are required at launch time, change _MaxSize_ in the Auto Scaling configuration will need t be updated as well)
* __Manually__ - Change the 'Desired', 'Min,. 'Max', parameters on the auto scaling group as appropriate
* __By CPU Usage__ - the Cloud watch Alam 'CPUUsageAlarmForTestWebsiteALB' linked to auto scaling group is by default set to scale up, if an instance is running about 50% CPU. This can be modified at the stack creation or at runtime on AWS console
