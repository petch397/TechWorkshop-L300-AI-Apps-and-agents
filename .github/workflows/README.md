# GitHub Actions ACR Deployment Setup

This document describes the GitHub secrets required for the Azure Container Registry deployment workflow.

## Required GitHub Secrets

To enable the deployment workflow, you need to configure the following secrets in your GitHub repository:

1. **ACR_LOGIN_SERVER**
   - Description: The login server URL for your Azure Container Registry
   - Example: `myregistry.azurecr.io`
   - How to find: In Azure Portal, go to your Container Registry → Overview → Login server

2. **ACR_USERNAME**
   - Description: Username for Azure Container Registry authentication
   - Example: `myregistry`
   - How to find: In Azure Portal, go to your Container Registry → Access keys → Username

3. **ACR_PASSWORD**
   - Description: Password for Azure Container Registry authentication
   - How to find: In Azure Portal, go to your Container Registry → Access keys → password (or password2)
   - Note: Ensure "Admin user" is enabled in Access keys

4. **ACR_NAME** (Optional)
   - Description: The name of your Azure Container Registry
   - Example: `myregistry`
   - Note: Used for environment variable, not strictly required

5. **ENV**
   - Description: The complete contents of your .env file
   - Format: Multi-line string containing all environment variables
   - Example:
     ```
     AZURE_OPENAI_ENDPOINT=https://...
     AZURE_OPENAI_API_KEY=...
     DATABASE_URL=...
     ```
   - **IMPORTANT**: This secret should contain the entire .env file content as a multi-line string

## How to Add Secrets to GitHub

1. Go to your GitHub repository
2. Click on **Settings** → **Secrets and variables** → **Actions**
3. Click **New repository secret**
4. Add each secret with the name and value specified above
5. Click **Add secret** to save

## Workflow Triggers

The workflow will automatically run when:
- Code is pushed to the `main` branch within the `src/` folder
- Manually triggered via the Actions tab (workflow_dispatch)

## Image Tags

The workflow creates two tags for each build:
- `<ACR_LOGIN_SERVER>/shopping-assistant:latest` - Always points to the most recent build
- `<ACR_LOGIN_SERVER>/shopping-assistant:<commit-sha>` - Specific version tied to the commit

## Security Notes

- ✅ The .env file is never committed to the repository (.gitignore prevents this)
- ✅ The .env file is created during the build process from the ENV secret
- ✅ The .env file is cleaned up after the build, even if the build fails
- ✅ All sensitive credentials are stored as GitHub secrets
- ✅ The Docker build context only includes files from the src/ folder
