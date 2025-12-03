Here is a **clean, copy-paste-ready README.md** — no emojis, no fluff, just professional content.

---

# Simple EKS Cluster (CloudFormation)

This repository contains an AWS CloudFormation template that deploys a minimal Amazon EKS cluster for development, testing, or learning purposes.
The template sets up a dedicated VPC, two public subnets, an EKS control plane, and a managed node group.

---

## Architecture Overview

The stack creates the following resources:

* VPC (10.20.0.0/16)
* Two public subnets (in two Availability Zones)
* Internet Gateway and public route table
* Security Group shared by the cluster and nodes
* EKS Cluster (public endpoint)
* Managed Node Group (EC2 instances)
* IAM roles for the cluster and nodes
* Cluster logging (API, audit, authenticator)

Nodes are deployed in public subnets for simplicity. This template is not intended for production environments.

---

## Resources Created

### Networking

* AWS::EC2::VPC
* AWS::EC2::InternetGateway
* AWS::EC2::VPCGatewayAttachment
* AWS::EC2::RouteTable
* AWS::EC2::Route
* Two AWS::EC2::Subnet
* Two AWS::EC2::SubnetRouteTableAssociation

### IAM

* Cluster IAM role (AmazonEKSClusterPolicy)
* Node IAM role (AmazonEKSWorkerNodePolicy, AmazonEC2ContainerRegistryReadOnly, AmazonEKS_CNI_Policy)
* EC2 Instance Profile

### EKS

* AWS::EKS::Cluster
* AWS::EKS::Nodegroup
* Logging enabled: api, audit, authenticator

---

## Template Parameters

| Parameter            | Description                      | Default         |
| -------------------- | -------------------------------- | --------------- |
| ClusterName          | Name of the EKS cluster          | `romain`        |
| KubernetesVersion    | Kubernetes version               | `1.30`          |
| VpcCidr              | VPC CIDR block                   | `10.20.0.0/16`  |
| PublicSubnet1Cidr    | Public subnet 1 CIDR             | `10.20.0.0/19`  |
| PublicSubnet2Cidr    | Public subnet 2 CIDR             | `10.20.32.0/19` |
| NodeInstanceType     | EC2 instance type for node group | `t3.small`      |
| NodeGroupDesiredSize | Desired node count               | `2`             |
| NodeGroupMinSize     | Minimum node count               | `1`             |
| NodeGroupMaxSize     | Maximum node count               | `4`             |

---

## Deployment

### Using AWS CLI

```
aws cloudformation deploy \
  --template-file eks.yaml \
  --stack-name eks-romain \
  --capabilities CAPABILITY_NAMED_IAM
```

### Using AWS Console

1. Open the CloudFormation console
2. Select "Create stack"
3. Upload the template file
4. Review and configure the parameters
5. Create the stack

Wait for the `CREATE_COMPLETE` status.

---

## Accessing the Cluster

Once the stack is deployed:

```
aws eks update-kubeconfig --name romain
kubectl get nodes
```

---

## Outputs

The stack provides:

* `ClusterNameOutput`: EKS cluster name
* `ClusterArn`: ARN of the EKS cluster
* `VpcIdOutput`: ID of the created VPC
* `PublicSubnetIds`: Comma-separated list of public subnet IDs

---

## Notes

* This setup uses **public subnets** and a **public cluster endpoint**.
* It is intended for **non-production** use.
* For production, consider adding private subnets, NAT gateways, IRSA, network restrictions, and additional EKS add-ons.

---

If you want, I can also generate a production version of this template, a Terraform version, or a diagram for the README.
