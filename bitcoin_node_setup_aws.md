# Setting up a Bitcoin Full Node in AWS
- sources: see end of doc for references used and helpful sources around the edges. wolfmcnally.com guide is responsible for guiding most of this setup
- disclaimer: the steps below are not inclusive of all due diligence required by any user following this guide regarding security hardening for the AWS infra around the node, the instance hosting the node, the node itself, and anything else related. The focus of this guide is getting a btc node and various features up and running, not securing the node or related infra, and this guide makes no claims on producing a secured bitcoin node, just on producing a functional one.  

## Set up AWS Account

This will gloss over some AWS account setup due diligence and steps, as my AWS homelab has been in existence for a few years. 

### Non-root IAM User
First, make a IAM user with the ability to build a node in AWS. If this is the first user made after creating an AWS account and related root user, the following steps are a way to make one.

- Login as root user
- Navigate to IAM

### Billing Alarm
Bitcoin nodes can use notable storage (500 GB to store the blockchain, likely more (not sure yet) to store all transactions) and network throughput. I hosted it in AWS for ease of use and to incorporate AWS architecture and crypto-specific cloudsec into this as well, but that likely will incur additional charges too. 

Solution here is to make a billing alarm: https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/monitor_estimated_charges_with_cloudwatch.html

So far, 7 days into running the node, I've incurred about $10 USD of billing (2022).

### Enable GuardDuty
I am interested in alerting that cloud-native threat detection/security products like GuardDuty produce when deployed into a cryptocurrency-focused infrastructure. So, enable GuardDuty early on. 



## Set up Instance

## Set up Node

## Further Configure Node 




## Sources
- https://wolfmcnally.com/115/developer-notes-setting-up-a-bitcoin-node-on-aws/
    - Largely worked, but diverged in a few areas. Most changes I did were based on changes to 
    BTC core since pub data of 2018 of this blog