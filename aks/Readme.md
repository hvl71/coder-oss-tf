## Getting Coder Installed

1. Create an [Azure Account](https://portal.azure.com/) and [a service principal](https://docs.spacelift.io/integrations/cloud-providers/azure#create-a-service-principal).
2. Fork this repo and set it up with [spacelift.io](https://spacelift.io/) or equivalent
3. Set ARM_CLIENT_ID, ARM_CLIENT_SECRET, ARM_SUBSCRIPTION_ID, ARM_TENANT_ID using the values when you created the service principal.
4. Make sure to set the directory to aks
4. Run and apply the Terraform (takes 10 minutes).

## Coder setup Instructions

1. Navigate to the IP address of the load balancer (Kubernetes services / coder-k8s-cluster / Services & Ingresses.
2. Create the initial username and password.
3. Go to Templates / Kubernetes / Create Workspace and give the workspace a name.
4. Within three minutes, the workspace should launch.
5. Click the code-server button, and start coding.

## Specifically for Terraform Cloud

## to fix https://stackoverflow.com/questions/72681536/azure-cli-path-error-running-in-terraform-cloud

1. Install Azure CLI if not already installed 
    https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-windows?tabs=winget
    Windows: winget install -e --id Microsoft.AzureCLI

2. Create a service principal to authenticate to your subscription
    $ az login
    $ az account list
    $ az account set --subscription="SUBSCRIPTION_ID"
    $ az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/SUBSCRIPTION_ID"

3. Configure these environment variables in a Terraform Cloud variable set
    ARM_CLIENT_ID (Appid from 'az ad sp create-for-rbac' command output above)
    ARM_SUBSCRIPTION_ID (Id from 'az account list' output above)
    ARM_TENANT_ID (tenant from 'az ad sp create-for-rbac' command output above)
    ARM_CLIENT_SECRET (password from 'az ad sp create-for-rbac' command output above)

3. Then this error occured:

Error: creating Cluster: (Managed Cluster Name "coder-k8s-cluster" / Resource Group "coder-resources"): containerservice.ManagedClustersClient#CreateOrUpdate: Failure sending request: StatusCode=400 -- Original Error: Code="QuotaExceeded" Message="Provisioning of resource(s) for container service coder-k8s-cluster in resource group coder-resources failed. Message: Operation could not be completed as it results in exceeding approved standardDADSv5Family Cores quota. Additional details - Deployment Model: Resource Manager, Location: centralus, Current Limit: 0, Current Usage: 0, Additional Required: 2, (Minimum) New Limit Required: 2. Submit a request for Quota increase at https://aka.ms/ProdportalCRP/#blade/Microsoft_Azure_Capacity/UsageAndQuota.ReactView/Parameters/%7B%22subscriptionId%22:%22REMOVED%22,%22command%22:%22openQuotaApprovalBlade%22,%22quotas%22:[%7B%22location%22:%22centralus%22,%22providerId%22:%22Microsoft.Compute%22,%22resourceName%22:%22standardDADSv5Family%22,%22quotaRequest%22:%7B%22properties%22:%7B%22limit%22:2,%22unit%22:%22Count%22,%22name%22:%7B%22value%22:%22standardDADSv5Family%22%7D%7D%7D%7D]%7D by specifying parameters listed in the ‘Details’ section for deployment to succeed. Please read more about quota limits at https://docs.microsoft.com/en-us/azure/azure-supportability/per-vm-quota-requests. Details: "

Login to Azure Portal, search for 'quotas' and search for standardDADSv5Family in the region where you plan on creating the AKS cluster. Increase quota and try again.
