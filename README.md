## Azure Data Factory CI/CD Pipeline
This repository contains a **GitHub Actions CI/CD pipeline** to deploy **Azure Data Factory (ADF)** resources from **development** to **production**. The pipeline validates, exports, and deploys ADF artifacts, while also ensuring that **global parameters are updated appropriately per environment** (dev → prod).

### 🚀 Features
- **Branch-based deployments**
  - `develop` → Deploys to Dev ADF
  - `main` → Deploys to Prod ADF

- **Validation** of ADF resources before deployment
- **ARM template export** and artifact upload
- **Automated deployment** to target Data Factory
- **Global parameters management**
   - Automatically updates `storage_name`, `container_name`, and `environment` values for the target environment
   - Ensures proper JSON schema (`type` + `value`) for each parameter
- **Verification step** after deployment to confirm parameters


### 📂 Pipeline Overview
1. **Build Job**
   - Checks out code
   - Installs ADF Utilities (npm install)
   - Validates ADF resources
   - Exports ARM template (with global parameters)
   - Uploads ARM template as an artifact

2. **Release Job**
   - Determines target environment based on branch
   - Downloads ARM template artifact
   - Logs in to Azure using OIDC
   - Deploys ADF ARM templates
   - Prepares environment-specific **global parameters**
   - Deploys global parameters via **Azure REST API**
   - Verifies parameter deployment in the target ADF
  
### 🔑 Required GitHub Secrets
|     Secret Name          |	        Description                    |
|--------------------------|-----------------------------------------|
| AZURE_CLIENT_ID	         | Azure service principal client ID       |
| AZURE_TENANT_ID	         | Azure tenant ID                         |
| AZURE_SUBSCRIPTION_ID	   | Azure subscription ID                   |
| STORAGE_NAME	           | Storage account name for ADF parameters |
| CONTAINER_NAME	         | Blob container name for ADF parameters  |

### 📖 Usage
1. Push to `develop` branch → Deploys to Dev ADF
2. Push to `main` branch → Deploys to Prod ADF with updated global parameters

### 🛠️ Verification
After deployment, the pipeline:
  - Fetches global parameters from the target ADF
  - Logs deployed parameter values
  - Confirms successful deployment

### ⚠️ Notes
- Global parameters are automatically transformed for production (ensures correct values and schema).
- The pipeline uses `jq` for JSON manipulation and validates before deployment.
- REST API is used for global parameter deployment since ARM templates do not always cover updates.

### ✅ Example Workflow
- Developer merges a feature branch → `develop` → Deploys to dev-mitch-adf-cicd
- When stable → Merge `develop` into `main` → Deploys to prod-mitch-adf-cicd + updates prod global parameters

  
