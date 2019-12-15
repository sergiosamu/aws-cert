# Course summary

## IAM
* IAM consists of
	* Users: de consola u/o de acceso programático
	* Groups: collection of several users. A user can belong to multiple groups
	* Roles:  role is a preset of policies for service(s). Cannot be assigned to users. It is assigned to a resource type and associate a set of policies
	* Policies: JSON format (effect, action and resource)
* IAM is universal (not apply to regions)
* Root account is the account created when AWS is first set up. I has admin access
* New users have no premissions when first created
* New users are assigned Access Key ID & Secret Access Keys.
	* These are no valid to login in the console, only por APIs and CLI. 
	* You can only see them once when they are created
* Always setup MFA on root accout
* Password rotation policy can be customized

## S3 

* Object based storage. No valid for installing SW or SO
* Files from 0 Bytes to 5 TB
* Unlimited storage
* Files stores in buckets
* S3 is an universal namespace. Names must be unique
* Succesful uploads return HTTP 200 code
* Al newly created buckets are Private. You can setup access control using Bucket policies nd ACLS
* Buckets can be configured to create access logs that can be stored in another bucket or another bucket in another account
* Fundamentals
	* key (name of the object)
	* Value (data)
	* Version Id (for versioning)
	* Metadata
* Read after Write consistency for PUTs of new Objects
* Eventual Consistency for overwrite PUTs AN DELETES (can take some time for propagate)
* Types of storage (set on first file upload)

	* S3 Standard>99.99% availability 99.999999999% durabilidty
	* S3 - IA: accessed infrequently but fast. Pay per access
	* S3 - One zone -IA: lower cost for infrequent access but without multiple availability zone
	* S3 - Intelligent tiering: automatically move data to the most cost-affective tier
	* S·3 - Glacier: secure, durable and low cost. Retrieval time configurable from minutes to hours
	* S3 - Glacier Deep Archive: Lowest cost. 12 hours of retrieval time
* Encrytion in transit archieved by ssl/tls
* Encryption at rest (server side) 
	* S3 Managed keys - SSE - S3
	* Key management service. Managed keys - SSE-KMS
	* Server Side Encryption with customer provided keys - SSE-C
* Client side encryption. Client encrypt files and upload them to Amazon
* Cross region replication 
	* Can be the same or different AWS accounts
	* Versioning must be enabled on both source and registration
	* Region must be unique
	* Existing files are not replicated automatically
	* Al subsecuent updates will be replicated automatically
	* Delete markers are not replicated
	* Deleting individual versions o delete markers will not be replicated
* Lifecicle policy
	* Automate moving objects between tiers
	* Can be used in conjunction with versioning
	* Can be applied to current versions and previous versions
	* Transitioning small objects to Glacier or Deep Archive will increase costs
* S3 Transfer acceleration
	* Edge locations
* Cloud front
	* Edge location: where content is cached
	* Origin: Source of files that CDN will distribute
	* Distribution: name given to CDN which consiss of a collection of Edge locations
	* Web Distribution: used for websites
	* RTPM: used for media streaming
	* Edge location are not READ only, you can write to them too
	* Object are cached for the TTL
	* You can clear objects by invalidating them but you will be charged
* Snowball:
	* Snowball can import or export from or to S3

* Storage gateway
	* File Gateway: for flat files. Stored directly on S3 (local cache for low-latency access)
	* Volume gateway (block storage)
	* Stored volumes: entire dataset stored on site and async backed up to S3 (point-in-time backups as Amazon EBS snapshots)
	* Cached volumes: Entire dataset stored on S3 and the most frequenly accesed data is cached on site
	* Gateway Virtual tape library

		* Used for backup and compatible with popular applications like NetBackup, Backup Exec, Veeam, etc....
* READ S3 FAQ 

## EC2
* 4 pricing models
	* On demand: pay a fixed rate for the hour
	* REserved: contract terms for 1 o 3  years. You get a discount on the hourly rate
	* Spot: depend on the market. You bid what you want to pay for an instace
	* Dedicated hosts: phiscal EC2 service dedicated. Server bound licenses (lower cost)
* If a spot instance is terminated by aws you don't get charged for the partial hour. If you terminate yourself, you will
* Instace types? (not important)
* EBS
	* Virtual hD on the cloud
	* Term. protection is off bya default
	* Default action for the root-EBS volume is to be deleted where the instance is terminated
	* EBS root volumes of your DEFAULT AMI's cannot be encrypted. You can use bitlocker to encrypt the root volume or this can be done by creating an AMI in the AWS console or using the API (ver lab?)
	* Additional volumes can be encrypted.
* Sec. groups
	* All inbound traffic is blocked by default
	* Al outbound traffic is allowed
	* Changes to Sec. groups take effect inmediately
	* You can have any number of EC2 instances within a sec. group
	* You can have multiple sec. groups attached to a EC2 instance
	* Are stateful
	* You cannot block specific IP addresses using sec groups, insted use NACL's
	* You can specify allow rules, but not deny rules
* SSD types
	* General purpose SSD
	* Provisioned IOPS SSD  (fastest)
	* Throughput Optimized HDD
	* Cold HDD (slowest)
	* EBS Magnetic
* Volumes exists on EBS (like virtual HD)
* Snapshots exist on S3 (photo of disk)
* Snapshots are point in time copies of Volumes
* Snapshots are incremental - only the blocks that have changed are moved to S3
* If this is your first snapshot, it may take some time to create
* To create a snapshot for Amazon EBS volumes that serve as root devices is recommended to stop the instance before taking it
* Yoy can create AMI's from both Volumes and Snapshots
* You can change EBS volume sizes on the fly, including changing the size and storage type
* Volumes will ALWAYS be in the same availability zone as the EC2 instance
* To move an EC2 volume from one AZ to another , take a snapshot of it, create an AMI from the snapshot and the use the AMI to launch the EC2 instance in a new AZ
* To move an EC2 colume to one region to another, take a snapshot of it, create an AMI from the snapshot and the copy the AMI from one region to another. The use the copied AMI to launch the new EC2 instance in the new region
* Snapshots of encrypted volumes are encrypted automatically
* Volumes restored from encrypted snapshots are encrypted automatically
* You can share snapshots, but only if they are unencrypted
* These snapshots can be shared with other AWS accounts or made public
* Root device volumes can now be encripted. If you have an unencrypted root device that needs to be encrypted, do the following:
	* Create a snapshot
	* Create a copy of the snapshot and select the encrypt option
	* Create an AMI from the encrypted Snapshot
	* Use the AMI to launch new encrypted instances
* EBS vs Instance store
	* Instance store volumes = ephimeral storage
	* Instance store volumes cannot be stopped. If the underlying host fails, you will lose data
	* EBS backed instances can be stopped without losing data
	* You can reboot both without losing data
	* By default both root volumes will be deleted on termination. However, with EBS volumes you can tell AWS to keep the root device volume on destination
* Cloudwatch
	* Used for monitoring performacne
	* Can monitor most of AWS as well as you application running on AWS
	* With EC2 it monitors events every 5 minutes by default
	* You can have one minute intervals by turning on defailed monitoring
	* You can create CW alarms which trigger notifications
	* CloudWatch is all about performance. CloudTrail is all about auditing
	* What can I do with It?
		* Dashboards
		* Alarms: depending of configured thredsholds
		* Events: helps you respondo to state changes in your aws resources
		* Log: helps you to aggregate, monitor and store
* CloudTrail vs CloudWatch
	* CloudWatch monitors performance
	* CloudTrail monitors API calls in the AWS platform
* CLI
	* you need to set up access in IAM (access key and secret access key)
* Roles
	* More secure than storing keys on individual EC2 instances
	* Are easier to manage
	* Can be assigned to an EC2 instance after it is created using both the console & command line
	* Are unversal - can be used in any region
* Bootstrap Scripts:
	* Run when an EC2 instance first boots
	* Powerful way of automating software installs and updates
* Instance Metadata and User Data
	* Useful to get info about and instance
	* curl http://x.x.x.x/latest/meta-data
	* curl http://x.x.x.x/latest/user-data
* EFS
	* NFS v4 is supported
	* Only pay for the storage you use
	* Can scale to petabytes
	* Supports thousands of concurrent NFS connections
	* Data is stored across mutiple AZ's within a region
	* Read after write consistency
* EC2 Placement groups
	* Three types
		* Clustered: Low network latency / High throughput
		* Spread: individual critical EC2 instances
		* Partitioned: mutiple EC2 instances: HDFS, HBase and Cassandra
	* A clustered one can't span multiple AZ's. Spread placement and partitioned can
	* The name of the placement group must be unique within your AWS account
	* Only certain types of instances can be launched in a placement group (Compute optimized, GPU, Memory Optimized, Storage Optimized)
	* AWS recommend homogeneous instances within clustered placement groups
	* You can't merge placement groups
	* You can't move an existing instance into a placement group. You can create an AMI from your existing instance and then launch a new instance from the AMI into the placement group

## Databases

* RDS (OLTP)
	* Soporte MySQL, PostgreSQL, Oracle, Aurora, MaríaDB
* DynamoDB (no SQL)
* Red Shiftp (OLAP)
* Elasticache
	* Memcached more simple Simple
	* Redis more complex objects and multiple availabilizy zones
* RDS runs on virtual machines. Cannot do ssh, nor login into SO
* Patching RDS OS and DBB is Amazon responisibility
* RDS is NOT serverless
* Aurora Serverless IS serverless
* DunamoDB is serverles
* Back up
    * Automated backups
	* Database snapshots
* Read replicas
	* Can be multi-AZ
	* Increase performance is main use case
	* Must have backups turned on
	* Can be in different regions
	* Can be MySQL, Postgres, MariaDB, Oracle and Aurora
* Can be promoted to master but this will break replication
* Mutlti A-Z
	* Used for DR not performance
	* You can force failover from one RDS instance to another by rebooting the RDS instance
* Encrytion a rest is sopoorted for MYSQL, Oracle, SQL Server Postgres, Maria DB and Aurora with KMS. Once the RDS instance is encrypted, the data stored at rest in the underlying storage is encrypted, as are its automated backups, read replicas and snapshots 
* DynamoDB
	* Serverless
	* Stored on SSD storage
	* Spread acorss 3 data centers
	* Eventual consistency read (default)
	* Strongly consistent reads
* Red Shift
	* One one AZ
	* For BI
	* Backups:
		* Enabled by default and 1 day retention period
		* Maximum retention is 35 days
		* Always attemp to maintain at least 3 copies of your data
		* Can replicate asynchronously your snapshots to S3 in another region for DR
* Aurora
	* 2 copies of your data containe in each AZ with minimum of 3 AZs. 6 copies of your data
	* You can share Aurora Snapshots with other AWS accounts
	* 2 types of replicas available: Aurora replicas and MySQL replicas. Automated failover is only available for Aurora replicas
	* Aurora has automated backups turned on by default. You can share these snapshots with other AWS accounts
* Elasticache
	* To increaase database and Web app performance
	* Redis is Multi AZ
	* You can do back ups and restores of Redis
	* If you need to scale horizontally, use memcached

## Route53

* ELBs do not have pre-defined IPv4 addresses; you resolve them using a DNS name
* Alias Record vs CNAME.
* Given the choice always choose an Alias Record over a CNAME
* DNS Types
	* SOA Records
	* NS
	* A
	* CNAMES
	* MX Records
	* PTR Records
* Routin policies:
	* Simple routing: only one record with multiple addresses. With multiple values route53 return all values in random order. No helath check
	* Weighted routing: can set a weight with percentage
	* Latency-based routing: redirect traffic to target with the lowest latency 
	* Failover Routing: the is active and passive target. If the active one fails, the passive one will be used
	* Geolocation routing: redirect depending users location to the nearest target (no latency related just geolocation)
	* Geoproximity Routing (Traffic Flow Only): route trafic based on the geographic location of your users and resources. You can specify more or less routing to a given resource (bias). For this routing it is required to use Route 53 traffic flow
	* Multi value answer routing: the same than Simple routing but with heallth checks
* Health Checks
	* You can set a health chek on individual record sets
	* If a record set fails a heallth check it will be removed from Route53 until it passes it
	* You can set SNS notifications to alert you if a health check is failed

## VPC
* think of VPC as a logical datacenter in AWS
* Consists of:
	* IGW or (virtual private gateway)
	* route tables
	* Network ACLs
	* Subnets
	* Security groups
* 1 Subnet = 1 AZ
* Security groups are stateful but network ACL are stateless
* No transitive peering
* when creating a VPC, a default route table, Network ACL and a default security group is created
* No subnets will be created, nor a default internet gateway
* AZ's are randomized. Your AZ US-East-1A in your AWS account can be a completely different one from US-East-1A in another account
* AMazon always reserves 5 IP addresses within your subnet
* You can only have 1 Internet Gateway per VPC
* Security groups can't span multiples VPC
* When creating a NAT instance:
	* disable Source/Destination Check on it
	* They must be on a public subnet
	* there must be a route out of the private subnet to the NAT instance
	* The amount of traffice the instance supoorts depends on the instance size
	* you can create autoscaling groups, multiple subnets in different AZs and a script to automate failover
	* Are always behind a security groups
* NAT gateways
	* Redundant inside the AZ
	* Preferred by enterprise
    * Starts at 5GBps and scales up to 45Gbps
	* No need to patch
	* Not associated with security groups
	* Automatically assigned a public ip address
	* Remember to update your route tables
	* No need to disable source/destination check
	* to create AZ independent architecture you have to create a Nat Gateway in every AZ and point to it from the same AZ
* Network ACL's
	* Your VPC comes with default NACL and by default it allows all outbound and indbound traffic
	* You can create custom NACL's. By default each custom NACL denies all inbound and outbound traffic until you add rules
	* Each subnet in the VPC must be associated wth a NACL. If you don't explicitly associate it, the default NACL will be used
	* Block IP addresses using NACL's and not Security groups (not possible)
	* You can associate a NAC with multiple subnets, however a subnet can be associated to only one NACL at a time.
	* NAC'L's contains a numbered list of rules evaluated in order, starting with the lowest numbered rule. If you want to deny something you have to put the deny rule previous to the allow
	* They have separate inbound and outbound rules, and each rule can either allow or deny traffic
	* Are stateles, responses to allowwed inbound traffice are subject to the rules for aoutbound traffic (and vice versa)
* ELB's and VPC's
	* You need a minium of 2 pulblic subnets to deploy an Internet facing loadbalancer
* VPC Flow logs
	* You cannot enable flow logs for VPCs that are peered with your VPC unless the peer VPC is in your account
	* YOu cannot tag a flow log
	* After created you cannot change its configuration, p.e. you cannto associate a different IAM roles with the flow log
	* Not all IP traffic is monitored
		* Traffic by instances contacting Amazon DNS
		* Traffic by a windows instance for Amazon license activation
		* Traffic to and from 169.254.169.254 for instance metadata
		* DHCP traffic
		* Traffic to the reserved IP address for the default VPC router
* Bastions
	* Instance in a public subnet accesible from internet to access private subnets
* Bastions vs NAT Gateways/Instances
	* A NAT Gateway/Instance is used to provide internet traffic to EC2 instances in a private subnet
	* a Basion is used to securely admin EC2 instances (using SSH or RDP). Also called jump boxes
	* You cannot use a NAT Gateway as a bastion host
* Direct connect
	* Connects directly your Datacenter to AWS
	* Usefur for high throutput workloads
	* Also for stable and reliable secure connection
* VPC Endpoint:
	* Enables you to privately connect your VPC to supported AWS services and VPC endpoint services powered by Privatelink without requiring an internet gateway, NAT device, VPN connection or AWS Direct connection.
	* Instances in VPC do no require public IP to connect the services
	* Traffic does not leave the Amazon network
	* Endpoint are virtual services
	* Horizontally scaled, redundant and HA
	* 2 types
		* Interface ENdoints
		* Gateway endpoints that supports
    		* Amazon S3
			* Dynamo DB

# HA

* 3 types of load balancers
	* Application load balancers
	* Network load balancers: extreme performance
	* Classic load balancer: cheaper
* 504 error means the gateway has timed out because the application is not responding within the idle timeout period
* Troubleshoot the application. Is it the Web Server, DB server, etc...?
* If you need the client V4 IP address of you end user you have to use the X-Forwarded-For header
* Instances monitored by ELB are reported as:
	* InService
	* OutofService
* Health checks check the instance health by talking to it
* Load Balancers have their own DNS name. You are never given an IP address
* Read ELB FAQ for Classic Load Balancers
* Sticky sessions enable your users to stick to the same EC2 Instance (in case of information stored locally)
* Cross Zone Load Balancing enables you to load balance across multiple AZ
* Path patterns allow you to direct  traffic to different EC2 instances based on the URL contained in the request
* CloudFormation
	* Scripting your cloud environment
	* Quick Start is a bunch of CF templates already built by AWS Solutions Architects allowing you to create complex environment very quickly
* Elastic Beanstalk:
	* Quickly deploy and manage applications in the AWS Cloud without worrying about the infrastructure that runs them
	* Simply upload your application and EB handles capacity provisioning, load balancing, scaling and app health monitoring

## Application Summary

* SQS
	* de-couple your infra
	* pull based, not push based
	* Messages are 256KB in size
	* Can be kept iin queue from 1 minuto to 14 days
	* Standard SQA and FIFO SQS
		* Standard order is not guarantee and messages can be delivered more than once
		* FIFO orded is strictly maintained and messages are delivered only once
* Visibility timeout: amount of time the message is invisible in the SQS queue after a reader picks up that message. If the job is not processed in that time the message will become visible again and other reader will process it. This could result in the same message processed twice
	* Visibility timeout max is 12 hours
	* SQS guarantee you messages will be processed at least once
	* Amazon SQS long polling is a way to retrieve messages from your Amazon SQS queues.  This method doesn't return a response until a mesage arrives in the message queue or the long poll times out
* SQS vs SQS
	* SQS retention up to 14 days. SWF up to 1 year
	* SWF is task oriented and SQS is message oriented
	* In SWF a task is assigned only once and neve duplicated. SQS you need to handle duplicate messages and may also need to ensure that a message is processed only once
	* SWF keeps track of all the tasks and events in a application. SQS you need to implement your own application-level tracking especially if your application uses multiple queues
* SWF Actors
	* Starters: An application that can start a workflow.
	* Deciders. control the flow of activity tasks in a workflow execution
	* Workers: carry out the activity tasks
* SNS
	* Instant push based delivery (no polling)
	* Simple API and easy integration with applications
	* Flexible message delivery over mutiple transfe protocol
	* Inexpensive, pay as you go up-front costs
	* Web-based AWS Management console simplicity
* SNS vs SQS
	* Both messageing services
	* SNS is push and SQS is pull
* Elastic transcoder
	* media transcoder in the cloud
* API Gateway
	* Has caching capabilities to increase performance
	* low cost and scales automatically
	* You can throttle API Gateway to prevent attacks
	* You can log results to CloudWatch
* Ensure you have enabled CORS in case you are using calls from multiple domains
* CORS is always enforced by client browser
* Kinesis
	* Streams: with persistence (shards, etc...)
	* Firehose: Analyze in RT and find a place where to store it (no persistence
	* Analytics:Analyze data in both of previous services
* Cognito:
	* Federation allow users to authenticate with a Web Identity Provider (Google, Facebook, Amazon)
	* The user authenticates first with the Web ID Provider and receives and authentication token, which is exchanged for temporary AWS credentials allowing them to use an IAM role
	* Is an Identity broker than handles interactions between your applications and the Web ID Provider
	* User pools are user based. It handles things like use registration, authentication and account recovery
	* Identity pools authorise access to your AWS resources

## Serverless

* Lambda scales out (not up) automatically
* Are independent, 1 event= 1 function
* Labmda is serverless
* Know what services are severless
* Lambdas can trigger other lambdas
* AWS X-ray allows to debug what is happening
* Lambda cand do things globally, you can use it to back up S3 buckets to other S3 buckets, etc...
* Know your triggers

