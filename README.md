# Securing CoreBanking Infrastructure on AWS 

## Infrastructure Setup Documentation
This project configures an EC2 instance in a private subnet of staging VPC to interact with S3 bucket test-ml-bkt for listing and creating buckets, without public internet exposure. Features include a Bastion Host for secure SSH access, a NAT Gateway for outbound connectivity, an S3 bucket with IAM permissions, and AWS CLI integration.

## VPC Configuration
I created a new **Virtual Private Cloud (VPC)** named `STAGING` `50.0.0.0/16` in the **eu-west-1** region.

---

## Subnets
I configured two subnets:
- **Public Subnet**: `50.0.20.0/24` Enabled *Auto-assign Public IP*.
- **Private Subnet** `50.0.80.0/24`
<img width="652" height="302" alt="Image" src="https://github.com/user-attachments/assets/1543aeca-141b-417a-a3ff-536b8e15356e" />
<img width="667" height="239" alt="Image" src="https://github.com/user-attachments/assets/42a78fcc-2ee6-474f-aa83-8dc3f9b15507" />

---

## Route Tables
I created two route tables:
- **Public RT**: Associated with the public subnet. 
- **Private RT**: Associated with the private subnet.
<img width="808" height="338" alt="Image" src="https://github.com/user-attachments/assets/ee52df69-1595-492c-ab21-ed695bbcd93e" />
<img width="808" height="352" alt="Image" src="https://github.com/user-attachments/assets/412d1121-e289-4f55-b7ea-20c10e698809" />

---

## Internet Gateway
To enable internet access:
- Created an **Internet Gateway (IGW)**.
- Attached the IGW to the VPC.
- Added a route in the **Public RT** to direct internet-bound traffic (`0.0.0.0/0`) through the IGW.
<img width="810" height="220" alt="Image" src="https://github.com/user-attachments/assets/ccc8f468-6d99-4541-b403-e10b2a33a70e" />

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
<img width="815" height="277" alt="Image" src="https://github.com/user-attachments/assets/2976d9a7-83f5-4789-862b-1bed6264c8c9" />
<img width="926" height="320" alt="Image" src="https://github.com/user-attachments/assets/4a923b3a-5721-406a-bba5-38b712fc8ac3" />

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
<img width="816" height="272" alt="Image" src="https://github.com/user-attachments/assets/2c76363c-24ac-444b-b4f0-edc040251f38" />

---

## NAT Gateway
To allow the private subnet to access the internet:
- Created a **NAT Gateway** in the public subnet.
- Assigned an **Elastic IP** to the NAT Gateway.
- Added a route in the **Private RT** to direct internet-bound traffic through the NAT Gateway.
- <img width="1637" height="590" alt="Image" src="https://github.com/user-attachments/assets/9e190845-745e-44bb-827e-1a3ddac58e2f" />

---

## S3 Bucket
- Created an S3 bucket named `test-ml-bucket-emma`.
- Uploaded two files to the bucket.
<img width="1018" height="332" alt="Image" src="https://github.com/user-attachments/assets/f6baae04-269d-4d0e-8813-c36ae1459f92" />

---

## IAM Policy Assignment
- Attached an **S3 access policy** to the IAM role associated with **CoreBankApp-VM**, allowing it to interact with the S3 bucket from within the private subnet.
 <img width="941" height="400" alt="Image" src="https://github.com/user-attachments/assets/cf571fa2-ad7c-4d68-abd1-4389ac8d6e52" />

---

## AWS CLI Installation
- Installed **AWS CLI** on the **CoreBankApp-VM**.
<img width="857" height="240" alt="Image" src="https://github.com/user-attachments/assets/4741ccc9-8bb0-41ff-bb5e-89dda73b933d" />

---

## Result
- SSH access was successfully established to the **BastionHost-VM** (public instance).
- From the BastionHost-VM, SSH access was established to the **CoreBankApp-VM** (private instance).
- After installing AWS CLI on the private instance, I was able to access the S3 bucket.
<img width="730" height="461" alt="Image" src="https://github.com/user-attachments/assets/357152ff-e424-41f0-9d40-da77352c6a15" />
<img width="864" height="395" alt="Image" src="https://github.com/user-attachments/assets/418f5922-d621-42ee-b0f7-97f9eb563e74" />

---
This setup ensures the CoreBankingAPI instance remains isolated from the internet while securely accessing S3 for the required operations, using a Bastion Host for management and a NAT Gateway for outbound connectivity.
