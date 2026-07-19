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
      VPC                   | **Same VPC as your EKS cluster**  
      Subnet                | **One of the EKS public subnets** 
      Auto-assign Public IP | **Enable**                        
      Storage               | 8 GB (or 10 GB if you prefer)     
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

 4. Then go back to your bastion-host EC2 instance:

    ```bash
       Select the instance.
       Actions → Security → Modify IAM role.
       Select bastion-host-role.
       Click Update IAM role.
    ```
    
      
    

    
