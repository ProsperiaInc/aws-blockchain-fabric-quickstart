AWS Managed Blockchain QuickStart  
===============================================
This solution shows how to create an AWS Managed Blockchain and deploy a sample application. This readme updates an article "Building and deploying an application for Hyperledger Fabric on Amazon Managed Blockchain" referenced below and provides a more basic step by step process.  Unfortunately this is a pretty manual effort right now.


## Configure AWS EC2 Blockchain Console Instance
Use AWS Console to configure the EC2 Instance for kubectl.  This is a step by step process.

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
Network name: BlockChainDemo
Description: BlockChainDemo
```
Click on "Next"  
Member configuration
```
Member name: KAL-Technology
Description: KAL-Technology
```
Hyperledger Fabric certificate authority (CA) configuration  
```
Admin username: admin
Admin password: Administration01
```
Click on "Next"  
Click on "Create network and member"  

Note: Should take about 20 minutes to complete.  Wait till you see "Available" appear in "Status" column.  


## Create Amazon Managed Blockchain Fabric Peer
Use the AWS Console to configure the Amazon Managed Blockchain Fabric Peer.  This is a step by step process.

### Amazon Managed Blockchain Dashboard
Click on "Networks"  
Click on "BlockChainDemo"   
Click on "Members" tab 
 
Locate "Members owned by you" and Select "KAL-Technology"  
Click on "Create peer node"  
```
Blockchain instance type: bc.t3.small
Availability Zone: us-east-1a
```
Click on "Create peer node"  

Note: Should take a couple of minutes to complete.  Wait till you see "Available" appear in "Status" column under "Peer Nodes".

## Create Amazon Managed Blockchain Fabric Client Node
Use the AWS Console to configure the Amazon Managed Blockchain Fabric Client node.  This is a step by step process.  

### Connect to EC2 Instance
Using ssh from your local machine, connect to your AWS EC2 Instance


### Configure BlockChain Environment
Configure environment to launch CloudFormation script  
```
NOTE:  There is a script in /home/ec2-user called "configure-blockchain-environment".  
       You may run this script to automate the creation and population of environment 
       variables.  It uses the naming convention I specified in this HOW-TO.  So if you didn't
       use the naming convention it won't work.  If you do use the script then all
       you need to do is run the "Test Cluster" and "Test Cluster Nodes" steps.
       
source configure-blockchain-environment
```

### Run CloudFormation Script to Create Fabric Client Node
Check the progress in the AWS CloudFormation console and wait until the stack is CREATE COMPLETE.
You will find some useful information in the Outputs tab of the CloudFormation stack once the stack 
is complete. We will use this information in later steps.
```
cd ~/non-profit-blockchain/ngo-fabric
./3-vpc-client-node.sh
```

## Remove Your Amazon Managed Blockchain 

### AWS CloudFormation
Delete "BlockChainDemo-fabric-client-node" Stack  

### Amazon Managed Blockchain Dashboard
Click on "Networks"  
Click on "BlockChainDemo"   
Click on "Members" tab  

Locate "Members owned by you"    
Select "KAL-Technology"  
Click on "Delete member"  

Note: This will delete the peer node, the member, and finally, the Fabric network. Should take a couple of minutes to complete.

### AWS EC2
Delete "blockchain-console" Instance  

## References

Building and deploying an application for Hyperledger Fabric on Amazon Managed Blockchain
https://github.com/aws-samples/non-profit-blockchain