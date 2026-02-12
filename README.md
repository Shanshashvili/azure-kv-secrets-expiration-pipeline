# Key Vault Secrets Expiration Check Pipeline (Oboba)

## Overview

This Azure DevOps pipeline automates the process of checking Azure Key Vault secrets for expiration across multiple environments (dev, qa, prod). It generates HTML reports and sends email notifications for secrets expiring soon or missing expiration dates. Designed for public use, it follows best practices for security, modularity, and maintainability.

## Features

- Scans all Key Vaults in a specified resource group for expiring secrets and secrets without expiration dates.
- Supports manual and scheduled runs.
- Publishes HTML report artifacts for each environment.
- Sends email notifications via SendGrid to designated recipients.
- Modular templates for easy customization and reuse.

## Prerequisites

- Azure DevOps organization and project (e.g., Oboba).
- Service connections for Azure subscriptions (e.g., NonProdServiceConnection, ProdServiceConnection).
- Key Vaults and resource groups named according to the variables in the pipeline (e.g., RG-Oboba-Dev, AKV-Oboba-DEV-ADM).
- SendGrid account and API key (see below for details).
- Self-hosted or Microsoft-hosted agent pools as referenced in the pipeline.

## About SendGrid & How to Get an API Key

This pipeline uses [SendGrid](https://sendgrid.com/) to send email notifications with HTML reports. SendGrid is a cloud-based email delivery service that allows you to send emails reliably and securely from applications and services.

**Why SendGrid?**

- Azure DevOps does not natively support sending custom HTML emails with attachments or rich formatting.
- SendGrid provides a robust API for sending transactional emails, which is ideal for automated notifications.

**How to Set Up SendGrid for This Pipeline:**

1. **Create a SendGrid Account:**
   - Go to [https://sendgrid.com/](https://sendgrid.com/) and sign up for a free or paid account.

2. **Create a SendGrid API Key:**
   - After logging in, navigate to "Settings" > "API Keys" in the SendGrid dashboard.
   - Click "Create API Key".
   - Give your key a name (e.g., `ObobaPipelineKey`), select "Full Access" or at least "Mail Send" permissions, and click "Create & View".
   - Copy the generated API key. **You will not be able to view it again!**

3. **Store the API Key in Azure Key Vault:**
   - In the admin Key Vault for your environment (e.g., `AKV-Oboba-DEV-ADM`), create a new secret (e.g., `SendGrid-API-Key`).
   - Paste the API key value.
   - Ensure your pipeline's service connection has access to read secrets from this Key Vault.

4. **Reference the Secret in the Pipeline:**
   - The pipeline and templates are already set up to use the secret named `SendGrid-API-Key`.
   - No further changes are needed unless you use a different secret name.

**More Information:**

- [SendGrid Documentation](https://docs.sendgrid.com/)
- [How to create and manage API keys](https://docs.sendgrid.com/ui/account-and-settings/api-keys)
- [Azure Key Vault documentation](https://learn.microsoft.com/azure/key-vault/general/)

## Folder Structure

```bash
project AKV Check/
  schedule-check-amt-kv-secrets-expiration.yml
  templates/
    template-check-kv-secrets-expiration.yml
    template-install-az-cli.yml
    template-send-email-job.yml
    variables-oboba.yml
```

## Setup Instructions

1. **Create a new Azure DevOps pipeline**
   - In Azure DevOps, navigate to your project (e.g., Oboba).
   - Create a new pipeline and select the `schedule-check-oboba-kv-secrets-expiration.yml` file as the pipeline definition.

2. **Configure Service Connections**
   - Set up Azure service connections named `NonProdServiceConnection` and `ProdServiceConnection`.
   - Grant access to the relevant resource groups and Key Vaults.

3. **Prepare Key Vaults and Resource Groups**
   - Ensure Key Vaults and resource groups are named as referenced in the variables file.
   - Store the SendGrid API key in the admin Key Vault (e.g., AKV-Oboba-DEV-ADM).

4. **Customize Email Recipients**
   - Update the email recipient variables in the pipeline or templates to your team's addresses.

5. **Agent Pools**
   - Use self-hosted or Microsoft-hosted agent pools as specified in the variables file.

## Usage

- **Manual Run:**
  - Select the target environment from the dropdown (dev, qa, prod).
  - The pipeline will scan the specified environment and send notifications if issues are found.

- **Scheduled Run:**
  - The pipeline is scheduled to run daily (customizable via cron in the YAML).
  - Scans dev, qa, and prod environments automatically.

- **Artifacts and Notifications:**
  - HTML reports are published as pipeline artifacts for each environment.
  - Email notifications are sent only if expiring or missing secrets are detected.

## Best Practices

- Use parameterized templates for flexibility and maintainability.
- Store sensitive information (e.g., SendGrid API key) securely in Azure Key Vault.
- Limit email notifications to relevant recipients to avoid alert fatigue.
- Regularly review Key Vault secrets and update expiration dates as needed.
- Use descriptive names for service connections, resource groups, and Key Vaults.

## Troubleshooting

- Ensure agent pools and service connections are correctly configured.
- Verify Key Vault and resource group names match those in the variables file.
- Check that the SendGrid API key is accessible from the pipeline.
- Review pipeline logs and HTML reports for details on detected issues.

## License

This pipeline is provided as a public template for secure Key Vault management. Customize as needed for your organization.
