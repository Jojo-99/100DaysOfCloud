## Azure Monitor 
Azure Monitor is used to **monitoring the health of Azure services**. 

Azure Monitor maximizes the availability and performance of your applications and services by delivering a comprehensive solution for collecting, analyzing, and acting on telemetry from your **cloud** and **on-premises** environments. It helps you understand how your applications are performing and proactively identifies issues affecting them and the resources they depend on.

Azure Monitor uses **Target Resource**, which is the scope and signals available for alerting. A **target** can be **any Azure resource**. Example targets: a virtual machine, a storage account, a virtual machine scale set, a Log Analytics workspace, or an Application Insights resource

### Collected data types - metrics & logs
All data collected by Azure Monitor fits into one of two fundamental types, metrics and logs (including Azure AD activity logs). 
1. Activity **logs** record when resources are created or modified. 
2. **Metrics** tell you how the resource is performing and the resources that it's consuming.
   
Azure Monitor can consolidate log entries from **multiple Azure resources**, subscriptions, and tenants into one location for analysis together.

Activity logs are kept for **90 days**. You can query for any range of dates, as long as the starting date isn't more than 90 days in the past.

### can create alerts in Azure Monitor
**Alerts** in Azure Monitor proactively notify you of critical conditions and potentially attempt to take corrective action.   - **Automotion Actions**

Alert rules based on **metrics** provide near *real time* alerting based on numeric values, while rules based on **logs** allow for complex logic *across data* from *multiple sources*. 