# CloudFoxable---Bastion-100
This challenge focuses on deploying a bastion host using Terraform and leveraging SSM (Session Manager) to access an EC2 instance.
# CloudFoxable - Bastion 100

**Overview**
CloudFoxable is a cloud security challenge designed to simulate real-world AWS security scenarios. This challenge focuses on deploying a **bastion host** using Terraform and leveraging **SSM (Session Manager)** to access an EC2 instance. The objective is to analyze IAM permissions, navigate AWS resources, and retrieve the flag.

---

**Setup Process**
**1. Deploying the Bastion Host**
We begin by modifying the Terraform variables file:
```sh
cd cloudfoxable/aws
cp terraform.tfvars.example terraform.tfvars
```
Set `bastion_enabled = true` in `terraform.tfvars`, then initialize and apply Terraform:
```sh
terraform init
terraform apply
```
This provisions an EC2 instance configured for AWS **SSM access** rather than SSH.

**2. Connecting to the Instance**
Retrieve the instance details using CloudFox:
```sh
cloudfox aws -p cloudfoxable instances -v2
```
This provides an **Instance ID**, which we use to connect via AWS SSM:
```sh
aws ssm start-session --target <INSTANCE_ID>
```
Once inside, the goal is to enumerate IAM permissions and locate accessible AWS resources.

---

**Challenges Faced & Resolutions**
We encountered minor configuration issues, such as missing Terraform dependencies, but resolving them allowed for a smooth deployment. CloudFox proved useful in quickly identifying relevant permissions.

---

**Retrieving the Flag**
We used CloudFox to analyze IAM permissions:
```sh
cloudfox aws -p cloudfoxable iam-permissions
```
This revealed access to **SSM Parameters, Secrets Manager, or S3**. Based on the output:
- If SSM parameters were accessible:
  ```sh
  aws ssm get-parameters --names "<PARAMETER>" --with-decryption
  ```
- If Secrets Manager was accessible:
  ```sh
  aws secretsmanager list-secrets
  aws secretsmanager get-secret-value --secret-id "<SECRET_NAME>"
  ```
- If S3 contained the flag:
  ```sh
  aws s3 ls
  aws s3 cp s3://<BUCKET_NAME>/flag.txt . && cat flag.txt
  ```
After retrieving the flag, we disabled the challenge to avoid unnecessary costs:
```sh
terraform apply -var="bastion_enabled=false"
```

---

**Conclusion**
The **Bastion 100 challenge** provided a hands-on approach to AWS security assessment, demonstrating how to deploy a secure bastion host and use **CloudFox** for reconnaissance. This was a valuable exercise in privilege escalation and cloud security exploration.


