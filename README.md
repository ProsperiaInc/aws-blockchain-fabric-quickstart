AWS Managed Blockchain QuickStart  
===============================================
This solution shows how to create an AWS Managed Fabric Blockchain and deploy a sample application. This readme updates an article "Building and deploying an application for Hyperledger Fabric on Amazon Managed Blockchain" referenced below and provides a more basic step by step process.

Steps:  

    
  Create Amazon Managed Blockchain    
  Create Amazon Managed Blockchain Fabric Peer  
  Create AWS EC2 Blockchain Console Instance   
  Create Amazon Managed Blockchain Fabric Client Node    
  Prepare the Amazon Managed Blockchain Fabric Client Node and Enroll Identity  
  
  Remove Your Amazon Managed Blockchain    

## Create Amazon Managed Blockchain 
Use the AWS Console to configure the Amazon Managed Blockchain.  This is a step by step process.

### Amazon Managed Blockchain Dashboard
Click on "Create a network"  

Blockchain frameworks
```
Hyperledger Fabric 1.2
```  
Network edition
```
Starter
```
Network name and description
```
Network name: MyFabric
```
Click on "Next"  

Member configuration
```
Member name: KAL-Technology
```
Hyperledger Fabric certificate authority (CA) configuration  
```
Admin username: admin
Admin password: Administration01
```
Click on "Next"  
Click on "Create network and member"  
```
Note: Should take about 20 minutes to complete.  Wait till you see "Available" appear in "Status" column.  
```
## Create Amazon Managed Blockchain Fabric Peer
Use the AWS Console to configure the Amazon Managed Blockchain Fabric Peer.  This is a step by step process.

### Amazon Managed Blockchain Dashboard
Click on "Networks"  
Click on "MyFabric"   
Click on "Members" tab 
 
Locate "Members owned by you"  
Select "KAL-Technology"  
Click on "Create peer node"    

Create peer node 
```
Blockchain instance type: bc.t3.small
Availability Zone: us-east-1a
```
Click on "Create peer node"  
```
Note: Should take a couple of minutes to complete.  Wait till you see "Available" appear in "Status" column under "Peer Nodes".
```

## Create AWS EC2 Blockchain Console Instance
Use AWS Console to configure the an EC2 Instance for blockchain fabric console.  This is a step by step process.

### AWS EC2 Dashboard  
Click on "Launch Instance"  
Click on "Quick Start"  
```
Amazon Linux 2 AMI (HVM), SSD Volume Type - ami-0cc96feef8c6bbff3 
```  
Click on "Select"

Choose Instance Type
```
t2.micro
```
Click on "Next: Configure Instance Details"  
Expand Advanced Details
```
User data
Select "As text"
Cut and Paste contents of file from "aws-blockchain-quickstart/cloud-init/cloud-init" in github 
```  
Click on "Next: Add Storage"  
Click on "Next" Add Tags"  
Click on "Add Tag"
```
Key: Name
Value: blockchain-console
```
Click on "Next: Configure Security Group"  
Click on "Review and Launch"    
Click on "Launch"  
```
Note:  Be sure select an "Choose an existing key pair" or "Create a new key pair"
```
### Connect to EC2 Instance
Using ssh from your local machine, connect to your AWS EC2 Instance
```
ssh -i <AWS EC2 Private Key> ec2-user@<AWS EC2 Instance IP Address>
```

### Check to insure cloud-init has completed
See contents of "/tmp/install-eks-support" it should say "installation complete".

### Configure AWS CLI
aws configure
```
AWS Access Key ID []: <Your Access Key ID>
AWS Secret Access Key []: <Your Secret Access Key>
Default region name []: us-east-1
```
### Test aws cli
Test aws cli is configured properly by doing a simple test
```
aws s3 ls
```

## Create Amazon Managed Blockchain Fabric Client Node
Use the AWS Console to configure the Amazon Managed Blockchain Fabric Client node.  This is a step by step process.  

### Connect to EC2 Blockchain Console Instance
Using ssh from your local machine, connect to your AWS EC2 Instance tagged "blockchain-console"  

### Configure BlockChain Environment
Configure BlockChain Fabric environment variables   
```
NOTE:  There is a script in /home/ec2-user called "configure-blockchain-environment".  
       You may run this script to automate the creation and population of environment 
       variables.  It uses the naming convention I specified in this HOW-TO.  So if you didn't
       use the naming convention it won't work.
       
source configure-blockchain-environment
```
### Run CloudFormation Script to Create Fabric Client Node
Launches a AWS CloudFormation script to create the Fabric Client Node
```
cd ~/non-profit-blockchain/ngo-fabric
./3-vpc-client-node.sh
```
Check the progress in the AWS CloudFormation console and wait until the stack is CREATE COMPLETE.  
Click on the "Outputs" Tab and copy the value of the EC2URL Public DNS of the EC2 instance 

## Prepare the Amazon Managed Blockchain Fabric Client Node and Enroll Identity
You'll need to ssh into the the EC2 instance tagged "ManagedBlockchainWorkshopEC2ClientInstance".  
```
cd ~
aws cloudformation describe-stacks --query Stacks[].Outputs[5].OutputValue --output text
ssh -i MyFabric-keypair.pem ec2-user@<EC2URL Public DNS of the EC2 instance>
```
### Configure AWS CLI
You only need to set the region environment
```
export AWS_DEFAULT_REGION=us-east-1
```
### Test aws cli
Test aws cli is configured properly by doing a simple test
```
aws s3 ls
```
### Upgrade AWS CLI
Upgrade the AWS Cli on the Fabric Client
```
sudo pip install awscli --upgrade
```
### Configure BlockChain Environment
Configure BlockChain Fabric environment variables  
```
NOTE:  There is a script in /home/ec2-user called "configure-blockchain-environment".  
       You may run this script to automate the creation and population of environment 
       variables.  It uses the naming convention I specified in this HOW-TO.  So if you didn't
       use the naming convention it won't work.
       
cd ~
git clone https://github.com/kskalvar/aws-blockchain-fabric-quickstart.git  

cp ~/aws-blockchain-fabric-quickstart/scripts/configure-blockchain-environment ~  
chmod 777 ~/configure-blockchain-environment  

cp ~/aws-blockchain-fabric-quickstart/scripts/build-blockchain-fabric-network ~  
chmod 777 ~/build-blockchain-fabric-network  

source configure-blockchain-environment
./build-blockchain-fabric-network
```
## Remove Your Amazon Managed Blockchain 

### AWS CloudFormation
Delete "MyFabric-fabric-client-node" Stack  

### Amazon Managed Blockchain Dashboard
Click on "Networks"  
Click on "MyFabric"   
Click on "Members" tab  

Locate "Members owned by you"    
Select "KAL-Technology"  
Click on "Delete member"  
```
Note: This will delete the peer node, the member, and finally, the Fabric network. Should take a couple of minutes to complete.
```
### AWS EC2 Dashboard
##### Remove blockchain-console instance
Click on "Instances/Instances" on the left hand menu
Delete "blockchain-console" Instance  

#### Remove MyFabric-keypair
Click on "Network & Security/Key Pairs" on the left hand menu  
Select " MyFabric-keypair"  
Click on "Delete"  

## References

Building and deploying an application for Hyperledger Fabric on Amazon Managed Blockchain  
https://github.com/aws-samples/non-profit-blockchain