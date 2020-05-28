# GitHub Action for Azure Resource Manager

With ARM GitHub Action, you can automate your workflow by executing to deploy ARM templates and manage Azure resources.

The action executes the Azure CLI Bash script for deployment. The parameters for the Action are:

- `location` – **Required** - The location of Resource Group
- `resource-group` – **Required** - The name of Resource Group
- `template-file` or `template-uri` – **Required** Either the local template location or the template URI must be provided
- `parameters` – **Optional**
- `deploymentMode` – **Optional** - Incremental (only add resources to resource group) or Complete (remove extra resources from resource group). Default: Incremental.
- `functionality` – **Optional** - Specify if the template is for creation or deletion of the resources. Default: Create, Accepted Values: Create, Delete

The definition of this GitHub Action is in [action.yml].

## Sample workflow 

### Dependencies on other GitHub Actions
* [Azure Login](https://github.com/Azure/login) – **Required** Login with your Azure credentials 
* [Checkout](https://github.com/actions/checkout) – **Required** To execute the scripts present in your repository

### Workflow for template deployment using template location
```
# File: .github/workflows/workflow.yml

on: [push]

name: AzureARMSample

jobs:

  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - name: Checkout
      uses: actions/checkout@v1
      
    - name: Azure ARM Deployment
      uses: azure/ARM@v3
      with:
        location: <Your Resource Group Location>
        resource-group: <Your Resource Group Name>
        template-file: <path/to/azuredeploy.json>
        parameters: <path/to/parameters.json>
        deploymentMode: <Complete/Incremental>
        functionality: <Create>
        
```
### Workflow for template deployment using template URI
```
# File: .github/workflows/workflow.yml

on: [push]

name: AzureARMSample

jobs:

  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - name: Checkout
      uses: actions/checkout@v1
      
    - name: Azure ARM Deployment
      uses: azure/ARM@v3
      with:
        location: <Your Resource Group Location>
        resource-group: <Your Resource Group Name>
        template-uri: <URI of your ARM template>
        parameters: <path/to/parameters.json>
        deploymentMode: <Complete/Incremental>
        functionality: <Create>
```
  * [GITHUB_WORKSPACE](https://help.github.com/en/github/automating-your-workflow-with-github-actions/virtual-environments-for-github-hosted-runners) is the environment variable provided by GitHub which represents the root of your repository.

### Output of the Action:

Each ARM template deployment will result in export of an output, that can be used in subsequent Actions. For example the output is called resourceID then it will be accessible with ${{ steps.STEP.outputs.resourceID }}

### Configure Azure credentials as GitHub Secret:

To use any credentials like Azure Service Principal,add them as [secrets](https://help.github.com/en/articles/virtual-environments-for-github-actions#creating-and-using-secrets-encrypted-variables) in the GitHub repository and then use them in the workflow.

Follow the steps to configure the secret:
  * Define a new secret under your repository settings, Add secret menu
  * Store the output of the below [az cli](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest) command as the value of secret variable 'AZURE_CREDENTIALS'
```bash  

   az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
  # Replace {subscription-id}, {resource-group} with the subscription, resource group details

  # The command should output a JSON object similar to this:

  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
  
```
  * Now in the workflow file in your branch: `.github/workflows/workflow.yml` replace the secret in Azure login action with your secret (Refer to the example above)


# Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.opensource.microsoft.com.

When you submit a pull request, a CLA bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

