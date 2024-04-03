# Mid-Jenkins-MasterNode  
# Setting up Jenkins Server for EKS Cluster Deployment  

This guide will walk you through setting up a Jenkins server for deploying an EKS (Amazon Elastic Kubernetes Service) cluster. Please make sure you have an AWS account and have generated `key.pem` and AWS access keys.  

## Prerequisites  

- AWS account  
- AWS access key and secret access key  
- `key.pem` file in your local directory  
- Install Terraform  
- Clone the repository locally  
- VSCode or any other code editor 

## Setup Jenkins Server  

1. Clone the repository locally.  
2. Run `aws configure` command to configure AWS access key, secret access key, region (us-east-1), and output format (json).  
3. Move `key.pem` file to the cloned directory.  
4. Open `main.tf` and update the following variables:  
   - Optional: Change the region on line 11.  
   - Change user variable on line 14.  
   - Change the key file name on line 17.  
5. Run `terraform init` followed by `terraform apply -auto-approve` to provision the Jenkins server.  
6. Copy the SSH connection address from the output and paste it into your terminal to connect to the Jenkins server.  
7. Copy the `jenkins-initialadminpasswordpath` from the output and paste it into Jenkins server to access the initial password.   
8. Access the Jenkins server through the provided HTTP connection link from the output.  
9. Install suggested plugins and provide user details when prompted.  

## Configure Jenkins Server

1. Go to "Manage Jenkins" > "Credentials" and add credentials.  
2. Choose the kind of credential you want to add. For GitHub access, select "Username with password".  
3. Enter your GitHub username in the "Username" field.   
4. For the "Password" field, paste your GitHub token. You can generate a token from your GitHub account settings.    
5. Optionally, add a description such as "GitHub" for better organization.
6. Go to "Manage Jenkins" > "Manage Plugins" search for Locale Plugin at the search bar.    
7. Install the Locale plugin and set the language to English in "Manage Jenkins" > "System" > "Locale".  

## Setting up Jenkins Job  

1. Go to the Jenkins dashboard and click "Add Item", then select "Freestyle project".  
2. Name the project (e.g., EKS).  
3. In the build step, select "Execute shell" and paste the contents of `EKS-Cluster Creating.md` from the cloned repository.  
4. Click "Build" to execute the job. You can monitor the progress in the console output.  

The process typically takes about 15 minutes. During this time, the EKS cluster and nodes are created using CloudFormation. Once we see success in the output, we can proceed to the second stage.

Clone the necessary resources from our repository to your local environment. Then, create your own private repository and push the  
resources there after making any updates. After creating the repository, you may need to make a few changes locally. For further  
instructions, you can refer to the ReadMe file in the Mid-EKS-Cluster-Deployment repository.    
