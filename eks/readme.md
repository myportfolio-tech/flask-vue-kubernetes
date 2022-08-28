# create our role for EKS

role_arn=$(aws iam create-role --role-name getting-started-eks-role --assume-role-policy-document file://assume-policy.json | jq .Role.Arn | sed s/\"//g)
aws iam attach-role-policy --role-name getting-started-eks-role --policy-arn arn:aws:iam::aws:policy/AmazonEKSClusterPolicy

# create the cluster VPC

curl https://amazon-eks.s3.us-west-2.amazonaws.com/cloudformation/2020-05-08/amazon-eks-vpc-sample.yaml -o vpc.yaml
aws cloudformation deploy --template-file vpc.yaml --stack-name getting-started-eks

# grab your stack details

aws cloudformation list-stack-resources --stack-name getting-started-eks > stack.json

# create our cluster

aws eks create-cluster \
--name getting-started-eks \
--role-arn $role_arn \
--resources-vpc-config subnetIds=subnet-063efe1fa0c5d4913,subnet-06f91e563755e2077,subnet-0824d16f8536b3681,securityGroupIds=sg-0960d3a116ba912e1,endpointPublicAccess=true,endpointPrivateAccess=false

aws eks list-clusters
aws eks describe-cluster --name getting-started-eks

# Command

aws eks create-cluster --name getting-started-eks --role-arn arn:aws:iam::822575900206:role/getting-started-eks-role --resources-vpc-config subnetIds=subnet-065a3c058e3f40c2b,subnet-0360164d087307fa0,subnet-086312d43eb0eb779,securityGroupIds=sg-0cc2fef4a799efcec,endpointPublicAccess=true,endpointPrivateAccess=false

aws eks list-clusters
aws eks describe-cluster --name getting-started-eks

aws eks update-kubeconfig --name getting-started-eks --region us-east-1

aws eks create-nodegroup --cluster-name getting-started-eks --nodegroup-name test --node-role arn:aws:iam::822575900206:role/getting-started-eks-role-nodes --subnets subnet-065a3c058e3f40c2b --disk-size 200 --scaling-config minSize=1,maxSize=2,desiredSize=1 --instance-types t2.small

https://github.com/marcel-dempers/docker-development-youtube-series/blob/master/kubernetes/cloud/amazon/getting-started.md
