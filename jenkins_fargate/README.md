## Fargate
AWS to home network.

Better Fargate documentation located on the plugin [Github repo](https://github.com/jenkinsci/amazon-ecs-plugin).

### Requirements
* [Amazon Elastic Container Service (ECS / Fargate) Plugin](https://github.com/jenkinsci/amazon-ecs-plugin) installed.
* AWS Credentials Created in Jenkins
  * Make sure `Kind` = `AWS Credentials`.
* [Fargate Cluster Created](https://docs.aws.amazon.com/AmazonECS/latest/userguide/create_cluster.html).
* AWS VPC + Subnets Created.
* AWS Security Group Created.
* Jenkins accessible via public IP.
  * Port 8080 (not exactly sure why).
  * Port 50000 (agents/slaves initiate a connection to this port).

### Steps
#### Create the Fargate Cloud in Jenkins
1. Navigate `Manage Jenkins` > `Manage Nodes and Clouds` > `Configure Clouds`
1. Add a new cloud `Amazon EC2 Container Service Cloud`
1. Select the AWS credentials
1. Make sure the `Amazon ECS Region Name` is set to the region the Fargate cluster is in.
1. The Fargate cluster should load and appear in the `ECS Cluster` selection, select the correct one.

**If the agents are not connecting or we have a load balancer/complicated setup etc, we can override the connection defaults as below.**
1. Expand the `Advanced...` options.
1. `Tunnel connection through` = the host and port the Fargate agents try to connect back to the master Jenkins on (50000). `HOST:50000` "HOST:PORT" HOST can be domain or ip, port can leave blank to use Jenkins master default settings, or specify port.
  * `jenkins.example.com:50000`
  * `jenkins.example.com:`
  * `203.220.103.174:50000`
  * etc.
1. `Alternative Jenkins URL` not exactly sure why
  * `http://jenkins.example.com:8080/`
  * `https://jenkins.example.com:8080/`
  * `http://203.220.103.174:8080/`
  * etc.

#### Create ECS Agent Template
1. Click `Add`
1. `Docker image` Can use the default or we own, see jenkins-slave-fargate for an example custom image (have not tried Docker in Docker).
1. `Soft Memory Reservation` and `CPU units`: these values must follow 'Supported task CPU and memory values for Fargate' from [here](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-cpu-memory-error.html).
  *  `CPU units`: `256`, `Soft Memory Reservation`: `1024`
  *  `CPU units`: `4096`, `Soft Memory Reservation`: `8192`
  *  `CPU units`: `4096`, `Soft Memory Reservation`: `30720`
  *  etc.
1. `Subnets`: e.g. `subnet-cbbac1bd,subnet-d6d483b2`
1. `Security Groups`: e.g. `sg-0f9213cc248e60f31`
1. `Assign Public Ip`: checked
1. (Optional) `Task Execution Role ARN`: if we want to give tasks access to AWS resources, for example CloudWatch Logs.

(Optional) Fargate by default has no logs for the tasks, if we want logging we can use the [awslogs](https://docs.aws.amazon.com/AmazonECS/latest/userguide/using_awslogs.html) logging driver to push logs to CloudWatch Logs. See below for configuration.

1. Make sure `Task Execution Role ARN` is set above and has permissions to create log groups and logs.
1. `Logging Driver`: `awslogs`
1. `Logging Configuration`:
  * `Name`: `awslogs-create-group`, `Value`: `true`
  * `Name`: `awslogs-region`, `Value`: e.g. `ap-southeast-2`
  * `Name`: `awslogs-group`, `Value`:  e.g. `jenkins-slave-fargate`
  * `Name`: `awslogs-stream-prefix`, `Value`: e.g. `jenkins-slave-fargate`
