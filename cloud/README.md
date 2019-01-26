# Kubernetes as a Service

## GCP
Run the following commands in the Cloud Shell

```
gcloud config set project kubernetes-intro-216921
gcloud config set compute/zone europe-west1
gcloud container clusters get-credentials k8s
```
Reference [link](https://cloud.google.com/kubernetes-engine/docs/quickstart)

## Azure
Run the following commands in the Cloud Shell

```
az group create --name k8s-intro --location westeurope
az aks create \
    --resource-group k8s-intro \
    --name k8s \
    --node-count 1 \
    --enable-addons monitoring \
    --generate-ssh-keys
az aks get-credentials --resource-group k8s-intro --name k8s
```

Reference [link](https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough)

And here's a [tutorial](https://stackify.com/azure-container-service-kubernetes/) I wrote for Stackify.

## AWS
You can run the following commands using Cloud9 (Sadly, there's no cloud shell)

```
curl --silent --location "https://github.com/weaveworks/eksctl/releases/download/latest_release/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv -v /tmp/eksctl /usr/local/bin
rm -vf ${HOME}/.aws/credentials
sudo yum install jq
export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r .region)
echo "export AWS_REGION=${AWS_REGION}" >> ~/.bash_profile
aws configure set default.region ${AWS_REGION}
aws configure get default.region
ssh-keygen
aws ec2 import-key-pair --key-name "eksworkshop" --public-key-material file://~/.ssh/id_rsa.pub
mkdir -p ~/.kube
sudo curl --silent --location -o /usr/local/bin/kubectl "https://amazon-eks.s3-us-west-2.amazonaws.com/1.11.5/2018-12-06/bin/linux/amd64/kubectl"
sudo chmod +x /usr/local/bin/kubectl
go get -u -v github.com/kubernetes-sigs/aws-iam-authenticator/cmd/aws-iam-authenticator
sudo mv ~/go/bin/aws-iam-authenticator /usr/local/bin/aws-iam-authenticator
INSTANCE_PROFILE_NAME=`basename $(aws ec2 describe-instances --filters Name=tag:Name,Values=aws-cloud9-${C9_PROJECT}-${C9_PID} | jq -r '.Reservations[0].Instances[0].IamInstanceProfile.Arn' | awk -F "/" "{print $2}")`
eksctl create cluster --name=eksworkshop-eksctl --nodes=3 --node-ami=auto --region=${AWS_REGION}
```

Reference [link](https://eksworkshop.com/prerequisites/self_paced/)