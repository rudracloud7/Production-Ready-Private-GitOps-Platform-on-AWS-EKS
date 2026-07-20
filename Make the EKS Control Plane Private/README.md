***ALL COMMANDS WILL RUN ON BASTION HOST***

1. ***Enable Private Endpoint (Keep Public Enabled)***
   First, check the current endpoint configuration:

   ```bash
   aws eks describe-cluster \
   --name argocd-cluster \
   --region us-east-1 \
   --query "cluster.resourcesVpcConfig.{Private:endpointPrivateAccess,Public:endpointPublicAccess}"
   ```
   It will show this

   ```bash
   {
   "Private": false,
   "Public": true
   }
   ```

   Enable the private endpoint

   ```bash
   aws eks update-cluster-config \
   --region us-east-1 \
   --name argocd-cluster \
   --resources-vpc-config endpointPrivateAccess=true
   ```
   
   You should receive a response with an update ID.

   Check the status

   ```bash
   aws eks list-updates \
   --region us-east-1 \
   --name argocd-cluster
   ```

   Then

   ```bash
   aws eks describe-update \
   --region us-east-1 \
   --name argocd-cluster \
   --update-id <UPDATE_ID>
   ```
   
   Wait until:

   ```bash
   "status": "Successful"
   ```

   **Verify the configuration**

   ```bash
   aws eks describe-cluster \
   --name argocd-cluster \
   --region us-east-1 \
   --query "cluster.resourcesVpcConfig.{Private:endpointPrivateAccess,Public:endpointPublicAccess}"
   ```

   Expected output:

   ```bash
   {
   "Private": true,
   "Public": true
   }
   ```

   **Now get Bastion security group ID**

   ```bash
   aws ec2 describe-instances \
   --instance-ids $INSTANCE_ID \
   --region us-east-1 \
   --query "Reservations[0].Instances[0].{Vpc:VpcId,Subnet:SubnetId,SG:SecurityGroups[*].GroupId}"
   ```

   **Now get cluster security group ID**

   ```bash
   aws eks describe-cluster \
   --name argocd-cluster \
   --region us-east-1 \
   --query "cluster.resourcesVpcConfig.{ClusterSG:clusterSecurityGroupId,AdditionalSGs:securityGroupIds}"
   ```

   Now Get cluster eni private ip

   ```bash
   aws ec2 describe-network-interfaces \
   --filters Name=description,Values="Amazon EKS argocd-cluster*" \
   --region us-east-1 \
   --query "NetworkInterfaces[].{ENI:NetworkInterfaceId,PrivateIP:PrivateIpAddress,Groups:Groups[*].GroupId}"
   ```

   Now Run This Command

   ```bash
   aws ec2 authorize-security-group-ingress \
   --group-id <cluster security groupId> \
   --protocol tcp \
   --port 443 \
   --source-group <bastion security group id> \
   --region us-east-1
   ```



























   
