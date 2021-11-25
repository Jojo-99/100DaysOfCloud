# Azure security and network security - 2
## Azure Key Vault
Azure Key Vault is a secure store for storage various types of **sensitive information**. (eg. administrative credentials)

Azure Key Vault can be used to Securely store and tightly control access to tokens, passwords, certificates, API keys, and other secrets.
Access to a key vault requires proper authentication and authorization before a caller (user or application) can get access. Authentication establishes the identity of the caller, while authorization determines the operations that they are allowed to perform.

Centralizing storage of **application secrets** in Azure Key Vault allows you to control their distribution. Key Vault greatly reduces the chances that secrets may be accidentally leaked. When using Key Vault, application developers no longer need to store security information in their application. Not having to store security information in applications eliminates the need to make this information part of the code. For example, an application may need to connect to a database. Instead of storing the connection string in the app's code, you can store it securely in Key Vault.

## Azure Information Protection
Azure Information Protection can **encrypt documents and emails**. Azure Information Protection is a cloud-based solution that helps an organization to **classify** and optionally, protect its documents and emails by applying **labels**. 

Azure Information Protection is used to **automatically add a watermark** to Microsoft Word documents that contain credit card information.
You use Azure Information Protection labels to apply classification to documents and emails. When you do this, the classification is identifiable regardless of where the data is stored or with whom it's shared. The labels can include visual markings such as a header, footer, or watermark.

Labels can be applied automatically by administrators who define rules and conditions, manually by users, or a combination where users are given recommendations.
The protection technology uses **Azure Rights Management** (often abbreviated to Azure RMS). This technology is integrated with other Microsoft cloud services and applications, such as Office 365 and Azure Active Directory.
This protection technology uses encryption, identity, and authorization policies. Similarly to the labels that are applied, protection that is applied by using Rights Management stays with the documents and emails, independently of the location ?inside or outside your organization, networks, file servers, and applications.

## Azure Distributed Denial of Service (DDoS)
DDoS is a form of attack on a network resource. A DDoS protection plan is used to protect against DDoS attacks. 

Azure has **two DDoS service** offerings that provide protection from network attacks: **DDoS Protection Basic** and **DDoS Protection Standard**. 

DDoS **Basic** protection is integrated into the Azure platform by default and at no extra cost.
You have the option of paying for DDoS **Standard**. It has several advantages over the basic service, including **logging, alerting, and telemetry**. DDoS **Standard** can generate reports that contain details of attempted attacks as required in this question.