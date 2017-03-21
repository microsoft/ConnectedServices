# Adding custom context menu to provider folder node

## Service provider logic
1. Providing a custom context menu is optional.
2. Service providers can register their custom context commands with VS by using a VSCT file. Through their VSCT file the providers will control grouping of their commands and their relative positions in the context menu. They will parent their command groups under guid="guidSHLMainMenu" id="IDM_VS_CTXT_FOLDERNODE" which are well-known values. The visibility of these commands will be constrained based on a custom UIContext unique to that provider.
Note that this is all standard Visual Studio command authoring code and nothing Connected Services specific.
3. Service providers will use a predetermined guid based on the provider id as their UIContext guid. Note: generating this predetermined guid is something that the provider author will do offline and only once per each provider id. Once generated they can hardcode that guid in their code.

## Getting UIContextGuidFromProviderId
Steps to generate UIContext guid based on the  provider id string:
1. Get a byte array representing the UTF8 encoding of the provider id string. This is the same provider id string that a provider specifies in the exisiting ConnectedServiceProviderExport attribute.
2. Generate a SHA256 hash of this byte array.
3. Use the most significant 16 bytes to generate a guid.

### C# code snippet to get UIContext guid from a given provider id
```c#
static Guid GetUIContextGuidFromProviderId(string providerId)
{
    byte[] bytes = Encoding.UTF8.GetBytes(providerId);

    SHA256 mySHA256 = SHA256Managed.Create();
    byte[] hashValue = mySHA256.ComputeHash(bytes);

    return new Guid(hashValue.Take(16).ToArray());
}
```
### Powershell script to get UIContext guid from a given provider id
```powershell
function GetUIContextGuidFromProviderId([string]$providerId)
{
   $toHash = [System.Text.Encoding]::UTF8.GetBytes($providerId)
   $hasher = new-object System.Security.Cryptography.SHA256Managed
   $hashByteArray = $hasher.ComputeHash($toHash)

   $hashByteArray = $hashByteArray[0..15]

   [UInt32]$a = [Convert]::ToUInt32(([Convert]::ToUInt32($hashByteArray[3]) -shl 24) -bor ([Convert]::ToUInt32($hashByteArray[2]) -shl 16) -bor ([Convert]::ToUInt32($hashByteArray[1]) -shl 8) -bor ($hashByteArray[0]))
   [UInt16]$b = [Convert]::ToUInt16(([Convert]::ToUInt32($hashByteArray[5]) -shl 8) -bor ($hashByteArray[4]))
   [UInt16]$c = [Convert]::ToUInt16(([Convert]::ToUInt32($hashByteArray[7]) -shl 8) -bor ($hashByteArray[6]))

   [Byte]$d = $hashByteArray[8]
   [Byte]$e = $hashByteArray[9]
   [Byte]$f = $hashByteArray[10]
   [Byte]$g = $hashByteArray[11]
   [Byte]$h = $hashByteArray[12]
   [Byte]$i = $hashByteArray[13]
   [Byte]$j = $hashByteArray[14]
   [Byte]$k = $hashByteArray[15]

   [Guid]$guid = new-object Guid($a, $b, $c, $d, $e, $f, $g, $h, $i, $j, $k)

   return $guid
}
```

## Sample VSCT file
```xml
<?xml version="1.0" encoding="utf-8"?>
<CommandTable xmlns="http://schemas.microsoft.com/VisualStudio/2005-10-18/CommandTable" xmlns:xs="http://www.w3.org/2001/XMLSchema">
  <Extern href="stdidcmd.h"/>
  <Extern href="vsshlids.h"/>
  <Commands package="guidTestConnectedServicesCommandPackage">
    <Groups>
      <Group guid="guidConnectedServicesTestContextMenuCmdSet" id="ConnectedServicesTestContextMenuGroup">
        <Parent guid="guidSHLMainMenu" id="IDM_VS_CTXT_FOLDERNODE"/>
      </Group>
    </Groups>
    <Buttons>
      <!-- Custom context menu commands -->
      <Button guid="guidConnectedServicesTestContextMenuCmdSet" id="ConnectedServicesTestContextMenuCmd" priority="0x0100" type="Button">
        <Parent guid="guidConnectedServicesTestContextMenuCmdSet" id="ConnectedServicesTestContextMenuGroup" />
        <CommandFlag>DynamicVisibility</CommandFlag>
        <CommandFlag>DefaultInvisible</CommandFlag>
        <Strings>
          <ButtonText>Custom Context Menu Command</ButtonText>
        </Strings>
      </Button>
      <Button guid="guidConnectedServicesTestContextMenuCmdSet" id="ConnectedServicesTestContextMenuCsharpCmd" priority="0x0200" type="Button">
        <Parent guid="guidConnectedServicesTestContextMenuCmdSet" id="ConnectedServicesTestContextMenuGroup" />
        <CommandFlag>DynamicVisibility</CommandFlag>
        <CommandFlag>DefaultInvisible</CommandFlag>
        <Strings>
          <ButtonText>Custom Context Menu Command for C# projects</ButtonText>
        </Strings>
      </Button>
    </Buttons>
  </Commands>
  <VisibilityConstraints>
    <VisibilityItem guid="guidConnectedServicesTestContextMenuCmdSet" id="ConnectedServicesTestContextMenuCmd"  context="guidContextMenuUIContext"/>
    <VisibilityItem guid="guidConnectedServicesTestContextMenuCmdSet" id="ConnectedServicesTestContextMenuCsharpCmd"  context="guidContextMenuUIContext"/>
  </VisibilityConstraints>
  <Symbols>
    <GuidSymbol name="guidTestConnectedServicesCommandPackage" value="{8EC404F4-F8AF-46C3-A2D5-1D95AA6C7AF4}">
    </GuidSymbol>
    <!-- This UIContext corresponds to provider id "Microsoft.NonShipping.CustomContextMenu" -->
    <GuidSymbol name="guidContextMenuUIContext" value="{41cb04f3-727e-7341-707f-0d3e01a95993}" />
    <GuidSymbol name="guidConnectedServicesTestContextMenuCmdSet" value="{6C8CF7BC-4962-4FE0-87A4-4F9DB8BE28B0}">
      <IDSymbol name="ConnectedServicesTestContextMenuGroup" value="0x1000" />
      <IDSymbol name="ConnectedServicesTestContextMenuCmd" value="0x0100" />
      <IDSymbol name="ConnectedServicesTestContextMenuCsharpCmd" value="0x0200" />
    </GuidSymbol>
  </Symbols>
</CommandTable>
```

## Sample provider package class
```c#
[PackageRegistration(UseManagedResourcesOnly = true)]
[ProvideMenuResource("menus.ctmenu", 1)]
[ProvideBindingPath]
[Guid(PackageConstants.PackageGuidString)]
// Autoload on ContextMenuUIContextGuid is NOT recommended to ensure package is loaded only when actually needed. However, if a provider wants
// to add some custom logic for command visibility that's in addition to VisibilityConstraints in the vsct file then Autoload on ContextMenuUIContextGuid
// would be needed. For example a provider might want to show a custom context menu only if the selected provider folder node belongs to a C#
// project then it would need to add this additional custom logic in the IOleCommandTarget.QueryStatus as shown below for command
// ConnectedServicesTestContextMenuCsharpCmdId. If such a custom logic is not needed and the VisibilityConstraints in the vsct file is good enough
// then Autoload on ContextMenuUIContextGuid is not needed. For example if this provider only supported commands like ConnectedServicesTestContextMenuCmdId
// then this Autoload would not be needed.
[ProvideAutoLoad(PackageConstants.ContextMenuUIContextGuid_String)]
internal sealed class TestPackage : Package, IOleCommandTarget
{

    #region IOleCommandTarget members

    int IOleCommandTarget.QueryStatus(ref Guid pguidCmdGroup, uint cCmds, OLECMD[] prgCmds, IntPtr pCmdText)
    {
        if (pguidCmdGroup == PackageConstants.ConnectedServicesTestContextMenuCmdSet)
        {
            switch (prgCmds[0].cmdID)
            {
                case PackageConstants.ConnectedServicesTestContextMenuCsharpCmdId:
                    var uiContext = UIContext.FromUIContextGuid(PackageConstants.ContextMenuUIContextGuid);
                    if (uiContext != null && uiContext.IsActive && this.DoesCapabilityMatchForCurrentProject("Csharp"))
                    {
                        prgCmds[0].cmdf = (uint)(OLECMDF.OLECMDF_SUPPORTED | OLECMDF.OLECMDF_ENABLED);
                    }
                    else
                    {
                        prgCmds[0].cmdf = (uint)(OLECMDF.OLECMDF_SUPPORTED | OLECMDF.OLECMDF_INVISIBLE);
                    }
                    return VSConstants.S_OK;
            }
        }

        return (int)OLE.Interop.Constants.OLECMDERR_E_NOTSUPPORTED;
    }

    int IOleCommandTarget.Exec(ref Guid commandGroup, uint commandId, uint commandExecOpt, IntPtr variantIn, IntPtr variantOut)
    {
        if (commandGroup == PackageConstants.ConnectedServicesTestContextMenuCmdSet)
        {
            switch (commandId)
            {
                case PackageConstants.ConnectedServicesTestContextMenuCmdId:
                    VsShellUtilities.ShowMessageBox(
                                                    this,
                                                    "Custom context menu command executed",
                                                    "Custom context menu command",
                                                    OLEMSGICON.OLEMSGICON_INFO,
                                                   OLEMSGBUTTON.OLEMSGBUTTON_OK,
                                                    OLEMSGDEFBUTTON.OLEMSGDEFBUTTON_FIRST);
                    return VSConstants.S_OK;

                case PackageConstants.ConnectedServicesTestContextMenuCsharpCmdId:
                    VsShellUtilities.ShowMessageBox(
                                                    this,
                                                    "Custom context menu command for C# project executed",
                                                    "Custom context menu command",
                                                    OLEMSGICON.OLEMSGICON_INFO,
                                                    OLEMSGBUTTON.OLEMSGBUTTON_OK,
                                                    OLEMSGDEFBUTTON.OLEMSGDEFBUTTON_FIRST);
                    return VSConstants.S_OK;
            }
        }

        return (int)OLE.Interop.Constants.OLECMDERR_E_NOTSUPPORTED;
    }

    #endregion IOleCommandTarget members

    private bool DoesCapabilityMatchForCurrentProject(string capabilityAppliesToExpression)
    {
        IVsHierarchy hierarchy = null;
        uint itemid = VSConstants.VSITEMID_NIL;

        var monitor = (IVsMonitorSelection)Package.GetGlobalService(typeof(IVsMonitorSelection));
        IntPtr currentHierarchy;
        IVsMultiItemSelect mis;
        IntPtr container;

        ErrorHandler.ThrowOnFailure(monitor.GetCurrentSelection(out currentHierarchy, out itemid, out mis, out container));
        try
        {
            if (currentHierarchy != IntPtr.Zero && mis == null)
            {
                hierarchy = Marshal.GetObjectForIUnknown(currentHierarchy) as IVsHierarchy;
                return PackageUtilities.IsCapabilityMatch(hierarchy, capabilityAppliesToExpression);
            }

            return false;
        }
        finally
        {
            if (currentHierarchy != IntPtr.Zero)
            {
                Marshal.Release(currentHierarchy);
            }
            if (container != IntPtr.Zero)
            {
                Marshal.Release(container);
            }
        }
    }
}

internal static class PackageConstants
{
    /// <summary>
    /// Package GUID string.
    /// </summary>
    public const string PackageGuidString = "8EC404F4-F8AF-46C3-A2D5-1D95AA6C7AF4";

    /// <summary>
    /// Custom context menu cmd set guid
    /// </summary>
    public static readonly Guid ConnectedServicesTestContextMenuCmdSet = new Guid("6C8CF7BC-4962-4FE0-87A4-4F9DB8BE28B0");

    /// <summary>
    /// Custom context menu command
    /// </summary>
    public const int ConnectedServicesTestContextMenuCmdId = 0x0100;

    /// <summary>
    /// Custom Context Menu Command for C# projects
    /// </summary>
    public const int ConnectedServicesTestContextMenuCsharpCmdId = 0x0200;

    /// <summary>
    /// This UIContext corresponds to provider id "Microsoft.NonShipping.CustomContextMenu"
    /// </summary>
    public const string ContextMenuUIContextGuid_String = "41cb04f3-727e-7341-707f-0d3e01a95993";

    /// <summary>
    /// This UIContext corresponds to provider id "Microsoft.NonShipping.CustomContextMenu"
    /// </summary>
    public static readonly Guid ContextMenuUIContextGuid = new Guid(ContextMenuUIContextGuid_String);
}
```
