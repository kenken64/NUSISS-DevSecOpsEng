# S-DOEA - Workshop 2 - Jenkins Installation

## Pre-requisites for cloud installation

* AWS NUS ISS account with access to EC2 and AWS Marketplace.
* AWS Region: **Asia Pacific (Singapore)** (`ap-southeast-1`).
* A web browser and a secure location to save the EC2 private key.

## Step by step installation

The screenshots use the **Jenkins (Jenkins) on Ubuntu 24.04** Marketplace image by **Cloud Infrastructure Services**. Use your own instance details wherever placeholders appear.

### 1. Open EC2

![Search for EC2 in the AWS console](./screens/new-workshop2/Screenshot%20From%202026-10-21%2009-57-44.png)

Sign in to the AWS console, select **Asia Pacific (Singapore)**, and search for **EC2**. Open the EC2 service.

### 2. Launch an instance

![EC2 landing page with Launch instance](./screens/new-workshop2/Screenshot%20From%202026-10-21%2009-59-15.png)

Click **Launch instance** and enter a name such as `workshop2-jenkins`.

### 3. Browse the available machine images

![Application and OS Images section](./screens/new-workshop2/Screenshot%20From%202026-10-21%2009-59-57.png)

![AMI selection page](./screens/new-workshop2/Screenshot%20From%202026-10-21%2010-00-17.png)

Under **Application and OS Images (Amazon Machine Image)**, click **Browse more AMIs**.

### 4. Search AWS Marketplace for Jenkins

![Jenkins search in Quick Start AMIs](./screens/new-workshop2/Screenshot%20From%202026-10-21%2010-00-25.png)

![Jenkins results in AWS Marketplace AMIs](./screens/new-workshop2/Screenshot%20From%202026-10-21%2010-00-32.png)

Search for `jenkins`, then open the **AWS Marketplace AMIs** tab. The Quick Start tab may have no matching results.

### 5. Select the Jenkins Ubuntu image

![Selected Jenkins on Ubuntu 24.04 Marketplace image](./screens/new-workshop2/Screenshot%20From%202026-10-21%2010-00-41.png)

Select **Jenkins (Jenkins) on Ubuntu 24.04** by **Cloud Infrastructure Services**. Review the product details, pricing, and any subscription terms before continuing.

### 6. Configure the instance and storage

![Instance summary and 15 GiB root volume](./screens/new-workshop2/Screenshot%20From%202026-10-21%2010-00-56.png)

Choose **t2.medium**, as shown in the screenshots, or the instance type specified by your instructor. Set the root volume to **15 GiB** using **gp3** storage. The screenshot configuration is not a guarantee of free-tier eligibility.

### 7. Create a key pair

![Create an RSA key pair in PEM format](./screens/new-workshop2/Screenshot%20From%202026-10-21%2010-01-14.png)

Under **Key pair (login)**, click **Create new key pair**. Enter a name such as `workshop2`, select **RSA** and **.pem**, then click **Create key pair**. Save the downloaded key securely. If a file with that name already exists, use a different name to keep both keys.

### 8. Configure network access

Under **Network settings**, use the workshop VPC and subnet, enable **Auto-assign public IP**, and select or create a security group.

Allow inbound **TCP port 8080** from **My IP** for Jenkins. If the launch form does not offer this rule, add it after launch under the instance's **Security** tab: open the security group, choose **Edit inbound rules**, add **Custom TCP / 8080 / My IP**, and save.

For browser-based EC2 Instance Connect, allow SSH on **TCP port 22** from the EC2 Instance Connect service source for Singapore, as indicated by the connection page. For SSH from your own computer, allow port 22 from **My IP**.

### 9. Review and launch

![EC2 instance launch in progress](./screens/new-workshop2/Screenshot%20From%202026-10-21%2010-01-37.png)

![Successful EC2 instance launch](./screens/new-workshop2/Screenshot%20From%202026-10-21%2010-01-40.png)

Review the AMI, instance type, key pair, network rules, and storage, then click **Launch instance**. Wait for the success message and open the instance.

### 10. Check instance readiness

![Instance summary with public IPv4 address](./screens/new-workshop2/Screenshot%20From%202026-10-21%2010-01-59.png)

![Running instance with status checks passed](./screens/new-workshop2/Screenshot%20From%202026-10-21%2010-04-13.png)

Wait until the instance is **Running** and its status checks have passed. Copy its **Public IPv4 address** from the instance details. Use this address throughout the remaining steps.

### 11. Connect to the server

![EC2 Instance Connect using the ubuntu username](./screens/new-workshop2/Screenshot%20From%202026-10-21%2010-04-40.png)

![Ubuntu terminal opened through EC2 Instance Connect](./screens/new-workshop2/Screenshot%20From%202026-10-21%2010-05-04.png)

Select the instance and click **Connect**. Choose **EC2 Instance Connect**, select the public IP address, set the username to `ubuntu`, and click **Connect**.

Alternatively, use a terminal or Git Bash on your computer, replacing the key path and public IP:

```bash
chmod 400 /path/to/workshop2.pem
ssh -i /path/to/workshop2.pem ubuntu@<public-ip>
```

### 12. Open Jenkins

![Jenkins Unlock Jenkins page on port 8080](./screens/new-workshop2/Screenshot%20From%202026-10-21%2010-08-46.png)

In a browser, open:

```text
http://<public-ip>:8080
```

Include `:8080` in the URL. If the page times out, confirm that the instance is ready and the security group allows TCP port 8080 from your current public IP. Wait a few minutes for Jenkins to finish starting, then retry.

### 13. Retrieve the initial administrator password

In the instance terminal, run:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Copy the password displayed by the command. Use `sudo` because the `ubuntu` user cannot read this file directly.

### 14. Unlock Jenkins

![Initial administrator password entered in the Jenkins setup wizard](./screens/new-workshop2/Screenshot%20From%202026-10-21%2010-09-30.png)

Paste the password into the **Administrator password** field on the **Unlock Jenkins** page, then click **Continue**.

### 15. Install the suggested plugins

![Jenkins plugin installation progress](./screens/new-workshop2/Screenshot%20From%202026-10-21%2010-09-39.png)

When prompted to customize Jenkins, choose **Install suggested plugins**. Wait for plugin installation to complete.

### 16. Create the administrator account

![Create First Admin User form](./screens/new-workshop2/Screenshot%20From%202026-10-21%2010-12-05.png)

On **Create First Admin User**, enter your own username, password, full name, and email address. Click **Save and Continue**.

### 17. Set the Jenkins URL

![Jenkins instance configuration with URL on port 8080](./screens/new-workshop2/Screenshot%20From%202026-10-21%2010-12-44.png)

On **Instance Configuration**, confirm that **Jenkins URL** is `http://<public-ip>:8080/`, using your instance's public IP address. Click **Save and Finish**.

### 18. Open the dashboard

![Jenkins is ready confirmation](./screens/new-workshop2/Screenshot%20From%202026-10-21%2010-47-48.png)

![Jenkins dashboard after setup](./screens/new-workshop2/Screenshot%20From%202026-10-21%2010-47-55.png)

When **Jenkins is ready!** appears, click **Start using Jenkins**. Confirm that the Jenkins dashboard loads.

## Submission

Take a screenshot of your Jenkins dashboard with the Jenkins URL visible in the browser address bar. Save it to the workshop submission folder specified by your instructor.

## Cleanup

After submitting your screenshot, open the EC2 console in **Asia Pacific (Singapore)**. Remove only the resources you created exclusively for this workshop.

1. **Terminate the EC2 instance.** Under **Instances**, select your Jenkins instance (for example, `workshop2-jenkins`). Note its security group ID and key pair name. Choose **Instance state** → **Terminate (delete) instance**, confirm, and wait until the state is **Terminated**.
2. **Delete the security group.** If you created a dedicated group in step 8, open **Security Groups** and select the ID you noted. Choose **Actions** → **Delete security groups** and confirm. Skip this step if you reused an existing group.
3. **Delete the key pair.** Under **Key Pairs**, select the workshop key pair you noted. Choose **Actions** → **Delete**, enter `Delete` to confirm, and click **Delete**. Remove the matching `.pem` file from your computer if you used it only for this workshop.
