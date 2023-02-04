#### AWS EKS

#####1. AWS Console:
Setup or Preparation Steps:
1. Create AWS Account
2. Create a VPC
3. Create an IAM Role with Security Group.
    Create an AWS User with a list of Permissions for EKS Cluster
4. Create CLuster Control Plane with IAM role.
    (choose cluster name, version, region & vpc, set security)
5. Create worker nodes (ec2 instances) & add them to the cluster.
    create as node-groups, choose the cluster it'll attach to, define security group, select instance types, resources, define min & max number of nodes which will be used in autoscaling.
6. Connect to the cluster using local system with `kubectl` command line tool. Configure `kubectl` for your system.

- `eksctl` developed by weaveworks is a command-line tool which can be used to configure `EKS` on AWS without in much faster & efficient way without having all the above process to do manually. `https://eksctl.io/`

#####2. EKSCTL: 
Create a cluster using default values in AWS using one single command & it'll do all the above 6 steps in one go.
`eksctl` installation guide: `https://docs.aws.amazon.com/eks/latest/userguide/eksctl.html`
```
- first configure the AWS credentials to connect to AWS
>>> aws configure                     ----- add the credentails (add a profile)
>>> export AWS_PROFILE = <profile>    ----- configure the profile to be used 
>>> eksctl create cluster --name test-cluster --version 1.23 --region ap-southeast-1 --nodegroup-name linux-nodes --node-type t2.micro --nodes 2
>>> eksctl delete cluster --name test-cluster
```
