# [WPF 常用的9种布局方式](https://blog.csdn.net/qq_30725967/article/details/125821560)

[一、Grid](https://blog.csdn.net/qq_30725967/article/details/125821560#t0)

[二、StackPanel](https://blog.csdn.net/qq_30725967/article/details/125821560#t1)

[三、WrapPanel](https://blog.csdn.net/qq_30725967/article/details/125821560#t2)

[四、DockPanel](https://blog.csdn.net/qq_30725967/article/details/125821560#t3)

[五、 UniformGrid](https://blog.csdn.net/qq_30725967/article/details/125821560#t4)

[六、Canvas](https://blog.csdn.net/qq_30725967/article/details/125821560#t5)

[七、ScrollViewer](https://blog.csdn.net/qq_30725967/article/details/125821560#t6)

[八、ViewBox](https://blog.csdn.net/qq_30725967/article/details/125821560#t7)

[九、Border](https://blog.csdn.net/qq_30725967/article/details/125821560#t8)



# WPF的全部控件

https://learn.microsoft.com/en-us/dotnet/desktop/wpf/controls/controls-by-category?view=netframeworkdesktop-4.8

https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls?view=windowsdesktop-6.0

## Layout

Layout controls are used to manage the size, dimensions, position, and arrangement of child elements.

- [Border](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.border)
- [BulletDecorator](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.primitives.bulletdecorator)
- [Canvas](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.canvas)
- [DockPanel](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.dockpanel)
- [Expander](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.expander)
- [Grid](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.grid)
- [GridSplitter](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.gridsplitter)
- [GroupBox](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.groupbox)
- [Panel](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.panel)
- [ResizeGrip](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.primitives.resizegrip)
- [Separator](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.separator)
- [ScrollBar](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.primitives.scrollbar)
- [ScrollViewer](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.scrollviewer)
- [StackPanel](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.stackpanel)
- [Thumb](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.primitives.thumb)
- [Viewbox](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.viewbox)
- [VirtualizingStackPanel](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.virtualizingstackpanel)
- [Window](https://learn.microsoft.com/en-us/dotnet/api/system.windows.window)
- [WrapPanel](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.wrappanel)

## Buttons

Buttons are one of the most basic user interface controls. Applications typically perform some task in the [Click](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.primitives.buttonbase.click) event when a user clicks on them.

- [Button](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.button)
	- API案例 https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.button?view=windowsdesktop-6.0
- [RepeatButton](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.primitives.repeatbutton)

## Data Display

Data display controls are used to show information from a data source.

- [DataGrid](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.datagrid)
- [ListView](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.listview)
- [TreeView](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.treeview)

## Date Display and Selection

Date controls are used to display and select calendar information.

- [Calendar](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.calendar)
- [DatePicker](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.datepicker)

## Menus

Menus are used to group related actions or to provide contextual assistance.

- [ContextMenu](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.contextmenu)
- [Menu](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.menu)
- [ToolBar](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.toolbar)

## Selection

Selection controls are used to enable a user to select one or more options.

- [CheckBox](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.checkbox)
- [ComboBox](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.combobox)
- [ListBox](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.listbox)
- [RadioButton](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.radiobutton)
- [Slider](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.slider)

## Navigation

Navigation controls enhance or extend the application navigation experience by creating targeting frames or tabbed application appearance.

- [Frame](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.frame)
- [Hyperlink](https://learn.microsoft.com/en-us/dotnet/api/system.windows.documents.hyperlink)
- [Page](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.page)
- [NavigationWindow](https://learn.microsoft.com/en-us/dotnet/api/system.windows.navigation.navigationwindow)
- [TabControl](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.tabcontrol)

## Dialog Boxes

Dialog boxes provide targeted support for common user-interaction scenarios such as printing.

- [OpenFileDialog](https://learn.microsoft.com/en-us/dotnet/api/microsoft.win32.openfiledialog)
- [PrintDialog](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.printdialog)
- [SaveFileDialog](https://learn.microsoft.com/en-us/dotnet/api/microsoft.win32.savefiledialog)

## User Information

User information controls provide contextual feedback or clarify an application's user interface. The user typically cannot interact with these controls.

- [AccessText](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.accesstext)
- [Label](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.label)
- [Popup](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.primitives.popup)
- [ProgressBar](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.progressbar)
- [StatusBar](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.primitives.statusbar)
- [TextBlock](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.textblock)
- [ToolTip](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.tooltip)

## Documents

WPF includes several specialized controls for viewing documents. These controls optimize the reading experience, based on the targeted user scenario.

- [DocumentViewer](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.documentviewer)
- [FlowDocumentPageViewer](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.flowdocumentpageviewer)
- [FlowDocumentReader](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.flowdocumentreader)
- [FlowDocumentScrollViewer](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.flowdocumentscrollviewer)
- [StickyNoteControl](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.stickynotecontrol)

## Input

Input controls enable the user to input text and other content.

- [TextBox](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.textbox)
- [RichTextBox](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.richtextbox)
- [PasswordBox](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.passwordbox)

## Media

WPF includes integrated support for hosting both audio and video content, as well as [codecs] for most popular image formats.

- [Image](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.image)
- [MediaElement](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.mediaelement)
- [SoundPlayerAction](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.soundplayeraction)

## Digital Ink

Digital ink controls provide integrated support for Tablet PC features, such as ink viewing and ink input.

- [InkCanvas](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.inkcanvas)
- [InkPresenter](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.inkpresenter)

## See also

- [Control Library](https://learn.microsoft.com/en-us/dotnet/desktop/wpf/controls/control-library?view=netframeworkdesktop-4.8)

