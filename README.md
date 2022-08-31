# mongodb-cf-standalone

This repository contains AWS CloudFormation scripts which will launch
a standalone MongoDB Community Edition 5.X server. This script has the [Production Notes](https://www.mongodb.com/docs/v5.0/administration/production-notes/) applied. 

## Notes

* Authentication has been enabled
* Creates and encrypted data volume and mounts it in XFS format mounted
* MongoDB Data are stored at `/data/db`
* MongoDB Logs are stored at `/data/logs`
* Binds to all IP Addresses

## Parameters

To use this script you will need to provide the following:

* **VPC**: VPC-ID of your existing Virtual Private Cloud (VPC) where you want to depoy MongoDB cluster.
* **MongoDBSubnet**: A subnet within your VPC that you want to deploy to. These can be found within the VPC section of AWS.
* **KeyName**: The name of an existing EC2 KeyPair. This can be found/created in the EC2 section of the AWS console.
* **MongoDBInstanceType**: Amazon EC2 instance type for the MongoDB Instance. Default is `m5.large`
* **MongoDBAdminPassword**: The password for the initial MongoDB User.
* **MongoDBNameTag**: The name for your MongoDB Instance

## Optional Parameters

* **MongoDBAdminUsername**: The script creates an initial admin MongoDB User. The default is `admin`
* **Iops**: Iops of EBS volume when io1 type is chosen. Otherwise ignored
* **VolumeSize**: EBS Volume Size (data) to be attached to node in GBs. Default is `25`
* **VolumeType**: EBS Volume Type (data) to be attached to node [io1,gp2]. Deafault is `io1`

## Example Script

```shell
export STACK_NAME=<YOUR STACK NAME>
export AWS_VPC=<YOUR VPC ID>
export AWS_KEY_NAME=<YOUR KEY PAIR NAME>
export AWS_INSTANCE_TYPE=<YOUR INSTANCE TYPE>
export AWS_SUBNET=<YOUR SUBNET INSIDE YOUR VPC>
export MONGODB_NODE_NAME=<YOUR NODE NAME>
export MONGODB_ADMIN_PASSWORD=<YOUR PASSWORD>

aws cloudformation deploy --stack-name $STACK_NAME \
  --template-file mongodb-standalone.yml \
  --parameter-overrides VPC=$AWS_VPC \
      KeyName=$AWS_KEY_NAME \
      MongoDBAdminPassword=$MONGODB_ADMIN_PASSWORD \
      MongoDBSubnet=$AWS_SUBNET \
      MongoDBInstanceType=$AWS_INSTANCE_TYPE \
      MongoDBNameTag=$MONGODB_NODE_NAME \
  --capabilities CAPABILITY_NAMED_IAM \
  --no-fail-on-empty-changeset \
  --region us-east-1
```