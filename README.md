# Sitecore ARM Template: XP0 With CD
## Sitecore 8.2 Update-4

This folder contains Azure Resource Manager templates compatible with WebDeploy packages for **Sitecore 8.2 rev. 170614 (Update-4)**.

Notice: These templates configure Sitecore to use Preview version of Azure Search service out of the box.

### Download:
1. Sitecore Azure Toolkit [https://dev.sitecore.net/Downloads/Sitecore_Azure_Toolkit.aspx](https://dev.sitecore.net/Downloads/Sitecore_Azure_Toolkit.aspx) 
	* Such as `C:\Tools\Sitecore Azure Toolkit`
2. Sitecore WebDeploy Packages XP0 and XP [https://dev.sitecore.net/Downloads/Sitecore_Experience_Platform/82/Sitecore_Experience_Platform_82_Update4.aspx](https://dev.sitecore.net/Downloads/Sitecore_Experience_Platform/82/Sitecore_Experience_Platform_82_Update4.aspx)


The two packages to use will be XP0 single for the CMS and XP CD for the CD role.

Unzip CD from XP and make the following modifications
	parameters.xml
	Add:
	<parameter name="Tracking History Connection String" description="MongoDB Connection string to enter into config" tags="Hidden,NoStore">
    		<parameterEntry kind="XmlFile" scope="App_Config\\ConnectionStrings\.config$" match="//connectionStrings/add[@name='tracking.history']/@connectionString" />
	</parameter>
	
	\Content\Website\App_Config\ConnectionStrings.config
	Add:
  		<add name="tracking.history" connectionString="mongodb://localhost/Sitecore_tracking_history"/>
  		<add name="reporting" connectionString="user id=user;password=password;Data Source=(server);Database=Sitecore_Analytics"/>
  
### Upload Web Deploy Packages
Rezip the CD and upload to a publicly accessible location. Single web deploy package can be used as is.
Can use blob storage from azure, create a container and set it's access to be publicly available. Then remove access or delete resource when done.
  
### Upload ARM Templates:
Also upload the arm templates to a publicly accessible location. Maybe the same blob storage?

- \Sitecore Arm XP0-CD\Sitecore 8.2.4\xp0-cd\azuredeploy.json
- \Sitecore Arm XP0-CD\Sitecore 8.2.4\xp0-cd\addons\bootloader.json
- \Sitecore Arm XP0-CD\Sitecore 8.2.4\xp0-cd\addons\generic.json
- \Sitecore Arm XP0-CD\Sitecore 8.2.4\xp0-cd\nested\application.json
- \Sitecore Arm XP0-CD\Sitecore 8.2.4\xp0-cd\nested\emptyAddon.json
- \Sitecore Arm XP0-CD\Sitecore 8.2.4\xp0-cd\nested\infrastructure.json

### Update Parameters
Set the parameters on your local file system. This will be passed into Sitecore Azure Toolkit using powershell
- \Sitecore Arm XP0-CD\Sitecore 8.2.4\xp0-cd\azuredeploy.parameters.json
  
### Run Powershell
* `> Install-Module AzureRM -AllowClobber`
* `> Login-AzureRmAccount`
	* Enter credentials
* `> cd  C:\Tools\Sitecore Azure Toolkit`
* `> Import-Module .\tools\Sitecore.Cloud.Cmdlets.psm1 -Verbose`
* `> Start-SitecoreAzureDeployment -location "EastUS" -Name "Bobcat" -ArmTemplateUrl "https://yourwdp.blob.core.windows.net/xp0-cd/azuredeploy.json" -ArmParametersPath "C:\Projects\Sitecore Arm XP0-CD\Sitecore 8.2.4\xp0-cd\azuredeploy.parameters.json" -LicenseXmlPath "C:\Path\To\license.xml"`


I'll be honest. I copied the contents of the license.xml directly into the parameters file.