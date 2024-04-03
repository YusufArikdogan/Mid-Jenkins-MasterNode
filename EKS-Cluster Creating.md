### Jenkins Server freestyle

# create eks key
EKS_KEY="eks_key"
EKS_PUB_KEY="eks_key_pub"
AWS_REGION="us-east-1"
aws ec2 create-key-pair --key-name ${EKS_KEY} --region ${AWS_REGION} --query 'KeyMaterial' --output text > ${EKS_KEY}

# create .ssh folder in Jenkins Home
# move current key under .ssh folder
mkdir -p ${JENKINS_HOME}/.ssh
cp ${EKS_KEY} ${JENKINS_HOME}/.ssh/${EKS_KEY}

# set permissions for the private key
chmod 600 ${JENKINS_HOME}/.ssh/${EKS_KEY}

# get public key from key pair
output=$(aws ec2 describe-key-pairs --key-names ${EKS_KEY} --include-public-key)
#  Extract the public key material using jq, JSON parser
public_key=$(echo "$output" | jq -r '.KeyPairs[0].PublicKey')
echo "$public_key" > ${JENKINS_HOME}/.ssh/${EKS_PUB_KEY}

# create EKS cluster
eksctl create cluster --name kube --region ${AWS_REGION} --zones us-east-1a,us-east-1b,us-east-1c --nodegroup-name kube-nodes --node-type t3a.small --nodes 2 --nodes-min 2 --nodes-max 5 --ssh-access --ssh-public-key ${JENKINS_HOME}/.ssh/${EKS_PUB_KEY} --managed --version 1.27

# install metric-sever
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
kubectl get deployment metrics-server -n kube-system

# install vpa
git clone https://github.com/kubernetes/autoscaler.git
cd autoscaler/vertical-pod-autoscaler/
./hack/vpa-down.sh (if you have existing vpa`s)
./hack/vpa-up.sh

# Update Kubeconfig (commented out)
# aws eks --region us-east-1 update-kubeconfig --name kube

# Delete cluster (commented out)
# eksctl delete cluster --name kube --region ${AWS_REGION}
# Delete EKS key pair (commented out)
# aws ec2 delete-key-pair --key-name ${EKS_KEY} --region ${AWS_REGION}

# Delete ingress-nginx-admission (commented out)
# kubectl delete -A ValidatingWebhookConfiguration ingress-nginx-admission