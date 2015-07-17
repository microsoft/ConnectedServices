# Supporting Connected Services Across Multiple Projects #
Connected Services is designed to support all Visual Studio projects from C#/VB.NET Console apps, ASP.NET Web Apps, Windows Universal Projects, Xamarin, Cordova to Python. 
Rather than attempting to build a solution where the Connected Service Author exposes a metadata model and a Connected Service parser generates the output (code and config) in all languages and project types, Connected Services supports a concept referred to as handlers. 

![](./media/CreatingAConnectedServiceExtension/ProviderInstanceHandler.jpg)

A [ConnectedServiceHander](handler) is a way to represent a group of similar projects you wish to make changes to. For instance, the configuration and code changes you'd make to a C# Console app are likely the same you'd make to a C# WinForm project. They share the same config file format and the C# code you'd add references the same .NET Framework However, if you were also targeting a Cordova project, you'd likely ask different questions in your Configurator, make different changes config management is different and of course you'd scaffold JavaScript code instead of C# code. In this case, you'd have two handlers. One for C# .NET projects, and another handler for Cordova. 

A [ConnectedServiceHandler](handler) is responsible for taking the configuration information that the app developer specifies in the [configurator](configurator) and performs the actions in the Handler. The changes range from configuring the service to be consumed (such as OAuth configuration) and modifying the project to consume the selected service. These modifications include adding values to app or app/web.config files, adding References, NuGets, scaffolding code, etc. 

## Prompting for different questions based on the project type ##
If your Connected Service supports more than one handler, and the questions may vary based on the current project, you'll likely want to ask the questions relevant to the specific project, or even some details related to the project. For instance, in the Azure Active Directory Connected Service, the developer is prompted different whether the project support is considered a WebAPI or MVC project. In the Salesforce case, the developer is prompted for Web Server Flow authentication in a Web App. If the project were a WinForms or Console App, it should hide that option. In the Azure Active Directory case, this was implemented, however as of Visual Studio 2015 RTM, we had not yet implemented the project detection.
See the [tracking/enhancement Issue](https://github.com/Microsoft/ConnectedServicesSdkSamples/issues/4) for the current status. 
 
## Q&A ##

- **Q: Can a [ConnectedServiceHandler](handler) be shipped in a separate extension?**
- **A:** In theory yes, but this scenario isn't supported nor tested. Connected Services uses MEF, which means Visual Studio will find any components that expose the appropriate [ConnectedServiceHanderExportAttribute](handlerexportattribute). However you can also wind up with several handers with the same [ProviderId](providerid), and Connected Services doesn't yet implement any choosers allowing the developer to choose which handler will be used.      

[handler]: https://msdn.microsoft.com/library/microsoft.visualstudio.connectedservices.connectedservicehandler.aspx
[configurator]: https://msdn.microsoft.com/library/microsoft.visualstudio.connectedservices.connectedserviceconfigurator.aspx
[handlerexportattribute]: https://msdn.microsoft.com/library/microsoft.visualstudio.connectedservices.connectedservicehandlerexportattribute.aspx
[providerid]: https://msdn.microsoft.com/library/microsoft.visualstudio.connectedservices.connectedserviceproviderexportattribute.providerid.aspx


