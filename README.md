AWS Managed Hyperledger Fabric QuickStart  
=========================================
This solution shows you how to create an AWS Managed Hyperledger Fabric and how to deploy some sample chaincode. This project updates an article "Building and deploying an application for Hyperledger Fabric on Amazon Managed Hyperledger Part 1"referenced below and but provides a more basic step by step process.

![AWS Managed Hyperledger Fabric](images/AWS-Managed-Blockchain-2.png?raw=true "AWS Managed Hyperledger Fabric")

Steps:  
* [Create Amazon Managed Hyperledger](#create-amazon-managed-hyperledger-and-first-member)  
* [Create Amazon Managed Hyperledger Fabric Peer Node](#create-amazon-managed-hyperledger-fabric-peer-node)  
* [Create AWS EC2 Hyperledger Console Instance and Configure](#create-aws-ec2-hyperledger-console-instance-and-configure)  
* [Create AWS EC2 Hyperledger Fabric Client Node](#create-aws-ec2-hyperledger-fabric-client-node)  
* [Configure AWS EC2 Hyperledger Fabric Client Node and Build Blockchain Network](#configure-aws-ec2-hyperledger-fabric-client-node-and-build-blockchain-network)  
* [Using the AWS EC2 Hyperledger Fabric Client Node](#using-the-aws-ec2-hyperledger-fabric-client-node)  
* [Remove Your Amazon Managed Hyperledger Fabric Network](#remove-your-amazon-managed-hyperledger-fabric-network)  

## Create Amazon Managed Hyperledger and First Member
Use the AWS Console to configure the Amazon Managed Hyperledger.  This is a step by step process.

### Amazon Managed Blockchain Dashboard
Click on "Create a network"  

Hyperledger frameworks
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
Note: Should take about 10 - 20 minutes to complete.  Wait till you see "Available" appear in "Status" column.  
```
## Create Amazon Managed Hyperledger Fabric Peer Node
Use the AWS Console to configure the Amazon Managed Hyperledger Fabric Peer Node.  This is a step by step process.

### Amazon Managed Hyperledger Dashboard
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
Note: Should take about 2 minutes to complete.
Wait till you see "Available" appear in "Status" column under "Peer Nodes".
```

## Create AWS EC2 Hyperledger Console Instance and Configure
Use AWS Console to configure the an EC2 Instance for blockchain fabric console.  This is a step by step process.

### AWS EC2 Dashboard  
Click on "Launch Instance"  
Click on "Quick Start"  
```
Amazon Linux 2 AMI (HVM), SSD Volume Type 
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
Value: hyperledger-fabric-console
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
See contents of "/tmp/install-blockchain-support" it should say "installation complete".

### Configure AWS CLI
Configure the Access Key, Secret Key, and region.
```
aws configure
AWS Access Key ID []: <Your Access Key ID>
AWS Secret Access Key []: <Your Secret Access Key>
Default region name []: us-east-1
```
### Test aws cli
Test aws cli is configured properly by doing a simple test
```
aws s3 ls
```

## Create AWS EC2 Hyperledger Fabric Client Node
Use the EC2 Hyperledger Console Instance to create the AWS EC2 Hyperledger Fabric Client Node.  This step  
uses a CloudFormation script to build Fabric Client Node.  This is a step by step process.  

### Connect to EC2 Hyperledger Console Instance
Using ssh from your local machine, connect to your AWS EC2 Instance tagged "hyperledger-fabric-console"  

### Configure BlockChain Environment
Configure BlockChain Fabric environment variables   
```
NOTE:  There is a script in /home/ec2-user called "configure-blockchain-environment".  
       You may run this script to automate the creation and population of your environment 
       variables.  It uses the naming convention I specified in this HOW-TO.  So if you didn't
       use the my naming convention it won't work.
```
```       
source configure-blockchain-environment
```
### Run CloudFormation Script to Create Fabric Client Node
Launches a AWS CloudFormation script to create the Fabric Client Node
```
cd ~/non-profit-blockchain/ngo-fabric
./3-vpc-client-node.sh
```
Check the progress in the AWS CloudFormation console and wait until stack CREATE COMPLETE.  
```
Click on the "Outputs" Tab and copy the value of the EC2URL Public DNS
``` 

## Configure AWS EC2 Hyperledger Fabric Client Node and Build Blockchain Network  

### ssh into the Fabric Client Node
You'll need to ssh into the AWS EC2 Hyperledger Fabric Client Node from your hyperledger-fabric-console.
You can use the aws cli to get the ec2url to make it easier or you can just use "EC2URL Public DNS"
you copied from the step above.
```
cd ~
ec2url=$(aws cloudformation describe-stacks --query Stacks[].Outputs[5].OutputValue --output text)
ssh -i MyFabric-keypair.pem -o "StrictHostKeyChecking no" ec2-user@$ec2url
```
### Configure AWS CLI
You only need to set the region environment  
```
aws configure

AWS Access Key ID [None] :
AWS Secret Access Key [None] :
Default region name [] us-east-1
Default output format [None] :
```
### Test aws cli
Test aws cli is configured properly by doing a simple test
```
aws s3 ls
```
### Upgrade AWS CLI
Upgrade the AWS Cli on the Fabric Client Node
```
sudo pip install awscli --upgrade
```
### Configure BlockChain Environment and Build the Hyperledger Fabric Blockchain Network
Configure BlockChain Fabric environment variables and build the Hyperledger Fabric blockchain network 
```
NOTE:  There is a script in /home/ec2-user called "configure-blockchain-environment" and 
       "build-blockchain-fabric-network".  You may run these scripts to automate the
       creation and population of environment variables as well as install a test Blockchain
       chaincode.  It uses the naming convention I specified in this HOW-TO.  So if you didn't
       use my naming convention it won't work.
```
#### Configure Blockchain Fabric Environment Variables
Set the environment variables
```       
cd ~
git clone https://github.com/kskalvar/aws-blockchain-fabric-quickstart.git  

cp ~/aws-blockchain-fabric-quickstart/scripts/configure-blockchain-environment ~  
chmod 777 ~/configure-blockchain-environment  

source configure-blockchain-environment
```
#### Build Blockchain Fabric Network.
This will not only install the sample test chaincode but run test against the chaincode and network as well.  
If all goes well your network will be up and running.    
```
cp ~/aws-blockchain-fabric-quickstart/scripts/build-blockchain-fabric-network ~  
chmod 777 ~/build-blockchain-fabric-network  

./build-blockchain-fabric-network
```
## Using the AWS EC2 Hyperledger Fabric Client Node
After the Hyperledger Fabric Network has been created you can log into the AWS EC2 Hyperledger Client Node      
at a later date and perform operations on the Blockchain Fabric Network.  This is a step by step process.  

### Connect to AWS EC2 Hyperledger Fabric Client
You'll need to ssh into the the EC2 instance tagged "ManagedHyperledgerWorkshopEC2ClientInstance".  
```
cd ~
ec2url=$(aws cloudformation describe-stacks --query Stacks[].Outputs[5].OutputValue --output text)
ssh -i MyFabric-keypair.pem -o "StrictHostKeyChecking no" ec2-user@$ec2url
```

### Set Blockchain Fabric Environment
Set environment variables
```       
source configure-blockchain-environment
source /home/ec2-user/non-profit-blockchain/ngo-fabric/fabric-exports.sh
source /home/ec2-user/peer-exports.sh
```
### Query the Blockchain Fabric Network
Query the Fabric Network to see if you are configured properly
```
docker exec -e "CORE_PEER_TLS_ENABLED=true" \
            -e "CORE_PEER_TLS_ROOTCERT_FILE=/opt/home/managedblockchain-tls-chain.pem" \
            -e "CORE_PEER_ADDRESS=$PEER" -e "CORE_PEER_LOCALMSPID=$MSP" \
            -e "CORE_PEER_MSPCONFIGPATH=$MSP_PATH" \
            cli peer chaincode query -C $CHANNEL -n $CHAINCODENAME -c '{"Args":["query","a"]}'
```
## Remove Your Amazon Managed Hyperledger Fabric Network 

### AWS CloudFormation
Delete "MyFabric-fabric-client-node" Stack  

### Amazon Managed Hyperledger Dashboard
Click on "Networks"  
Click on "MyFabric"   
Click on "Members" tab  

Locate "Members owned by you"    
Select "KAL-Technology"  
Click on "Delete member"  
```
Note: This will delete the peer node, the member, and finally, the Fabric network.
Should take a couple of minutes to complete.
```
### AWS EC2 Dashboard
#### Remove blockchain-console instance
Click on "Instances/Instances" on the left hand menu  
Delete "hyperledger-fabric-console" Instance  

#### Remove MyFabric-keypair
Click on "Network & Security/Key Pairs" on the left hand menu  
Select "MyFabric-keypair"  
Click on "Delete" 

#### Remove launch-wizard-1 Security Group
Click on "Network & Security/Security Groups" on the left hand menu  
Select "launch-wizard-1"  
Click on "Delete" 

## References

Building and deploying an application for Hyperledger Fabric on Amazon Managed Hyperledger  
https://github.com/aws-samples/non-profit-blockchain/blob/master/ngo-fabric/README.md 

Amazon Managed Blockchain How to build an application with Amazon Managed Blockchain
https://www.youtube.com/watch?v=1br20axMBxw

Michael Edge edgema@amazon.com  
Sr. Blockchain Architect  
AWS Professional Services



