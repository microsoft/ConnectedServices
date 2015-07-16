## Connected Services SDK Changes between 2015 RC and RTM ##

The following is a list of breaking changes made to the Connected Services SDK from version 2.0.0-beta that shipped with Visual Studio 2015 RC and the final 2.0.0 version that ships with Visual Studio 2015 RTM.

- The **WizardNavigationResult** class was renamed to **[PageNavigationResult](https://msdn.microsoft.com/en-us/library/microsoft.visualstudio.connectedservices.pagenavigationresult.aspx)**.
- **ConnectedServicesManager.ShowProviderConfigurationDialogAsync** was renamed to **[ConfigureServiceAsync](https://msdn.microsoft.com/en-us/library/microsoft.visualstudio.connectedservices.connectedservicesmanager.configureserviceasync.aspx)**.
- The deprecated **ConnectedServiceHyperlinkAuthenticator** class was removed.
- **ConnectedServiceProvider.SupportedProjectTypes** property was removed as this feature was incomplete for RTM 

The following are non-breaking changes that occurred between the RC and the RTM release.

- **ConnectedServicesManager.ConfigureServiceAsync** can optionally take in a **[ConfigureServiceOptions](https://msdn.microsoft.com/en-us/library/microsoft.visualstudio.connectedservices.configureserviceoptions.aspx)** instance.  This allows for user defined "Args" to be passed into the ConnectedService Provider and Handler.  The Args can be retrieved from the **[ConnectedServiceProviderContext](https://msdn.microsoft.com/en-us/library/microsoft.visualstudio.connectedservices.ConnectedServiceProviderContext.aspx)** and **[ConnectedServiceHandlerContext](https://msdn.microsoft.com/en-us/library/microsoft.visualstudio.connectedservices.ConnectedServiceHandlerContext.aspx)** objects. 
- **[ConnectedServicesManager.CanConfigureService](https://msdn.microsoft.com/en-us/library/microsoft.visualstudio.connectedservices.connectedservicesmanager.canconfigureservice.aspx)** was added to check whether a service can be configured in the project.
- **[ConnectedServicesManager.GetConfiguredServices](https://msdn.microsoft.com/en-us/library/microsoft.visualstudio.connectedservices.connectedservicesmanager.GetConfiguredServices.aspx)** was added to retrieve information about the configured services in a project.
- **[ConnectedServiceSinglePage.OnPageLeavingAsync](https://msdn.microsoft.com/en-us/library/microsoft.visualstudio.connectedservices.connectedservicesinglepage.onpageleavingasync.aspx)** was added to support the scenario where the configure dialog should stay open when a user clicks the finish button.
- **[ConnectedServiceUILess](https://msdn.microsoft.com/en-us/library/microsoft.visualstudio.connectedservices.connectedserviceuiless.aspx)** class was introduced to support the scenario when a **ConnectedServiceHandler** can be invoked without prompting the user. 
- **[ConnectedServiceUpdateContext.Version](https://msdn.microsoft.com/en-us/library/microsoft.visualstudio.connectedservices.connectedserviceupdatecontext.version.aspx)** was added to expose the version of the **ConnectedServiceProvider** that was used to previously configure the service.
- **[ConnectedServiceProviderContext.GetServiceFolder](https://msdn.microsoft.com/en-us/library/microsoft.visualstudio.connectedservices.connectedserviceprovidercontext.getservicefolder.aspx)** was added to support checking if a service was already configured in the current project.
- **[ConnectedServiceProviderContext.InitializeUpdateContext](https://msdn.microsoft.com/en-us/library/microsoft.visualstudio.connectedservices.connectedserviceprovidercontext.initializeupdatecontext.aspx)** was added to support initializing a **ConnectedServiceUpdateContext** for a previously configured service.

