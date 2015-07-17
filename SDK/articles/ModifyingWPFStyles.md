#Modifying the WPF Styles#
By default, the base implementation of Connected Services will default all the styles to match the light, dark and blue themes of Visual Studio. These styles ship as part of Visual Studio, to support what we call the Signature Dialog theme.

In most cases, you shouldn't have to make changes to the styles. However, if you do need to add a watermark to help customers understand how to use the control, you'll need to tweak the style. However, you'll need to know the base styles so you don't replace the base style, rather enhance it.



## Adding the .NET reference ##
To access the WPF Signature Dialog styles included with Visual Studio, add a reference to: 

- `Microsoft.VisualStudio.Shell.14.0`

## Adding the XAML reference ##
To reference the styles in XAML, add the following XAML reference to the UserControl definition:
`xmlns:vsfx="clr-namespace:Microsoft.VisualStudio.Shell;assembly=Microsoft.VisualStudio.Shell.14.0"` 
 
The Salesforce [ObjectPicker.xaml](https://github.com/developerforce/visual-studio-tools/blob/master/src/Salesforce.VisualStudio.Services/ConnectedService/Views/ObjectPicker.xaml) view now looks like:

    <UserControl x:Class="Salesforce.VisualStudio.Services.ConnectedService.Views.ObjectPicker"
        x:ClassModifier="internal"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:view="clr-namespace:Salesforce.VisualStudio.Services.ConnectedService.Views"
        xmlns:viewModel="clr-namespace:Salesforce.VisualStudio.Services.ConnectedService.ViewModels"
        xmlns:vsfx="clr-namespace:Microsoft.VisualStudio.Shell;assembly=Microsoft.VisualStudio.Shell.14.0">

## Setting the BasedOn property ##
To customize the control, set the relevant properties, but include the [BasedOn](https://msdn.microsoft.com/en-us/library/system.windows.style.basedon.aspx) property to the Visual Studio themes.
For example: `BasedOn="{StaticResource {x:Static vsfx:VsResourceKeys.ThemedDialogTreeViewItemStyleKey}`

    <Style TargetType="TreeViewItem" BasedOn="{StaticResource {x:Static vsfx:VsResourceKeys.ThemedDialogTreeViewItemStyleKey}}">
        <Setter Property="IsExpanded" Value="True" />
        <Setter Property="IsTextSearchEnabled" Value="{Binding IsTextSearchEnabled}" />
        <Setter Property="IsSelected" Value="{Binding IsSelected}" />
        <Setter Property="KeyboardNavigation.AcceptsReturn" Value="True" />
        <Setter Property="view:VirtualToggleButton.IsVirtualToggleButton" Value="True" />
        <Setter Property="view:VirtualToggleButton.IsChecked" Value="{Binding IsChecked}" />

Without setting the [BasedOn](https://msdn.microsoft.com/en-us/library/system.windows.style.basedon.aspx) property to the Visual Studio themes, setting these additional properties would lose the underlying VS themeing support.    


## WPF Styles ##
**Coming Soon:**
*The list of WPF Styles, and the necessary steps.*

## Reference Implementation ##

See the Salesforce Connected Service, [ObjectPicker.xaml](https://github.com/developerforce/visual-studio-tools/blob/master/src/Salesforce.VisualStudio.Services/ConnectedService/Views/ObjectPicker.xaml) for an example.
      
