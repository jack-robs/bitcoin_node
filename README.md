# bitcoin_node

## What
- Hosting a bitcoin full node in AWS
- Setting up monitoring capabilities (prometheus/grafana, SIEM ingestion, etc) 
  - prom/graph: in progress
  - SIEM/security-oriented: TODO
- Building limited apps using bitcoin's RPC 
  - TODO
- Figuring out AWS architecture around the node
  - network/app arch to support the node
  - security monitoring in AWS oriented towards btc-specific

## Why
- Get under the hood on bitcoin/cryptocurrency/blockchain abstractions, ID how the various softwares/daemons/nodes function 
- Expand detection/respones engineering, cyber threat intel, and security frameworks knowledge base for bitcoin/cryptocurrencies
  - How or why to inject chain data into a SIEM, what detections to build
  - Support knolwedge base of MITRE ATT&CK/OWASP Top 10 style frameworks for bitcoin/cryptocurrency
    - Existing examples that are cool:
      - DeFi Threat Matrix: https://github.com/manifoldfinance/defi-threat
      - SWC Smart Contract appsec registry: https://swcregistry.io/
- Stretch: learn and expand DevOps and DevSecOps capabilities and knowledgebase for running BTC/cryptocurrency-specific software

## Where 
- AWS account

## How
- hosting the node (see: aws_bitcoin_setup directory for detailed steps): 
  - Infra: AWS account w/ a created IAM user login (vs. root user)
  - Node setup guide(s) used: 
    - AWS-specific guidance for using an instance + additional EBS volume to store blockchain 
      - https://wolfmcnally.com/115/developer-notes-setting-up-a-bitcoin-node-on-aws/
    - Troubleshooting network connections for the instance:
      - https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstancesConnecting.html
    - Setting up AWS CLI for getting basic node data:
      - basic AWS setup:
      - per-service commands to use:
        - EBS volumes: https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-volumes.html
        - EC2 instance: https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html
    - Querying `bitcoind`, the node
      - basic familiarization and node architecture map: https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch03.asciidoc
      - API querying w/ rpc: https://en.bitcoin.it/wiki/API_reference_(JSON-RPC)
    - Node troubleshooting (running list)
      - Challenge: during initial block sync, bitcoind keeps crashing, initial block count sync stuck at a certain high-ish number once restarted, will start syncing via `bitcoin-cli getblockcount` once bitcoind restarts, bitcoind crashes, restart back at certain high-ish number
        - Possible issue: additioanl EBS volume setup from before (above: **+additional EBS volume to store...**) not enough space, needs to be made bigger (500 GB or so) to store full blockchain. Specific issue I had was a 300 gb EBS volume from the wolfmcnally link from 2018, but need about 500GB for current chain size
        - ID the issue: 
          - run `bitcoind -printtoconsole`, will possibly see a ~"Error: not enough space" output
          - run `$ df -hT`, will see the EBS volume store the chainstate at 99%-ish full
          - Example (after resizing to 500gb from 300gb, using Solution Steps below, note 74% full)
          ```
          user@ip-xxxx df -hT
Filesystem      Type      Size  Used Avail Use% Mounted on
...
/dev/nvme1n1p1  ext4      7.7G  1.6G  5.7G  22% /
....
#btc chainstate stored here, this is setup using wolfmcnalley steps above
/dev/nvme0n1    ext4      492G  344G  125G  74% /data
tmpfs           tmpfs     389M     0  389M   0% /run/user/1000
          ```
        - Solution steps:
          - Expand EBS volume to 500GB: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/requesting-ebs-volume-modifications.html
          - Expand FS on the node to recognize the additional space: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/recognize-expanded-volume-linux.html
          - run `df -hT` again, see lower % full of the FS storing the blockchain. Changes should reflect quickly (10-20 seconds for me). 


