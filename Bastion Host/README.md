## CREATING OF BASTION HOST

1. **LAUNCH A INSTANCE**
   **Configuraton** of instance

    ```bash
    | Setting               | Value                             |
| --------------------- | --------------------------------- |
| Name                  | `bastion-host`                    |
| AMI                   | Ubuntu latest         |
| Instance Type         | `t3.micro`                        |
| Key Pair              | Your existing key pair            |
| VPC                   | **Same VPC as your EKS cluster**  |
| Subnet                | **One of the EKS public subnets** |
| Auto-assign Public IP | **Enable**                        |
| Storage               | 8 GB      |
    ```

    
