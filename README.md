# Infrastructure Setup Documentation

## VPC Configuration
I created a new **Virtual Private Cloud (VPC)** named `STAGING` `50.0.0.0/16` in the **eu-west-1** region.

---

## Subnets
I configured two subnets:
- **Public Subnet**: `50.0.50.0/24` Enabled *Auto-assign Public IP*.
- **Private Subnet** `50.0.80.0/24`

---

## Route Tables
I created two route tables:
- **Public RT**: Associated with the public subnet. 
- **Private RT**: Associated with the private subnet. 

---

## Internet Gateway
To enable internet access:
- Created an **Internet Gateway (IGW)**.
- Attached the IGW to the VPC.
- Added a route in the **Public RT** to direct internet-bound traffic (`0.0.0.0/0`) through the IGW.

---

## Security Groups
Created two security groups:

### Bastion-SG
- **Type**: SSH  
- **Protocol**: TCP  
- **Port**: 22  
- **Source**: `0.0.0.0/0`

### CoreBank-SG
- **Type**: SSH  
- **Protocol**: TCP  
- **Port**: 22  
- **Source**: Bastion-SG (only allows SSH from Bastion Host)

---

## EC2 Instances
Launched two EC2 instances using **Ubuntu AMI** and **t2.micro**:

- **BastionHost-VM**  
  - Subnet: Public  
  - Security Group: Bastion-SG

- **CoreBankApp-VM**  
  - Subnet: Private  
  - Security Group: CoreBank-SG

Created a **key pair** for secure SSH access.  
Selected **8 GiB SSD** as the root volume.

---

## NAT Gateway
To allow the private subnet to access the internet:
- Created a **NAT Gateway** in the public subnet.
- Assigned an **Elastic IP** to the NAT Gateway.
- Added a route in the **Private RT** to direct internet-bound traffic through the NAT Gateway.

---

## S3 Bucket
- Created an S3 bucket named `test-ml-bucket-emma`.
- Uploaded two files to the bucket.

---

## IAM Policy Assignment
- Attached an **S3 access policy** to the IAM role associated with **CoreBankApp-VM**, allowing it to interact with the S3 bucket from within the private subnet.

---

## AWS CLI Installation
- Installed **AWS CLI** on the **CoreBankApp-VM**.

---

## Result
- SSH access was successfully established to the **BastionHost-VM** (public instance).
- From the BastionHost-VM, SSH access was established to the **CoreBankApp-VM** (private instance).
- After installing AWS CLI on the private instance, I was able to access the S3 bucket.

---
