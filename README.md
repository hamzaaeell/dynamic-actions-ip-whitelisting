# GitHub Actions Dynamic IP Whitelisting for EC2

This repository contains GitHub Actions workflows that demonstrate how to securely connect to EC2 instances from GitHub runners by dynamically whitelisting the runner's IP address in AWS Security Groups.

## Overview

The workflows solve a common problem: GitHub Actions runners have dynamic IP addresses that change with each run, making it challenging to maintain secure SSH access to EC2 instances. This solution automatically:

1. Detects the runner's current public IP address
2. Temporarily adds it to an AWS Security Group
3. Performs SSH operations on the EC2 instance
4. Removes the IP address from the Security Group when finished

## Workflows

### 1. Print Runner Public IP (`get-runner-dynamic-ip.yaml`)
A simple workflow that retrieves and displays the GitHub runner's public IP address.

**Triggers:**
- Push to `main` branch
- Manual workflow dispatch

### 2. SSH to EC2 with Dynamic IP Whitelisting (`whitelist-runner-dynamic-ip.yaml`)
The main workflow that demonstrates secure EC2 access with dynamic IP management.

**Features:**
- Automatically detects runner's public IP
- Temporarily whitelists IP in AWS Security Group
- Establishes SSH connection to EC2 instance
- Installs and configures Nginx as an example
- Cleans up by removing IP from Security Group

**Triggers:**
- Push to `main` branch
- Manual workflow dispatch

## Required Secrets

To use these workflows, you must configure the following secrets in your GitHub repository:

| Secret Name | Description |
|-------------|-------------|
| `AWS_ACCESS_KEY_ID` | AWS access key with EC2 and Security Group permissions |
| `AWS_SECRET_ACCESS_KEY` | AWS secret access key |
| `AWS_REGION` | AWS region where your EC2 instance is located |
| `EC2_HOST` | Public IP address or hostname of your EC2 instance |
| `EC2_SSH_PRIVATE_KEY` | Private SSH key for connecting to the EC2 instance |
| `EC2_USER` | Username for SSH connection (e.g., `ubuntu`, `ec2-user`) |
| `SECURITY_GROUP_ID` | ID of the AWS Security Group to modify |

## Setup Instructions

### 1. Configure AWS IAM User
Create an IAM user with the following permissions:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:AuthorizeSecurityGroupIngress",
                "ec2:RevokeSecurityGroupIngress",
                "ec2:DescribeSecurityGroups"
            ],
            "Resource": "*"
        }
    ]
}
```

### 2. Prepare EC2 Instance
- Ensure your EC2 instance is running and accessible
- Note the Security Group ID attached to your instance
- Generate or use an existing SSH key pair for access

### 3. Configure GitHub Secrets
Navigate to your repository's Settings → Secrets and variables → Actions, and add all the required secrets listed above.

### 4. Test the Workflow
- Push to the `main` branch or manually trigger the workflow
- Monitor the Actions tab to see the workflow execution

## Security Considerations

- The workflow automatically removes the runner's IP from the Security Group after completion, even if the workflow fails
- SSH keys are handled securely and not exposed in logs
- The IP whitelisting is temporary and scoped to port 22 only
- Consider using more restrictive IAM policies in production environments

## Customization

You can modify the SSH commands in the workflow to perform different operations on your EC2 instance. The current example installs Nginx, but you can replace this with any commands needed for your use case.

## Troubleshooting

- Ensure all secrets are correctly configured
- Verify that the IAM user has the necessary permissions
- Check that the Security Group allows modification of inbound rules
- Confirm the EC2 instance is running and the SSH key is correct

## License

This project is provided as-is for educational and demonstration purposes.