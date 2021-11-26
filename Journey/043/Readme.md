# Azure Active Directory 
Azure Active Directory (Azure AD) is a **centralized identity provider** in the cloud. This is the primary **built-in authentication and authorization** service to provide secure access to Azure resources.

An Azure AD tenant can have multiple subscriptions but an Azure subscription can only be associated with one Azure AD tenant.
You can change the Azure AD tenant to which an Azure subscription is associated.

If your subscription expires, you **lose access** to all the other resources associated with the subscription. However, the Azure AD directory **remains** in Azure. You can associate and manage the directory using a different Azure subscription.

You can send **Azure AD activity logs** to **Azure Monitor logs** to enable rich visualizations, monitoring and alerting on the connected data.

### Authorization
Authorization to access Azure resources can be provided by other identity providers by using federation.  A commonly used example of this is to federate your on-premises Active Directory environment with Azure AD and use this federation for authentication and authorization. 


## Azure AD Connect
The Azure Active Directory Connect synchronization services (Azure AD Connect sync) is a main component of Azure AD Connect. It takes care of all the operations that are related to synchronizing identity data between your on-premises environment and Azure AD.


