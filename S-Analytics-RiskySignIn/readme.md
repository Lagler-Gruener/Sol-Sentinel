## Analytics Rule and LogicApp name

    S-Analytics-Aad-RiskySignIns
    LogicApp-GetAADRiskySignIns

<hr/>

### Short solution description

    In that solution, I've developed an Azure LogicApp that collects all risky sign-ins and save the output into a custom
    LogAnalytics table named 'AADRiskySignIns_CL'
    I've also created an Azure sentinel analytics rule which covers those logs and creates an incident if needed.

<hr/>

### Prerequisits

    The system assigned managed identity from the Azure logic App needs the Azure graph permission:

    * IdentityRiskEvent.Read.All
    * Azure active directory premium 1 license for all users        

<hr/>

### Other informations

The following GraphAPI is used by the Azure LogicApp:

```http    
GET /identityProtection/riskDetections
```
    
Keep in mind, that API is available with the Azure AD P1 and P2 licenses.
There is also another API available:

```http
GET /identityProtection/riskyUsers
```
That API is only with the Azure AD P2 license available!

<hr/>

### Deploy the solution

#### 1.) Install the Azure logic app
Open the "Template deployment service" and execute the template /LogicApp/logicapptemplate.json

[![Deploy Azure storage account](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FLagler-Gruener%2FSol-Sentinel%2Fmaster%2FS-Analytics-RiskySignIn%2FLogicApp%2Flogicapptemplate.json)

#### 2.) To assign the permission

Please execute the following PowerShell script:

```powershell

$LogicAppMIName = "S-Analytics-Aad-RiskySignIns"
$permissions = @('IdentityRiskEvent.Read.All')
$GraphAppId = "00000003-0000-0000-c000-000000000000"    

Connect-AzureAD 
$MSI = (Get-AzureADServicePrincipal -Filter "displayName eq '$LogicAppMIName'")
$GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"

foreach($permissionname in $permissions){    
$AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $permissionname -and $_.AllowedMemberTypes -contains "Application"}
New-AzureAdServiceAppRoleAssignment -ObjectId $MSI.ObjectId -PrincipalId $MSI.ObjectId -ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
}

```

I always prefer the Azure cloudshell!

#### 3.) Execute the LogicApp
It's important, that you execute the LogicApp once to create the custom log named 'AADRiskySignIns_CL'

#### 4.) Review the custom log createn and deploy the sentinel analytics rule
Please review the creation of the custom log in azure log analytics.
If the log isn't created, please enforce a risky sign-in (login with a Tor browser for example), wait 5 minutes, and execute the LogicApp once more.
If the log is created, please execute the Azure sentinel analytics rule template.

[![Deploy Azure storage account](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FLagler-Gruener%2FSol-Sentinel%2Fmaster%2FS-Analytics-RiskySignIn%2FRule%2Falertrule-template.json)