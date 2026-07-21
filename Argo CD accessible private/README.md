1. Create the IAM Service Account (IRSA

   ```bash
   eksctl create iamserviceaccount \
   --cluster argocd-cluster \
   --region us-east-1 \
   --namespace kube-system \
   --name aws-load-balancer-controller \
   --role-name AmazonEKSLoadBalancerControllerRole \
   --attach-policy-arn arn:aws:iam::349412601569:policy/AWSLoadBalancerControllerIAMPolicy \
   --approve
   ```

   Install AWS Load Balancer Controller
      Add the Helm repository

   ```bash
   helm repo add eks https://aws.github.io/eks-charts
   ```

   Update the Helm repository

   ```bash
   helm repo update
   ```

   Install the controller

   ```bash
   helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
   -n kube-system \
   --set clusterName=argocd-cluster \
   --set serviceAccount.create=false \
   --set serviceAccount.name=aws-load-balancer-controller \
   --set region=us-east-1 \
   --set vpcId=vpc-097378fd718c6d45b
   ```

   Verify the installation

    ```bash
    kubectl get deployment -n kube-system
    ```
    
    Then

    ```bash
    kubectl get pods -n kube-system
    ```

   **Create an Internal Load Balancer for Argo CD**
      Convert the Service to an Internal LoadBalancer

      ```bash
           kubectl patch svc argocd-server -n argocd -p '{
            "metadata": {
              "annotations": {
                "service.beta.kubernetes.io/aws-load-balancer-scheme": "internal",
                "service.beta.kubernetes.io/aws-load-balancer-type": "external",
                "service.beta.kubernetes.io/aws-load-balancer-nlb-target-type": "ip"
               }
            },
            "spec": {
             "type": "LoadBalancer"
           }
         }'   
      ```

     Watch the Load Balancer Get Created

     ```bash
     kubectl get svc argocd-server -n argocd -w
     ```

     After about 1–3 minutes, it should see to something like:

     ```bash
      internal-k8s-argocd-xxxxxxxx.us-east-1.elb.amazonaws.com
     ```

     Verify it's an Internal Load Balancer

     ```bash
     
     aws elbv2 describe-load-balancers \
     --region us-east-1 \
     --query "LoadBalancers[?DNSName=='k8s-argocd-argocdse-57b578b009-97aa3d31d14faab5.elb.us-east-1.amazonaws.com'].[Scheme,Type,State.Code]" \
     --output table
     ```

     You will see after a while

     ```bash
     internal
     ```

     Get the Argo CD Password
     ```
     kubectl -n argocd get secret argocd-initial-admin-secret \
     -o jsonpath="{.data.password}" | base64 -d
     echo
     ```

     




     

   
