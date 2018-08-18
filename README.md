# Hail 0.2 on Amazon EMR: `cloudformation` tool 

This `cloudformation` tool creates an EMR cluster under an **emr-5.13.0** release using `Spark 2.3.0` and `Hadoop 2.8.3`. In addition, it installs `Hail 0.2` and `JupyterNotebook` in all the instances of the cluster. 

## How to use this tool

 Open your terminal in MAC or Linux and clone this repository: `git clone https://github.com/hms-dbmi/hail02-on-EMR`. 
 
1. `cd` into the `hail02-on-EMR/src` folder and with the text editor of your preference open the configuration file: `hail02_EMR.yaml`. This file will be used to provide information necessary to create the cluster. Fill in the fields as necessary using your personal key information and save your changes. See configuration details below:

```yaml
config:
  EMR_CLUSTER_NAME: "my-hail-02-cluster" # Give a name to the EMR cluster
  KEY_NAME: "my-key" # Input your key name DO NOT include the .pem extension
  PATH_TO_KEY: "/full-path/to-key/" # Full path to .pem file
  INSTANCE_TYPE: "c4.8xlarge" # Select the instance type, see table below
  CORE_COUNT: "3" # Number of cores. Additional reference in the EC2 FAQs website 
  SUBNET_ID: "subnet-12345" # Select you private subnet. See the EC2 FAQs website
  SLAVE_SECURITY_GROUP: "sg-fromYourPemKey" # PEM key specific. See the EC2 key pairs website
  MASTER_SECURITY_GROUP: "sg-fromYourPemKey" # PEM key specific. See the EC2 key pairs website
  EC2_NAME_TAG: "my-hail-EMR" # Tags for the individual EC2 instances
  OWNER_TAG: "emr-owner" # EC2 owner tag
  PROJECT_TAG: "my-project" # Project tag
  S3_BUCKET: "s3n://my-s3-bucket/" # Input your project's S3 bucket
```
This script is defaulted to region `us-east-1`, instances `c4.8xlarge` : 3 `cores` and 1 `master`. For additional configuration details regarding the **emr-5.13.0** release, visit: <https://console.aws.amazon.com/elasticmapreduce/home?region=us-east-1#quick-create\:>. 

|Suggested **`INSTANCE_TYPE`s** |
|:-------------------------:| 
| m4.large | 
| m4.xlarge | 
| m4.2xlarge | 
| m4.4xlarge | 
| m4.10xlarge | 
| m4.16xlarge | 
| c4.large | 
| c4.xlarge | 
| c4.2xlarge | 
| c4.4xlarge | 
| c4.8xlarge | 
| r3.xlarge | 
| r3.2xlarge | 
| r3.4xlarge | 
| r3.8xlarge | 

See additional instance details at: https://aws.amazon.com/ec2/instance-types/

2. Grant executable permits to the `cloudformation` script by executing the following: `chmod +x hail_cloudformation_emr.sh`
3. Execute the command: `nohup ./hail_cloudformation_emr.sh &`. The EMR creation takes between 5-7 minutes. See the installation log file at `tail -f /tmp/cloudcreation_log.out`, exit by pressing &#8984;`+C` or `Ctrl+C`; the logs are available, under the same path, at both the local installation computer and at the master node of your EMR. 
4. You can check the status of the EMR creation at: https://console.aws.amazon.com/elasticmapreduce/home?region=us-east-1. The EMR is successfully created once it gets the status `Waiting`. After created, allow ~20 minutes for all the programs to install. All the programs are installed automatically. 
5. To obtain the **DNS** (to `ssh` in to the master node) and the **public IP** of the Master node (required to connect to the `JupyterNotebook`), from the terminal execute: 
```bash
tail -4 /tmp/cloudcreation_log.out | head -2
```

## Launching the `JupyterNotebook` with `Hail 0.2`

To launch the  `JupyterNotebook` you need the Master IP address (IPv4) that can be obtained from 1) the terminal by executing: `tail -3 /tmp/cloudcreation_log.out | head -1` or by 2) going to the AWS Management Console website and under `EC2 > Instances` selecting the corresponding `master` node then select the `description tab > Security Groups > view inbound rules`.

Paste the IP in a browser followed by a `:` and port 8192: `PUBLIC_IP_ADDRESS_MASTER_NODE:8192`; use password: *`avillach`* to login. And you are all set! 

### FAQs and troubleshooting 

* EC2 FAQs website: https://aws.amazon.com/ec2/faqs/#How_many_instances_can_I_run_in_Amazon_EC2

* EC2 key pairs website: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html

* If the EMR cluster does not start, make sure that your AWS credentials are properly setup: https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html

