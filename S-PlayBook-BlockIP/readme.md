## Playbook name

    Sentinel-Playbook-BlockIp

### Short playbook description

    That playbook can be used by the SOC team to have the ability to block IP or IPs based on an Azure sentinel incident.
    The playbook get the current IPs from the named location (predefined) and append the new IP or IPs.


### Prerequisits

    Managed identity needs the following graph (Application) permissions:
    * Policy.Read.All and Policy.ReadWrite.ConditionalAccess
    * Define the NamedLocationID parameter for the logic app
    * Azure conditional access policy with a named location based block setting
    * Azure active directory premium 1 license for all users    
    * Assign the 'system assigned managed identity' to the Azure rbac role 'Azure Sentinel Responder' on the Azure sentinel RG!

### Other informations

    <b>Important!</b> There are the following limitations for named locations in place:

    * Only 2000 IP ranges per named location
    * No private IP ranges can be defined!
    * The number of IP addresses contained in a range is limited. Only CIDR masks greater than /8 are allowed when defining an IP range.

    > <b>To assign the permissions from above please execute the following powershell command in the Azure cloudshell (for example):</b>
    > Keep in mind, the refresh will take some seconds!

    $permissions = @('Policy.Read.All', 'Policy.ReadWrite.ConditionalAccess')

    $GraphAppId = "00000003-0000-0000-c000-000000000000"
    $GraphAppName = "Avl-Playbook-Blockip"

    Connect-AzureAD
    $MSI = (Get-AzureADServicePrincipal -Filter "displayName eq '$GraphAppName'")
    $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"

    foreach($permissionname in $permissions){
    $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $permissionname -and 
    $_.AllowedMemberTypes -contains "Application"}

    New-AzureAdServiceAppRoleAssignment -ObjectId $MSI.ObjectId -PrincipalId $MSI.ObjectId -ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
    }