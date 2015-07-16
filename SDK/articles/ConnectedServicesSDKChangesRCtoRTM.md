## Connected Services SDK Changes between 2015 RC and RTM ##

The following is a list of breaking changes made to the Connected Services SDK from version 2.0.0-beta that shipped with VS 2015 RC and the official 2.0.0 version that ships with VS 2015 RTM.

- The **WizardNavigationResult** class was renamed to **PageNavigationResult**.
- **ConnectedServicesManager**.*ShowProviderConfigurationDialogAsync* was renamed to *ConfigureServiceAsync*.
- The deprecated **ConnectedServiceHyperlinkAuthenticator** class was removed.
- **ConnectedServiceProvider**.*SupportedProjectTypes* property was removed because it is not used.

The following are non-breaking changes that occurred between the RC and the RTM release.

- **ConnectedServicesManager**.*ConfigureServiceAsync* can optionally take in a **ConfigureServiceOptions** instance.  This allows for user defined "Args" to be passed into the ConnectedService Provider and Handler.  The Args can be retrieved from the **ConnectedServiceProviderContext** and **ConnectedServiceHandlerContext** objects. 
- **ConnectedServicesManager**.*CanConfigureService* was added to check whether a service can be configured in the project.
- **ConnectedServicesManager**.*GetConfiguredServices* was added to retrieve information about the configured services in a project.
- **ConnectedServiceSinglePage**.*OnPageLeavingAsync* was added to support the scenario where the configure dialog should stay open when a user clicks the finish button.
- **ConnectedServiceUILess** class was introduced to support the scenario when a **ConnectedServiceHandler** can be invoked without prompting the user. 
- **ConnectedServiceUpdateContext**.*Version* was added to expose the version of the **ConnectedServiceProvider** that was used to previously configure the service.
- **ConnectedServiceProviderContext**.*GetServiceFolder* was added to support checking if a service was already configured in the current project.
- - **ConnectedServiceProviderContext**.*InitializeUpdateContext* was added to support initializing a **ConnectedServiceUpdateContext** for a previously configured service.
