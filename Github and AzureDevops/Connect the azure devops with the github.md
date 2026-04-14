## 🔧 **Steps to Connect Azure DevOps and GitHub with Full Commit History**

### 1️⃣ **Create Personal Access Tokens (PATs)**

- **Azure DevOps PAT**:    
    - Go to **Azure DevOps → User Settings → Personal Access Tokens**.        
    - Click **New Token**, give it a name (e.g., `AzureSync`), set the scope to **Code > Read**, and copy the token.     

 ```
 `GITHUB_TOKEN`: "MyTOKEN"
```

- **GitHub PAT**:    
    - Go to **GitHub → Settings → Developer Settings → Personal Access Tokens → Fine-grained tokens (or classic)**.        
    - Generate a token with **repo** scope (full access to push code).        

```
PALMAZAD_TOKEN: MyTOKEN
`GITHUB_TOKEN`: 
```

---

### 2️⃣ **Create a Variable Group in Azure DevOps**

- Go to **Azure DevOps → Pipelines → Library → Variable Groups**.    
- Create a new group, e.g., `SYNC_TOKENS`.    
- Add:    
    - `AZURE_DEVOPS_PAT` (secret) → paste the Azure DevOps PAT.        
    - `GITHUB_TOKEN` (secret) → paste the GitHub PAT.        
- **Enable “Allow access to all pipelines” or explicitly authorize the pipeline** under **Pipeline permissions**.
    

---

### 3️⃣ **Configure the Azure Pipeline YAML**

- In your Azure DevOps repo, create a file named **`azure-pipelines.yml`** at the repository root.   
- Use this YAML:

```
trigger:
  branches:
    include:
      - main

variables:
- group: SYNC_TOKENS  # Linked to the variable group

pool:
  vmImage: ubuntu-latest

steps:
  - checkout: none

  - script: |
      echo "✅ Checking token lengths"
      echo "AZURE_DEVOPS_PAT length: ${#AZURE_DEVOPS_PAT}"
      echo "GITHUB_TOKEN length: ${#GITHUB_TOKEN}"

      echo "✅ Cloning Azure DevOps repo as a mirror"
      git clone --mirror https://user:$(AZURE_DEVOPS_PAT)@dev.azure.com/ORG_NAME/PROJECT_NAME/_git/REPO_NAME repo.git
      cd repo.git

      echo "✅ Pushing full history (commits, branches, tags) to GitHub"
      git push --mirror https://$(GITHUB_TOKEN)@github.com/GITHUB_USERNAME/GITHUB_REPO.git
    displayName: 'Mirror Azure DevOps Repo to GitHub'

```

### 4️⃣ **Ensure Permissions**

- In Azure DevOps:    
    - Go to **Pipelines > Library > Variable Groups > SYNC_TOKENS**.        
    - Open **Pipeline permissions** and explicitly authorize your pipeline.        

---

### 5️⃣ **Run the Pipeline**

- Commit the YAML file to your Azure DevOps repo.    
- Run the pipeline from **Pipelines → Run**.    
- The job will:    
    1. Clone your **Azure DevOps repo (mirror)** using the PAT.        
    2. Push the **entire commit history** (branches, tags, commits) to your GitHub repo.        

---

### 6️⃣ **Verify on GitHub**

- Go to your GitHub repository.    
- You should see all commits, branches, and history mirrored from Azure DevOps.    

---

### ✅ **Key Points We Resolved**

- Initially, PAT variables were added incorrectly to **Pipeline Variables** instead of **Variable Group**.    
- Variable Group permissions were missing (pipeline couldn’t access secrets).    
- Added debugging (`printenv`) to verify tokens.    
- Unified tokens (`AZURE_DEVOPS_PAT` & `GITHUB_TOKEN`) into a single variable group (`SYNC_TOKENS`).