# Set up Open-vpn

1. Launch an Instance

   ```bash
   nama- openvpn-server
   ```

   *Configuration*

   ```bash
     Setting               | Value              
     --------------------- | ------------------ 
     AMI                   | Ubuntu 26.04 
     Instance Type         | t3.micro           
     Key Pair              | Your existing key  
     VPC                   | Same EKS VPC       
     Subnet                | Public subnet      
     Auto Assign Public IP | Enable             
     Storage               | 8 GB               
     ```

 2. **Security Group**
    Create a security group named:

    ```bash
    openvpn-sg
    ```

     Type       | Port | Source                              
     ---------- | ---- | ---------------------------------- 
     SSH        | 22   | Your IP (or 0.0.0.0/0 temporarily)
     HTTPS      | 443  | 0.0.0.0/0                         
     Custom UDP | 1194 | 0.0.0.0/0

 3. **Create a Role**
    Create a role

    ```bash
    Trusted Entity
    AWS Service
    EC2
    ```
    Attach

    ```bash
    AdministratorAccess
    ```

    Role Name

    ```bash
    openvpn-role
    ```

 4. Verify IAM 
    Run

    ```bash
    aws sts get-caller-identity
    ```

    You should see

    ```bash
    assumed-role/openvpn-role/...
    ```

 5. Install OpenVPN
    *Run this command*

     ```bash
     sudo apt update
     sudo apt install -y git
     ```

     clone this

     ```bash
     git clone https://github.com/angristan/openvpn-install.git
     ```

     Go into the directory:

     ```bash
     cd openvpn-install
     ```

     Make it executable:

     ```bash
     chmod +x openvpn-install.sh
     ```

     Run it

     ```bash
     sudo ./openvpn-install.sh install
     ```

 6. Verify the file exists
    Run

    ```bash
    ls -lh /home/ubuntu/*.ovpn
    ```

    
    Expected

    ```bash
    -rw------- 1 ubuntu ubuntu 8K Jul ... <what name you give>.ovpn
    ```
    
 7. Copy the file to your laptop
    From Git Bash on your laptop (not from the EC2 instance), run:

    ```bash
    scp -i /c/Users/<YOUR_WINDOWS_USERNAME>/Downloads/aws-key.pem \
    ubuntu@<OPENVPN_PUBLIC_IP>:/home/ubuntu/rudra.ovpn .
    ```

 8. **Install OpenVPN Connect on Windows**

    *Download and install OpenVPN Connect*

 9.  Import the VPN profile
     Open OpenVPN Connect.
     Click Upload File (or Import Profile).
     Select rudra.ovpn.
     Click Connect

     you should see

     ```bash
     Securely Connected
     ```

 10. Import the VPN Profile
      1.Open OpenVPN Connect.
      2.Click Upload File (or Import Profile).
      3.Browse to:

      ```bash
      C:\Users\era\Downloads\client.ovpn
      ```

      4.Import it.
      5.Click Connect.
      
  11. Verify VPN Connection

      Once connected, OpenVPN Connect should show something like:

      ```bash
      Connected
      ```

  12. ***Test the VPN**
      From Git Bash on your Windows laptop, SSH to the private IP of your bastion

      ```bash
      ssh -i /c/Users/era/Downloads/aws-key.pem ubuntu@<bastion private ip>
      ```

  13. Verify Kubernetes Access

      Once you're logged into the bastion over the VPN, run

      ```bash
      kubectl get nodes
      ```

      You should see your two EKS worker nodes in the Ready state.





    

                      

