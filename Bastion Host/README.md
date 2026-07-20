## CREATING OF BASTION HOST

1. **LAUNCH A INSTANCE**
   **Configuraton** of instance

    ```bash
      Setting               | Value                             
      --------------------- | --------------------------------- 
      Name                  | `bastion-host`                    
      AMI                   | Ubuntu latest        
      Instance Type         | `t3.micro`                        
      Key Pair              | Your existing key pair            
      VPC                   | Same VPC as your EKS cluster  
      Subnet                | One of the EKS public subnets
      Auto-assign Public IP | Enable                        
      Storage               | 8 GB      
     ```
 2. Security Group

    Allow:

    SSH (22) → Your IP (or 0.0.0.0/0 )
    Outbound → All traffic

 3. Attach an IAM role.

    3.1 Trusted entity type
        ✅ AWS service
    
    3.2 Use case
        ✅ EC2
    
    3.3 Attach the Policy
        ✅ AdministratorAccess
    
    3.4 Give the role name
        Bastion-host-rule

 5. Then go back to your bastion-host EC2 instance:

    ```bash
       Select the instance.
       Actions → Security → Modify IAM role.
       Select bastion-host-role.
       Click Update IAM role.
    ```

  6. Install required tools

    SSH into the bastion and install:
     kubectl
     eksctl
     Helm

  7. Configure kubeconfig
     
     ```bash
     aws eks update-kubeconfig \
     --region us-east-1 \
     --name argocd-cluster
     ```
     
  9. Create the access entry
     
     ```bash
     aws eks create-access-entry \
     --cluster-name argocd-cluster \
     --principal-arn arn:aws:iam::<aws accound id>:role/bastion-host-role \
     --type STANDARD \
     --region us-east-1
     ```

   You should get JSON output showing the access entry was created.

  10. Associate the cluster admin policy

      ```bash
      aws eks associate-access-policy \
      --cluster-name argocd-cluster \
      --principal-arn arn:aws:iam::<aws account id> :role/bastion-host-role \
      --policy-arn arn:aws:eks::aws:cluster-access-policy/AmazonEKSClusterAdminPolicy \
      --access-scope type=cluster \
      --region us-east-1
      ```

  11. Verify

      ```bash
      kubectl get nodes
      ```

      you will get

      ```bash
      
       NAME                             STATUS   ROLES    AGE
       ip-192-168-xx-xx.ec2.internal    Ready    <none>
       ip-192-168-yy-yy.ec2.internal    Ready    <none>
       ```
      
    

    
