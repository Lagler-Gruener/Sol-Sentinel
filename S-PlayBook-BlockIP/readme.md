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


[![Deploy Azure storage account](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FLagler-Gruener%2FSol-DeploySentinelPlaybooks%2Fmain%2FPlaybook%2Fdeployplaybook.json)