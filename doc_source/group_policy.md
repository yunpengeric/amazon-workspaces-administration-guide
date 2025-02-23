# Manage your Windows WorkSpaces<a name="group_policy"></a>

You can use Group Policy Objects \(GPOs\) to apply settings to manage Windows WorkSpaces or users that are part of your Windows WorkSpaces directory\.

**Note**  
Linux instances do not adhere to Group Policy\. For information about managing Amazon Linux WorkSpaces, see [Manage your Amazon Linux WorkSpaces](manage_linux_workspace.md)\. 

We recommend that you create an organizational unit for your WorkSpaces Computer Objects and an organizational unit for your WorkSpaces User Objects\.

To use the Group Policy settings that are specific to Amazon WorkSpaces, you must install the Group Policy administrative template for the protocol or protocols that you are using, either PCoIP or WorkSpaces Streaming Protocol \(WSP\)\.

**Warning**  
Group Policy settings can affect the experience of your WorkSpace users as follows:  
**Implementing an interactive logon message to display a logon banner prevents users from being able to access their WorkSpaces\.** The interactive logon message Group Policy setting is not currently supported by WorkSpaces\.
**Disabling removable storage through Group Policy settings causes a login failure** that results in users being logged in to temporary user profiles with no access to drive D\.
**Removing users from the Remote Desktop Users local group through Group Policy settings prevents those users from being able to authenticate** through the WorkSpaces client applications\. For more information about this Group Policy setting, see [ Allow log on through Remote Desktop Services](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-through-remote-desktop-services) in the Microsoft documentation\.
**If you remove the built\-in Users group from the **Allow log on locally** security policy, your PCoIP WorkSpaces users won't be able to connect to their WorkSpaces through the WorkSpaces client applications\.** Your PCoIP WorkSpaces also won't receive updates to the PCoIP agent software\. PCoIP agent updates might contain security and other fixes, or they might enable new features for your WorkSpaces\. For more information about working with this security policy, see [ Allow log on locally](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally) in the Microsoft documentation\.
Group Policy settings can be used to restrict drive access\. **If you configure Group Policy settings to restrict access to drive C or to drive D, users can't access their WorkSpaces\.** To prevent this issue from occurring, make sure that your users can access drive C and drive D\. 
**The WorkSpaces audio\-in feature requires local logon access inside the WorkSpace\.** The audio\-in feature is enabled by default for Windows WorkSpaces\. However, if you have a Group Policy setting that restricts users' local logon in their WorkSpaces, audio\-in won't work on your WorkSpaces\. If you remove that Group Policy setting, the audio\-in feature is enabled after the next reboot of the WorkSpace\. For more information about this Group Policy setting, see [ Allow log on locally](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally) in the Microsoft documentation\.  
For more information about enabling or disabling audio\-in redirection, see [Enable or disable audio\-in redirection for PCoIP](#gp_audio) or [Enable or disable audio\-in redirection for WSP](#gp_audio_in_wsp)\.
Using Group Policy to set the Windows power plan to **Balanced** or **Power saver** might cause your WorkSpaces to sleep when they're left idle\. We strongly recommend using Group Policy to set the Windows power plan to **High performance**\. For more information, see [My Windows WorkSpace goes to sleep when it's left idle](amazon-workspaces-troubleshooting.md#windows_workspace_sleeps_when_idle)\. 
Some Group Policy settings force users to log off when they are disconnected from a session\. Any applications that users have open on their WorkSpaces are closed\.

For information about using the Active Directory administration tools to work with GPOs, see [Set up Active Directory Administration Tools for WorkSpaces](directory_administration.md)\.

**Contents**
+ [Install the Group Policy administrative template for PCoIP](#gp_install_template)
  + [Configure printer support](#gp_local_printers)
  + [Enable or disable clipboard redirection](#gp_clipboard)
  + [Set the session resume timeout](#gp_auto_resume)
  + [Enable or disable audio\-in redirection](#gp_audio)
  + [Disable time zone redirection](#gp_time_zone)
  + [Configure PCoIP security settings](#gp_security)
  + [Enable USB redirection for YubiKey U2F](#gp_usbredirection)
+ [Install the Group Policy administrative template files for WSP](#gp_install_template_wsp)
  + [Configure printer support](#gp_local_printers_wsp)
  + [Enable or disable clipboard redirection](#gp_clipboard_wsp)
  + [Set the session resume timeout](#gp_auto_resume_wsp)
  + [Enable or disable video\-in redirection](#gp_video_in_wsp)
  + [Enable or disable audio\-in redirection](#gp_audio_in_wsp)
  + [Disable time zone redirection](#gp_time_zone_wsp)
  + [Enable or disable smart card redirection](#gp_smart_cards_in_wsp)
  + [Enable or disable disconnect session on screen lock](#gp_lock_screen_in_wsp)
+ [Set the maximum lifetime for a Kerberos ticket](#gp_kerberos_ticket)
+ [Configure device proxy server settings for internet access](#gp_device_proxy)

## Install the Group Policy administrative template for PCoIP<a name="gp_install_template"></a>

To use the Group Policy settings that are specific to Amazon WorkSpaces when using the PCoIP protocol, you must add the Group Policy administrative template that is appropriate to the version of the PCoIP agent \(either 32\-bit or 64\-bit\) that is being used for your WorkSpaces\. 

**Note**  
If you have a mix of WorkSpaces with 32\-bit and 64\-bit agents, you can use the Group Policy administrative templates for 32\-bit agents, and your Group Policy settings will be applied to both 32\-bit and 64\-bit agents\. When all of your WorkSpaces are using the 64\-bit agent, you can switch to using the administrative template for 64\-bit agents\.

**To determine whether your WorkSpaces have the 32\-bit agent or the 64\-bit agent**

1. Log in to a WorkSpace, and then open the Task Manager by choosing **View**, **Send Ctrl \+ Alt \+ Delete** or by right\-clicking the taskbar and choosing **Task Manager**\.

1. In the Task Manager, go to the **Details** tab, right\-click the column headers, and choose **Select Columns**\.

1. In the **Select Columns** dialog box, select **Platform**, and then choose **OK**\. 

1. On the **Details** tab, find `pcoip_agent.exe`, and then check its value in the **Platform** column to determine if the PCoIP agent is 32\-bit or 64\-bit\. \(You might see a mix of 32\-bit and 64\-bit WorkSpaces components; this is normal\.\)

### Install the Group Policy administrative template for PCoIP \(32\-Bit\)<a name="gp_install_template_pcoip_32_bit"></a>

To use the Group Policy settings that are specific to WorkSpaces when using the PCoIP protocol with the 32\-bit PCoIP agent, you must install the Group Policy administrative template for PCoIP\. Perform the following procedure on a directory administration WorkSpace or Amazon EC2 instance that is joined to your directory\. 

For more information about working with \.adm files, see [ Recommendations for managing Group Policy administrative template \(\.adm\) files](https://docs.microsoft.com/troubleshoot/windows-server/group-policy/manage-group-policy-adm-file) in the Microsoft documentation\.

**To install the Group Policy administrative template for PCoIP**

1. From a running Windows WorkSpace, make a copy of the `pcoip.adm` file in the `C:\Program Files (x86)\Teradici\PCoIP Agent\configuration` directory\.

1. On a directory administration WorkSpace or an Amazon EC2 instance that is joined to your WorkSpaces directory, open the Group Policy Management tool \(gpmc\.msc\) and navigate to the organizational unit in your domain that contains your WorkSpaces machine accounts\.

1. Open the context \(right\-click\) menu for the machine account organizational unit and choose **Create a GPO in this domain, and link it here**\.

1. In the **New GPO** dialog box, enter a descriptive name for the GPO, such as **WorkSpaces Machine Policies**, and leave **Source Starter GPO** set to **\(none\)**\. Choose **OK**\.

1. Open the context \(right\-click\) menu for the new GPO and choose **Edit**\.

1. In the Group Policy Management Editor, choose **Computer Configuration**, **Policies**, and **Administrative Templates**\. Choose **Action**, **Add/Remove Templates** from the main menu\. 

1. In the **Add/Remove Templates** dialog box, choose **Add**, select the `pcoip.adm` file copied previously, and then choose **Open**, **Close**\.

1. Close the Group Policy Management Editor\. You can now use this GPO to modify the Group Policy settings that are specific to WorkSpaces\.

**To verify that the administrative template file is correctly installed**

1. On a directory administration WorkSpace or an Amazon EC2 instance that is joined to your WorkSpaces directory, open the Group Policy Management tool \(gpmc\.msc\) and navigate to and select the WorkSpaces GPO for your WorkSpaces machine accounts\. Choose **Action**, **Edit** in the main menu\.

1. In the Group Policy Management Editor, choose **Computer Configuration**, **Policies**, **Administrative Templates**, **Classic Administrative Templates**, and **PCoIP Session Variables**\.

1. You can now use this **PCoIP Session Variables** Group Policy object to modify the Group Policy settings that are specific to Amazon WorkSpaces when using PCoIP\. 
**Note**  
To allow the user to override your setting, choose **Overridable Administrator Defaults**; otherwise, choose **Not Overridable Administrator Defaults**\.

### Install the Group Policy administrative template for PCoIP \(64\-Bit\)<a name="gp_install_template_pcoip_64_bit"></a>

To use the Group Policy settings that are specific to WorkSpaces when using the PCoIP protocol, you must add the Group Policy administrative template `PCoIP.admx` and `PCoIP.adml` files for PCoIP to the Central Store of the domain controller for your WorkSpaces directory\. For more information about `.admx` and `.adml` files, see [ How to create and manage the Central Store for Group Policy Administrative Templates in Windows](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)\.

The following procedure describes how to create the Central Store and add the administrative template files to it\. Perform the following procedure on a directory administration WorkSpace or Amazon EC2 instance that is joined to your WorkSpaces directory\.

**To install the Group Policy administrative template files for PCoIP**

1. From a running Windows WorkSpace, make a copy of the `PCoIP.admx` and `PCoIP.adml` files in the `C:\Program Files\Teradici\PCoIP Agent\configuration\policyDefinitions` directory\. The `PCoIP.adml` file is in the `en-US` subfolder of that directory\.

1. On a directory administration WorkSpace or an Amazon EC2 instance that is joined to your WorkSpaces directory, open Windows File Explorer, and in the address bar, enter your organization's fully qualified domain name \(FQDN\), such as `\\example.com`\.

1. Open the `sysvol` folder\.

1. Open the folder with the `FQDN` name\.

1. Open the `Policies` folder\. You should now be in `\\FQDN\sysvol\FQDN\Policies`\.

1. If it doesn't already exist, create a folder named `PolicyDefinitions`\.

1. Open the `PolicyDefinitions` folder\.

1. Copy the `PCoIP.admx` file into the `\\FQDN\sysvol\FQDN\Policies\PolicyDefinitions` folder\.

1. Create a folder named `en-US` in the `PolicyDefinitions` folder\.

1. Open the `en-US` folder\.

1. Copy the `PCoIP.adml` file into the `\\FQDN\sysvol\FQDN\Policies\PolicyDefinitions\en-US` folder\.

**To verify that the administrative template files are correctly installed**

1. On a directory administration WorkSpace or an Amazon EC2 instance that is joined to your WorkSpaces directory, open the Group Policy Management tool \(gpmc\.msc\)\.

1. Expand the forest \(**Forest:*FQDN***\)\.

1. Expand **Domains**\. 

1. Expand your FQDN \(for example, `example.com`\)\.

1. Expand **Group Policy Objects**\.

1. Select **Default Domain Policy**, open the context \(right\-click\) menu, and choose **Edit**\.
**Note**  
If the domain backing the WorkSpaces is an AWS Managed Microsoft AD directory, you cannot use the Default Domain Policy to create your GPO\. Instead, you must create and link the GPO under the domain container that has delegated privileges\.  
When you create a directory with AWS Managed Microsoft AD, AWS Directory Service creates a *yourdomainname* organizational unit \(OU\) under the domain root\. The name of this OU is based on the NetBIOS name that you typed when you created your directory\. If you didn't specify a NetBIOS name, it will default to the first part of your Directory DNS name \(for example, in the case of `corp.example.com`, the NetBIOS name is `corp`\)\.  
To create your GPO, instead of selecting **Default Domain Policy**, select the *yourdomainname* OU \(or any OU under that one\), open the context \(right\-click\) menu, and choose **Create a GPO in this domain, and Link it here**\.   
For more information about the *yourdomainname* OU, see [ What Gets Created](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_getting_started_what_gets_created.html) in the *AWS Directory Service Administration Guide*\.

1. In the Group Policy Management Editor, choose **Computer Configuration**, **Policies**, **Administrative Templates**, and **PCoIP Session Variables**\.

1. You can now use this **PCoIP Session Variables** Group Policy object to modify the Group Policy settings that are specific to WorkSpaces when using PCoIP\.
**Note**  
To allow the user to override your settings, choose **Overridable Administrator Defaults**; otherwise, choose **Not Overridable Administrator Defaults**\.

### Configure printer support for PCoIP<a name="gp_local_printers"></a>

By default, WorkSpaces enables Basic remote printing, which offers limited printing capabilities because it uses a generic printer driver on the host side to ensure compatible printing\.

Advanced remote printing for Windows clients lets you use specific features of your printer, such as double\-sided printing, but it requires installation of the matching printer driver on the host side\.

Remote printing is implemented as a virtual channel\. If virtual channels are disabled, remote printing does not function\.

For Windows WorkSpaces, you can use Group Policy settings to configure printer support as needed\.

**To configure printer support**

1. Make sure that you've installed the most recent [WorkSpaces Group Policy administrative template for PCoIP \(32\-Bit\)](#gp_install_template_pcoip_32_bit) or [WorkSpaces Group Policy administrative template for PCoIP \(64\-Bit\)](#gp_install_template_pcoip_64_bit)\.

1. On a directory administration WorkSpace or an Amazon EC2 instance that is joined to your WorkSpaces directory, open the Group Policy Management tool \(gpmc\.msc\) and navigate to **PCoIP Session Variables**\.

   To allow the user to override your setting, choose **Overridable Administrator Defaults**; otherwise, choose **Not Overridable Administrator Defaults**\.

1. Open the **Configure remote printing** setting\.

1. In the **Configure remote printing** dialog box, do one of the following:
   + To enable Advanced remote printing, choose **Enabled**, and then under **Options,** **Configure remote printing**, choose **Basic and Advanced printing for Windows clients**\. To automatically use the client computer's current default printer, select **Automatically set default printer**\.
   + To disable printing, choose **Enabled**, and then under **Options,** **Configure remote printing**, choose **Printing disabled**\.

1. Choose **OK**\.

1. The Group Policy setting change takes effect after the next Group Policy update for the WorkSpace and after the WorkSpace session is restarted\. To apply the Group Policy changes, do one of the following:
   + Reboot the WorkSpace \(in the Amazon WorkSpaces console, select the WorkSpace, then choose **Actions**, **Reboot WorkSpaces**\)\.
   + In an administrative command prompt, enter **gpupdate /force**\.

By default, local printer auto\-redirection is disabled\. You can use Group Policy settings to enable this feature so that your local printer is set as the default printer every time that you connect to your WorkSpace\.

**Note**  
Local printer redirection is not available for Amazon Linux WorkSpaces\. 

**To enable local printer auto\-redirection**

1. Make sure that you've installed the most recent [WorkSpaces Group Policy administrative template for PCoIP \(32\-Bit\)](#gp_install_template_pcoip_32_bit) or [WorkSpaces Group Policy administrative template for PCoIP \(64\-Bit\)](#gp_install_template_pcoip_64_bit)\.

1. On a directory administration WorkSpace or an Amazon EC2 instance that is joined to your WorkSpaces directory, open the Group Policy Management tool \(gpmc\.msc\) and navigate to **PCoIP Session Variables**\.

   To allow the user to override your setting, choose **Overridable Administrator Defaults**; otherwise, choose **Not Overridable Administrator Defaults**\.

1. Open the **Configure remote printing** setting\.

1. Choose **Enabled**, and then under **Options**, **Configure remote printing**, choose one of the following:
   + **Basic and Advanced printing for Windows clients**
   + **Basic printing**

1. Select **Automatically set default printer**, and then choose **OK**\.

1. The Group Policy setting change takes effect after the next Group Policy update for the WorkSpace and after the WorkSpace session is restarted\. To apply the Group Policy changes, do one of the following:
   + Reboot the WorkSpace \(in the Amazon WorkSpaces console, select the WorkSpace, then choose **Actions**, **Reboot WorkSpaces**\)\.
   + In an administrative command prompt, enter **gpupdate /force**\.

### Enable or disable clipboard redirection for PCoIP<a name="gp_clipboard"></a>

By default, WorkSpaces supports clipboard redirection\. If needed for Windows WorkSpaces, you can use Group Policy settings to disable this feature\. 

**To enable or disable clipboard redirection**

1. Make sure that you've installed the most recent [WorkSpaces Group Policy administrative template for PCoIP \(32\-Bit\)](#gp_install_template_pcoip_32_bit) or [WorkSpaces Group Policy administrative template for PCoIP \(64\-Bit\)](#gp_install_template_pcoip_64_bit)\.

1. On a directory administration WorkSpace or an Amazon EC2 instance that is joined to your WorkSpaces directory, open the Group Policy Management tool \(gpmc\.msc\) and navigate to **PCoIP Session Variables**\.

   To allow the user to override your setting, choose **Overridable Administrator Defaults**; otherwise, choose **Not Overridable Administrator Defaults**\.

1. Open the **Configure clipboard redirection** setting\.

1. In the **Configure clipboard redirection** dialog box, choose **Enabled** and then choose one of the following settings to determine the direction in which clipboard redirection is allowed\. When you're done, choose **OK**\.
   + Disabled in both directions
   + Enabled agent to client only \(WorkSpace to local computer\)
   + Enabled client to agent only \(local computer to WorkSpace\)
   + Enabled in both directions 

1. The Group Policy setting change takes effect after the next Group Policy update for the WorkSpace and after the WorkSpace session is restarted\. To apply the Group Policy changes, do one of the following:
   + Reboot the WorkSpace \(in the Amazon WorkSpaces console, select the WorkSpace, then choose **Actions**, **Reboot WorkSpaces**\)\.
   + In an administrative command prompt, enter **gpupdate /force**\.

**Known limitation**  
With clipboard redirection enabled on the WorkSpace, if you copy content that is larger than 890 KB from a Microsoft Office application, the application might become slow or unresponsive for up to 5 seconds\.

### Set the session resume timeout for PCoIP<a name="gp_auto_resume"></a>

When using the WorkSpaces client applications, an interruption of network connectivity causes an active session to be disconnected\. This can be caused by events such as closing the laptop lid, or the loss of your wireless network connection\. The WorkSpaces client applications for Windows and macOS attempt to reconnect the session automatically if network connectivity is regained within a certain amount of time\. The default session resume timeout is 20 minutes, but you can modify that value for WorkSpaces that are controlled by your domain's Group Policy settings\.

**To set the automatic session resume timeout value**

1. Make sure that you've installed the most recent [WorkSpaces Group Policy administrative template for PCoIP \(32\-Bit\)](#gp_install_template_pcoip_32_bit) or [WorkSpaces Group Policy administrative template for PCoIP \(64\-Bit\)](#gp_install_template_pcoip_64_bit)\.

1. On a directory administration WorkSpace or an Amazon EC2 instance that is joined to your WorkSpaces directory, open the Group Policy Management tool \(gpmc\.msc\) and navigate to **PCoIP Session Variables**\.

   To allow the user to override your setting, choose **Overridable Administrator Defaults**; otherwise, choose **Not Overridable Administrator Defaults**\.

1. Open the **Configure Session Automatic Reconnection Policy** setting\.

1. In the **Configure Session Automatic Reconnection Policy** dialog box, choose **Enabled**, set the **Configure Session Automatic Reconnection Policy** option to the desired timeout, in minutes, and choose **OK**\. 

1. The Group Policy setting change takes effect after the next Group Policy update for the WorkSpace and after the WorkSpace session is restarted\. To apply the Group Policy changes, do one of the following:
   + Reboot the WorkSpace \(in the Amazon WorkSpaces console, select the WorkSpace, then choose **Actions**, **Reboot WorkSpaces**\)\.
   + In an administrative command prompt, enter **gpupdate /force**\.

### Enable or disable audio\-in redirection for PCoIP<a name="gp_audio"></a>

By default, Amazon WorkSpaces supports redirecting data from a local microphone\. If needed for Windows WorkSpaces, you can use Group Policy settings to disable this feature\.

**Note**  
If you have a Group Policy setting that restricts users' local logon in their WorkSpaces, audio\-in won't work on your WorkSpaces\. If you remove that Group Policy setting, the audio\-in feature is enabled after the next reboot of the WorkSpace\. For more information about this Group Policy setting, see [ Allow logon locally](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally) in the Microsoft documentation\.

**To enable or disable audio\-in redirection**

1. Make sure that you've installed the most recent [WorkSpaces Group Policy administrative template for PCoIP \(32\-Bit\)](#gp_install_template_pcoip_32_bit) or [WorkSpaces Group Policy administrative template for PCoIP \(64\-Bit\)](#gp_install_template_pcoip_64_bit)\.

1. On a directory administration WorkSpace or an Amazon EC2 instance that is joined to your WorkSpaces directory, open the Group Policy Management tool \(gpmc\.msc\) and navigate to **PCoIP Session Variables**\.

   To allow the user to override your setting, choose **Overridable Administrator Defaults**; otherwise, choose **Not Overridable Administrator Defaults**\.

1. Open the **Enable/disable audio in the PCoIP session** setting\.

1. In the **Enable/disable audio in the PCoIP session** dialog box, choose **Enabled** or **Disabled**\.

1. Choose **OK**\.

1. The Group Policy setting change takes effect after the next Group Policy update for the WorkSpace and after the WorkSpace session is restarted\. To apply the Group Policy changes, do one of the following:
   + Reboot the WorkSpace \(in the Amazon WorkSpaces console, select the WorkSpace, then choose **Actions**, **Reboot WorkSpaces**\)\.
   + In an administrative command prompt, enter **gpupdate /force**\.

### Disable time zone redirection for PCoIP<a name="gp_time_zone"></a>

By default, the time within a Workspace is set to mirror the time zone of the client that is being used to connect to the WorkSpace\. This behavior is controlled through time zone redirection\. You might want to turn off time zone direction for various reasons: 
+ Your company wants all employees to work in a certain time zone \(even if some employees are in other time zones\)\.
+ You have scheduled tasks in a WorkSpace that are meant to run at a certain time in a specific time zone\.
+ Your users who travel a lot want to keep their WorkSpaces in one time zone for consistency and personal preference\.

If needed for Windows WorkSpaces, you can use Group Policy settings to disable this feature\.

**To disable time zone redirection**

1. Make sure that you've installed the most recent [WorkSpaces Group Policy administrative template for PCoIP \(32\-Bit\)](#gp_install_template_pcoip_32_bit) or [WorkSpaces Group Policy administrative template for PCoIP \(64\-Bit\)](#gp_install_template_pcoip_64_bit)\.

1. On a directory administration WorkSpace or an Amazon EC2 instance that is joined to your WorkSpaces directory, open the Group Policy Management tool \(gpmc\.msc\) and navigate to **PCoIP Session Variables**\.

   To allow the user to override your setting, choose **Overridable Administrator Defaults**; otherwise, choose **Not Overridable Administrator Defaults**\.

1. Open the **Configure timezone redirection** setting\.

1. In the **Configure timezone redirection** dialog box, choose **Disabled**\.

1. Choose **OK**\.

1. The Group Policy setting change takes effect after the next Group Policy update for the WorkSpace and after the WorkSpace session is restarted\. To apply the Group Policy changes, do one of the following:
   + Reboot the WorkSpace \(in the Amazon WorkSpaces console, select the WorkSpace, then choose **Actions**, **Reboot WorkSpaces**\)\.
   + In an administrative command prompt, enter **gpupdate /force**\.

1. Set the time zone for the WorkSpaces to the desired time zone\.

The time zone of the WorkSpaces is now static and no longer mirrors the time zone of the client machines\. 

### Configure PCoIP security settings<a name="gp_security"></a>

For PCoIP, data in transit is encrypted using TLS 1\.2 encryption and SigV4 request signing\. The PCoIP protocol uses encrypted UDP traffic, with AES encryption, for streaming pixels\. The streaming connection, using port 4172 \(TCP and UDP\), is encrypted by using AES\-128 and AES\-256 ciphers, but the encryption defaults to 128\-bit\. You can change this default to 256\-bit by using the **Configure PCoIP Security Settings** Group Policy setting\.

You can also use this Group Policy setting to modify the TLS Security Mode and to block certain cipher suites\. A detailed explanation of these settings and the supported cipher suites is provided in the **Configure PCoIP Security Settings** Group Policy dialog box\. 

**To configure PCoIP security settings**

1. Make sure that you've installed the most recent [WorkSpaces Group Policy administrative template for PCoIP \(32\-Bit\)](#gp_install_template_pcoip_32_bit) or [WorkSpaces Group Policy administrative template for PCoIP \(64\-Bit\)](#gp_install_template_pcoip_64_bit)\.

1. On a directory administration WorkSpace or an Amazon EC2 instance that is joined to your WorkSpaces directory, open the Group Policy Management tool \(gpmc\.msc\) and navigate to **PCoIP Session Variables**\.

   To allow the user to override your setting, choose **Overridable Administrator Defaults**; otherwise, choose **Not Overridable Administrator Defaults**\.

1. Open the **Configure PCoIP Security Settings** setting\.

1. In the **Configure PCoIP Security Settings** dialog box, choose **Enabled**\. To set the default encryption for streaming traffic to 256\-bit, go to the **PCoIP Data Encryption Ciphers** option, and select **AES\-256\-GCM only**\.

1. \(Optional\) Adjust the **TLS Security Mode** setting, and then list any cipher suites that you want to block\. For more information about these settings, see the descriptions provided in the **Configure PCoIP Security Settings** dialog box\.

1. Choose **OK**\.

1. The Group Policy setting change takes effect after the next Group Policy update for the WorkSpace and after the WorkSpace session is restarted\. To apply the Group Policy changes, do one of the following:
   + Reboot the WorkSpace \(in the Amazon WorkSpaces console, select the WorkSpace, then choose **Actions**, **Reboot WorkSpaces**\)\.
   + In an administrative command prompt, enter **gpupdate /force**\.

### Enable USB redirection for YubiKey U2F<a name="gp_usbredirection"></a>

**Note**  
Amazon WorkSpaces currently supports USB redirection only for YubiKey U2F\. Other types of USB devices might be redirected but they are not supported and might not work properly\. 

**To enable USB redirection for YubiKey U2F**

1. Make sure that you've installed the most recent [WorkSpaces Group Policy administrative template for PCoIP \(32\-Bit\)](#gp_install_template_pcoip_32_bit) or [WorkSpaces Group Policy administrative template for PCoIP \(64\-Bit\)](#gp_install_template_pcoip_64_bit)\.

1. On a directory administration WorkSpace or an Amazon EC2 instance that is joined to your WorkSpaces directory, open the Group Policy Management tool \(gpmc\.msc\) and navigate to **PCoIP Session Variables**\.

1. To allow the user to override your setting, choose **Overridable Administrator Defaults**\. Otherwise, choose **Not Overridable Administrator Defaults**\.

1. Open the **Enable/disable USB in the PCOIP session** setting\.

1.  Choose **Enabled**, and then choose **OK**\. 

1. Open the **Configure PCoIP USB allowed and unallowed device rules** setting\.

1. Choose **Enabled**, and under **Enter the USB authorization table \(maximum ten rules\)**, configure your USB device allowlist rules\.

   1. Authorization rule \- 110500407\. This value is a combination of a Vendor ID \(VID\) and a Product ID \(PID\)\. The format for a VID/PID combination is 1xxxxyyyy, where xxxx is the VID in hexadecimal format and yyyy is the PID in hexadecimal format\. For this example, 1050 is the VID, and 0407 is the PID\. For more YubiKey USB values, see [YubiKey USB ID Values](https://support.yubico.com/hc/en-us/articles/360016614920-YubiKey-USB-ID-Values)\.

1. Under **Enter the USB authorization table \(maximum ten rules\)**, configure your USB device blocklist rules\. 

   1. For **Unauthorization Rule**, set an empty string\. This means that only USB devices in the authorization list are allowed\.
**Note**  
You can define a maximum of 10 USB authorization rules and a maximum of 10 USB unauthorization rules\. Use the vertical bar \(\|\) character to separate multiple rules\. For detailed information about the authorization/unauthorization rules, see [Teradici PCoIP Standard Agent for Windows](https://www.teradici.com/web-help/pcoip_agent/standard_agent/windows/20.10/admin-guide/configuring/configuring/#pcoip-usb-allowed-and-unallowed-device-rules)\. 

1. Choose **OK**\.

1. The Group Policy setting change takes effect after the next Group Policy update for the WorkSpace and after the WorkSpace session is restarted\. To apply the Group Policy changes, do one of the following:
   + Reboot the WorkSpace \(in the Amazon WorkSpaces console, select the WorkSpace, then choose **Actions**, **Reboot WorkSpaces**\)\.
   + In an administrative command prompt, enter **gpupdate /force**\.

After the setting takes effect, all supported USB devices will be able to be redirected to WorkSpaces unless restrictions are configured through the USB device rules setting\.

## Install the Group Policy administrative template files for the WorkSpaces Streaming Protocol \(WSP\)<a name="gp_install_template_wsp"></a>

To use the Group Policy settings that are specific to WorkSpaces when using the WorkSpaces Streaming Protocol \(WSP\), you must add the Group Policy administrative template `wsp.admx` and `wsp.adml` files for WSP to the Central Store of the domain controller for your WorkSpaces directory\. For more information about `.admx` and `.adml` files, see [ How to create and manage the Central Store for Group Policy Administrative Templates in Windows](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)\.

The following procedure describes how to create the Central Store and add the administrative template files to it\. Perform the following procedure on a directory administration WorkSpace or Amazon EC2 instance that is joined to your WorkSpaces directory\.

**To install the Group Policy administrative template files for WSP**

1. From a running Windows WorkSpace, make a copy of the `wsp.admx` and `wsp.adml` files in the `C:\Program Files\Amazon\WSP` directory\.

1. On a directory administration WorkSpace or an Amazon EC2 instance that is joined to your WorkSpaces directory, open Windows File Explorer, and in the address bar, enter your organization's fully qualified domain name \(FQDN\), such as `\\example.com`\.

1. Open the `sysvol` folder\.

1. Open the folder with the `FQDN` name\.

1. Open the `Policies` folder\. You should now be in `\\FQDN\sysvol\FQDN\Policies`\.

1. If it doesn't already exist, create a folder named `PolicyDefinitions`\.

1. Open the `PolicyDefinitions` folder\.

1. Copy the `wsp.admx` file into the `\\FQDN\sysvol\FQDN\Policies\PolicyDefinitions` folder\.

1. Create a folder named `en-US` in the `PolicyDefinitions` folder\.

1. Open the `en-US` folder\.

1. Copy the `wsp.adml` file into the `\\FQDN\sysvol\FQDN\Policies\PolicyDefinitions\en-US` folder\.

**To verify that the administrative template files are correctly installed**

1. On a directory administration WorkSpace or an Amazon EC2 instance that is joined to your WorkSpaces directory, open the Group Policy Management tool \(gpmc\.msc\)\.

1. Expand the forest \(**Forest:*FQDN***\)\.

1. Expand **Domains**\. 

1. Expand your FQDN \(for example, `example.com`\)\.

1. Expand **Group Policy Objects**\.

1. Select **Default Domain Policy**, open the context \(right\-click\) menu, and choose **Edit**\.
**Note**  
If the domain backing the WorkSpaces is an AWS Managed Microsoft AD directory, you cannot use the Default Domain Policy to create your GPO\. Instead, you must create and link the GPO under the domain container that has delegated privileges\.  
When you create a directory with AWS Managed Microsoft AD, AWS Directory Service creates a *yourdomainname* organizational unit \(OU\) under the domain root\. The name of this OU is based on the NetBIOS name that you typed when you created your directory\. If you didn't specify a NetBIOS name, it will default to the first part of your Directory DNS name \(for example, in the case of `corp.example.com`, the NetBIOS name is `corp`\)\.  
To create your GPO, instead of selecting **Default Domain Policy**, select the *yourdomainname* OU \(or any OU under that one\), open the context \(right\-click\) menu, and choose **Create a GPO in this domain, and Link it here**\.   
For more information about the *yourdomainname* OU, see [ What Gets Created](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_getting_started_what_gets_created.html) in the *AWS Directory Service Administration Guide*\.

1. In the Group Policy Management Editor, choose **Computer Configuration**, **Policies**, **Administrative Templates**, **Amazon**, and **WSP**\.

1. You can now use this **WSP** Group Policy object to modify the Group Policy settings that are specific to WorkSpaces when using WSP\.

### Configure printer support for WSP<a name="gp_local_printers_wsp"></a>

By default, WorkSpaces enables Basic remote printing, which offers limited printing capabilities because it uses a generic printer driver on the host side to ensure compatible printing\.

Advanced remote printing for Windows clients \(not available for WSP\) lets you use specific features of your printer, such as double\-sided printing, but it requires installation of the matching printer driver on the host side\.

Remote printing is implemented as a virtual channel\. If virtual channels are disabled, remote printing does not function\.

For Windows WorkSpaces, you can use Group Policy settings to configure printer support as needed\.

**To configure printer support**

1. Make sure that the most recent [WorkSpaces Group Policy administrative template for WSP](#gp_install_template_wsp) is installed in the Central Store of the domain controller for your WorkSpaces directory\.

1. On a directory administration WorkSpace or an Amazon EC2 instance that is joined to your WorkSpaces directory, open the Group Policy Management tool \(gpmc\.msc\)\.

1. Expand the forest \(**Forest:*FQDN***\)\.

1. Expand **Domains**\. 

1. Expand your FQDN \(for example, `example.com`\)\.

1. Expand **Group Policy Objects**\.

1. Select **Default Domain Policy**, open the context \(right\-click\) menu, and choose **Edit**\.
**Note**  
If the domain backing the WorkSpaces is an AWS Managed Microsoft AD directory, you cannot use the Default Domain Policy to create your GPO\. Instead, select the *yourdomainname* OU \(or any OU under that one\), open the context \(right\-click\) menu, and choose **Create a GPO in this domain, and Link it here**\. For more information about the *yourdomainname* OU, see [ What Gets Created](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_getting_started_what_gets_created.html) in the *AWS Directory Service Administration Guide*\.

1. In the Group Policy Management Editor, choose **Computer Configuration**, **Policies**, **Administrative Templates**, **Amazon**, and **WSP**\.

1. Open the **Configure remote printing** setting\.

1. In the **Configure remote printing** dialog box, do one of the following:
   + To enable local printer redirection, choose **Enabled**, and then for **Printing options**, choose **Basic**\. To automatically use the client computer's current default printer, select **Map local default printer to the remote host**\.
   + To disable printing, choose **Disabled**\.

1. Choose **OK**\.

1. The Group Policy setting change takes effect after the next Group Policy update for the WorkSpace and after the WorkSpace session is restarted\. To apply the Group Policy changes, do one of the following:
   + Reboot the WorkSpace \(in the Amazon WorkSpaces console, select the WorkSpace, then choose **Actions**, **Reboot WorkSpaces**\)\.
   + In an administrative command prompt, enter **gpupdate /force**\.

### Enable or disable clipboard redirection for WSP<a name="gp_clipboard_wsp"></a>

By default, WorkSpaces supports two\-way \(copy/paste\) clipboard redirection\. If needed for Windows WorkSpaces, you can use Group Policy settings to disable this feature\. 

**To enable or disable clipboard redirection for Windows WorkSpaces**

1. Make sure that the most recent [WorkSpaces Group Policy administrative template for WSP](#gp_install_template_wsp) is installed in the Central Store of the domain controller for your WorkSpaces directory\.

1. On a directory administration WorkSpace or an Amazon EC2 instance that is joined to your WorkSpaces directory, open the Group Policy Management tool \(gpmc\.msc\)\.

1. Expand the forest \(**Forest:*FQDN***\)\.

1. Expand **Domains**\. 

1. Expand your FQDN \(for example, `example.com`\)\.

1. Expand **Group Policy Objects**\.

1. Select **Default Domain Policy**, open the context \(right\-click\) menu, and choose **Edit**\.
**Note**  
If the domain backing the WorkSpaces is an AWS Managed Microsoft AD directory, you cannot use the Default Domain Policy to create your GPO\. Instead, select the *yourdomainname* OU \(or any OU under that one\), open the context \(right\-click\) menu, and choose **Create a GPO in this domain, and Link it here**\. For more information about the *yourdomainname* OU, see [ What Gets Created](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_getting_started_what_gets_created.html) in the *AWS Directory Service Administration Guide*\.

1. In the Group Policy Management Editor, choose **Computer Configuration**, **Policies**, **Administrative Templates**, **Amazon**, and **WSP**\.

1. Open the **Enable/disable clipboard redirection** setting\.

1. In the **Enable/disable clipboard redirection** dialog box, choose **Enabled** or **Disabled**\.

1. Choose **OK**\.

1. The Group Policy setting change takes effect after the next Group Policy update for the WorkSpace and after the WorkSpace session is restarted\. To apply the Group Policy changes, do one of the following:
   + Reboot the WorkSpace \(in the Amazon WorkSpaces console, select the WorkSpace, then choose **Actions**, **Reboot WorkSpaces**\)\.
   + In an administrative command prompt, enter **gpupdate /force**\.

**Known limitation**  
With clipboard redirection enabled on the WorkSpace, if you copy content that is larger than 890 KB from a Microsoft Office application, the application might become slow or unresponsive for up to 5 seconds\.

### Set the session resume timeout for WSP<a name="gp_auto_resume_wsp"></a>

When using the WorkSpaces client applications, an interruption of network connectivity causes an active session to be disconnected\. This can be caused by events such as closing the laptop lid, or the loss of your wireless network connection\. The WorkSpaces client applications for Windows and macOS attempt to reconnect the session automatically if network connectivity is regained within a certain amount of time\. The default session resume timeout is 20 minutes \(1200 seconds\), but you can modify that value for WorkSpaces that are controlled by your domain's Group Policy settings\. 

**To set the automatic session resume timeout value**

1. Make sure that the most recent [WorkSpaces Group Policy administrative template for WSP](#gp_install_template_wsp) is installed in the Central Store of the domain controller for your WorkSpaces directory\.

1. On a directory administration WorkSpace or an Amazon EC2 instance that is joined to your WorkSpaces directory, open the Group Policy Management tool \(gpmc\.msc\)\.

1. Expand the forest \(**Forest:*FQDN***\)\.

1. Expand **Domains**\. 

1. Expand your FQDN \(for example, `example.com`\)\.

1. Expand **Group Policy Objects**\.

1. Select **Default Domain Policy**, open the context \(right\-click\) menu, and choose **Edit**\.
**Note**  
If the domain backing the WorkSpaces is an AWS Managed Microsoft AD directory, you cannot use the Default Domain Policy to create your GPO\. Instead, select the *yourdomainname* OU \(or any OU under that one\), open the context \(right\-click\) menu, and choose **Create a GPO in this domain, and Link it here**\. For more information about the *yourdomainname* OU, see [ What Gets Created](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_getting_started_what_gets_created.html) in the *AWS Directory Service Administration Guide*\.

1. In the Group Policy Management Editor, choose **Computer Configuration**, **Policies**, **Administrative Templates**, **Amazon**, and **WSP**\.

1. Open the **Enable/disable automatic reconnect** setting\.

1. In the **Enable/disable automatic reconnect** dialog box, choose **Enabled**, and then set **Reconnect timeout \(seconds\)** to the desired timeout in seconds\.

1. Choose **OK**\.

1. The Group Policy setting change takes effect after the next Group Policy update for the WorkSpace and after the WorkSpace session is restarted\. To apply the Group Policy changes, do one of the following:
   + Reboot the WorkSpace \(in the Amazon WorkSpaces console, select the WorkSpace, then choose **Actions**, **Reboot WorkSpaces**\)\.
   + In an administrative command prompt, enter **gpupdate /force**\.

### Enable or disable video\-in redirection for WSP<a name="gp_video_in_wsp"></a>

By default, WorkSpaces supports redirecting data from a local camera\. If needed for Windows WorkSpaces, you can use Group Policy settings to disable this feature\. 

**To enable or disable video\-in redirection for Windows WorkSpaces**

1. Make sure that the most recent [WorkSpaces Group Policy administrative template for WSP](#gp_install_template_wsp) is installed in the Central Store of the domain controller for your WorkSpaces directory\.

1. On a directory administration WorkSpace or an Amazon EC2 instance that is joined to your WorkSpaces directory, open the Group Policy Management tool \(gpmc\.msc\)\.

1. Expand the forest \(**Forest:*FQDN***\)\.

1. Expand **Domains**\. 

1. Expand your FQDN \(for example, `example.com`\)\.

1. Expand **Group Policy Objects**\.

1. Select **Default Domain Policy**, open the context \(right\-click\) menu, and choose **Edit**\.
**Note**  
If the domain backing the WorkSpaces is an AWS Managed Microsoft AD directory, you cannot use the Default Domain Policy to create your GPO\. Instead, select the *yourdomainname* OU \(or any OU under that one\), open the context \(right\-click\) menu, and choose **Create a GPO in this domain, and Link it here**\. For more information about the *yourdomainname* OU, see [ What Gets Created](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_getting_started_what_gets_created.html) in the *AWS Directory Service Administration Guide*\.

1. In the Group Policy Management Editor, choose **Computer Configuration**, **Policies**, **Administrative Templates**, **Amazon**, and **WSP**\.

1. Open the **Enable/disable video\-in redirection** setting\.

1. In the **Enable/disable video\-in redirection** dialog box, choose **Enabled** or **Disabled**\.

1. Choose **OK**\.

1. The Group Policy setting change takes effect after the next Group Policy update for the WorkSpace and after the WorkSpace session is restarted\. To apply the Group Policy changes, do one of the following:
   + Reboot the WorkSpace \(in the Amazon WorkSpaces console, select the WorkSpace, then choose **Actions**, **Reboot WorkSpaces**\)\.
   + In an administrative command prompt, enter **gpupdate /force**\.

### Enable or disable audio\-in redirection for WSP<a name="gp_audio_in_wsp"></a>

By default, WorkSpaces supports redirecting data from a local microphone\. If needed for Windows WorkSpaces, you can use Group Policy settings to disable this feature\. 

**To enable or disable audio\-in redirection for Windows WorkSpaces**

1. Make sure that the most recent [WorkSpaces Group Policy administrative template for WSP](#gp_install_template_wsp) is installed in the Central Store of the domain controller for your WorkSpaces directory\.

1. On a directory administration WorkSpace or an Amazon EC2 instance that is joined to your WorkSpaces directory, open the Group Policy Management tool \(gpmc\.msc\)\.

1. Expand the forest \(**Forest:*FQDN***\)\.

1. Expand **Domains**\. 

1. Expand your FQDN \(for example, `example.com`\)\.

1. Expand **Group Policy Objects**\.

1. Select **Default Domain Policy**, open the context \(right\-click\) menu, and choose **Edit**\.
**Note**  
If the domain backing the WorkSpaces is an AWS Managed Microsoft AD directory, you cannot use the Default Domain Policy to create your GPO\. Instead, select the *yourdomainname* OU \(or any OU under that one\), open the context \(right\-click\) menu, and choose **Create a GPO in this domain, and Link it here**\. For more information about the *yourdomainname* OU, see [ What Gets Created](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_getting_started_what_gets_created.html) in the *AWS Directory Service Administration Guide*\.

1. In the Group Policy Management Editor, choose **Computer Configuration**, **Policies**, **Administrative Templates**, **Amazon**, and **WSP**\.

1. Open the **Enable/disable audio\-in redirection** setting\.

1. In the **Enable/disable audio\-in redirection** dialog box, choose **Enabled** or **Disabled**\.

1. Choose **OK**\.

1. The Group Policy setting change takes effect after the next Group Policy update for the WorkSpace and after the WorkSpace session is restarted\. To apply the Group Policy changes, do one of the following:
   + Reboot the WorkSpace \(in the Amazon WorkSpaces console, select the WorkSpace, then choose **Actions**, **Reboot WorkSpaces**\)\.
   + In an administrative command prompt, enter **gpupdate /force**\.

### Disable time zone redirection for WSP<a name="gp_time_zone_wsp"></a>

By default, the time within a Workspace is set to mirror the time zone of the client that is being used to connect to the WorkSpace\. This behavior is controlled through time zone redirection\. You might want to turn off time zone direction for various reasons: 
+ Your company wants all employees to work in a certain time zone \(even if some employees are in other time zones\)\.
+ You have scheduled tasks in a WorkSpace that are meant to run at a certain time in a specific time zone\.
+ Your users who travel a lot want to keep their WorkSpaces in one time zone for consistency and personal preference\.

If needed for Windows WorkSpaces, you can use Group Policy settings to disable this feature\.

**To disable time zone redirection for Windows WorkSpaces**

1. Make sure that the most recent [WorkSpaces Group Policy administrative template for WSP](#gp_install_template_wsp) is installed in the Central Store of the domain controller for your WorkSpaces directory\.

1. On a directory administration WorkSpace or an Amazon EC2 instance that is joined to your WorkSpaces directory, open the Group Policy Management tool \(gpmc\.msc\)\.

1. Expand the forest \(**Forest:*FQDN***\)\.

1. Expand **Domains**\. 

1. Expand your FQDN \(for example, `example.com`\)\.

1. Expand **Group Policy Objects**\.

1. Select **Default Domain Policy**, open the context \(right\-click\) menu, and choose **Edit**\.
**Note**  
If the domain backing the WorkSpaces is an AWS Managed Microsoft AD directory, you cannot use the Default Domain Policy to create your GPO\. Instead, select the *yourdomainname* OU \(or any OU under that one\), open the context \(right\-click\) menu, and choose **Create a GPO in this domain, and Link it here**\. For more information about the *yourdomainname* OU, see [ What Gets Created](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_getting_started_what_gets_created.html) in the *AWS Directory Service Administration Guide*\.

1. In the Group Policy Management Editor, choose **Computer Configuration**, **Policies**, **Administrative Templates**, **Amazon**, and **WSP**\.

1. Open the **Enable/disable time zone redirection** setting\.

1. In the **Enable/disable time zone redirection** dialog box, choose **Disabled**\.

1. Choose **OK**\.

1. The Group Policy setting change takes effect after the next Group Policy update for the WorkSpace and after the WorkSpace session is restarted\. To apply the Group Policy changes, do one of the following:
   + Reboot the WorkSpace \(in the Amazon WorkSpaces console, select the WorkSpace, then choose **Actions**, **Reboot WorkSpaces**\)\.
   + In an administrative command prompt, enter **gpupdate /force**\.

1. Set the time zone for the WorkSpaces to the desired time zone\.

The time zone of the WorkSpaces is now static and no longer mirrors the time zone of the client machines\. 

### Enable or disable smart card redirection for WSP<a name="gp_smart_cards_in_wsp"></a>

By default, Amazon WorkSpaces are not enabled to support the use of smart cards for either *pre\-session authentication* or *in\-session authentication*\. Pre\-session authentication refers to smart card authentication that's performed while users are logging in to their WorkSpaces\. In\-session authentication refers to authentication that's performed after logging in\.

If needed, you can enable pre\-session and in\-session authentication for Windows WorkSpaces by using Group Policy settings\. Pre\-session authentication must also be enabled through your AD Connector directory settings by using the EnableClientAuthentication API action or the enable\-client\-authentication AWS CLI command\. For more information, see [ Enable Smart Card Authentication for AD Connector](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ad_connector_clientauth.html) in the *AWS Directory Service Administration Guide*\.

**Note**  
To enable the use of smart cards with Windows WorkSpaces, additional steps are required\. For more information, see [Use smart cards for authentication](smart-cards.md)\. 

**To enable or disable smart card redirection for Windows WorkSpaces**

1. Make sure that the most recent [WorkSpaces Group Policy administrative template for WSP](#gp_install_template_wsp) is installed in the Central Store of the domain controller for your WorkSpaces directory\.

1. On a directory administration WorkSpace or an Amazon EC2 instance that is joined to your WorkSpaces directory, open the Group Policy Management tool \(gpmc\.msc\)\.

1. Expand the forest \(**Forest:*FQDN***\)\.

1. Expand **Domains**\. 

1. Expand your FQDN \(for example, `example.com`\)\.

1. Expand **Group Policy Objects**\.

1. Select **Default Domain Policy**, open the context \(right\-click\) menu, and choose **Edit**\.
**Note**  
If the domain backing the WorkSpaces is an AWS Managed Microsoft AD directory, you cannot use the Default Domain Policy to create your GPO\. Instead, select the *yourdomainname* OU \(or any OU under that one\), open the context \(right\-click\) menu, and choose **Create a GPO in this domain, and Link it here**\. For more information about the *yourdomainname* OU, see [ What Gets Created](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_getting_started_what_gets_created.html) in the *AWS Directory Service Administration Guide*\.

1. In the Group Policy Management Editor, choose **Computer Configuration**, **Policies**, **Administrative Templates**, **Amazon**, and **WSP**\.

1. Open the **Enable/disable smart card redirection** setting\.

1. In the **Enable/disable smart card redirection** dialog box, choose **Enabled** or **Disabled**\.

1. Choose **OK**\.

1. The Group Policy setting change takes effect after the WorkSpace session is restarted\. To apply the Group Policy change, reboot the WorkSpace \(in the Amazon WorkSpaces console, select the WorkSpace, then choose **Actions**, **Reboot WorkSpaces**\)\.

### Enable or disable disconnect session on screen lock for WSP<a name="gp_lock_screen_in_wsp"></a>

If needed, you can disconnect users' WorkSpaces sessions when the Windows lock screen is detected\. To reconnect from the WorkSpaces client, users can use their passwords or their smart cards to authenticate themselves, depending on which type of authentication has been enabled for their WorkSpaces\.

This Group Policy setting is disabled by default\. If needed, you can enable disconnecting the session when the Windows lock screen is detected for Windows WorkSpaces by using Group Policy settings\.

**Note**  
This Group Policy setting is available only in the AWS GovCloud \(US\-West\) Region at this time\.
This Group Policy setting applies to both password\-authenticated and smart card\-authenticated sessions\.
To enable the use of smart cards with Windows WorkSpaces, additional steps are required\. For more information, see [Use smart cards for authentication](smart-cards.md)\. 

**To enable or disable disconnect session on screen lock for Windows WorkSpaces**

1. Make sure that the most recent [WorkSpaces Group Policy administrative template for WSP](#gp_install_template_wsp) is installed in the Central Store of the domain controller for your WorkSpaces directory\.

1. On a directory administration WorkSpace or an Amazon EC2 instance that is joined to your WorkSpaces directory, open the Group Policy Management tool \(gpmc\.msc\)\.

1. Expand the forest \(**Forest:*FQDN***\)\.

1. Expand **Domains**\. 

1. Expand your FQDN \(for example, `example.com`\)\.

1. Expand **Group Policy Objects**\.

1. Select **Default Domain Policy**, open the context \(right\-click\) menu, and choose **Edit**\.
**Note**  
If the domain backing the WorkSpaces is an AWS Managed Microsoft AD directory, you cannot use the Default Domain Policy to create your GPO\. Instead, select the *yourdomainname* OU \(or any OU under that one\), open the context \(right\-click\) menu, and choose **Create a GPO in this domain, and Link it here**\. For more information about the *yourdomainname* OU, see [ What Gets Created](https://docs.aws.amazon.com/directoryservice/latest/admin-guide/ms_ad_getting_started_what_gets_created.html) in the *AWS Directory Service Administration Guide*\.

1. In the Group Policy Management Editor, choose **Computer Configuration**, **Policies**, **Administrative Templates**, **Amazon**, and **WSP**\.

1. Open the **Enable/disable disconnect session on screen lock** setting\.

1. In the **Enable/disable disconnect session on screen lock** dialog box, choose **Enabled** or **Disabled**\.

1. Choose **OK**\.

1. The Group Policy setting change takes effect after the next Group Policy update for the WorkSpace and after the WorkSpace session is restarted\. To apply the Group Policy changes, do one of the following:
   + Reboot the WorkSpace \(in the Amazon WorkSpaces console, select the WorkSpace, then choose **Actions**, **Reboot WorkSpaces**\)\.
   + In an administrative command prompt, enter **gpupdate /force**\.

## Set the maximum lifetime for a Kerberos ticket<a name="gp_kerberos_ticket"></a>

If you have not disabled the **Remember Me** feature of your Windows WorkSpaces, your WorkSpace users can use the **Remember Me** or **Keep me logged in** check box in their WorkSpaces client application to save their credentials\. This feature allows users to easily connect to their WorkSpaces while the client application remains running\. Their credentials are securely cached up to the maximum lifetime of their Kerberos tickets\.

If your WorkSpace uses an AD Connector directory, you can modify the maximum lifetime of the Kerberos tickets for your WorkSpaces users through Group Policy by following the steps in [ Maximum Lifetime for a User Ticket](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/maximum-lifetime-for-user-ticket) in the Microsoft Windows documentation\.

To enable or disable the **Remember Me** feature, see [Enable self\-service WorkSpace management capabilities for your users](enable-user-self-service-workspace-management.md)\.

## Configure device proxy server settings for internet access<a name="gp_device_proxy"></a>

By default, the WorkSpaces Windows client application uses the proxy server that's specified in the device operating system settings for HTTPS \(port 443\) traffic\. The Amazon WorkSpaces client applications use the HTTPS port for updates, registration, and authentication\. 

**Note**  
The desktop streaming connections to the WorkSpace require ports 4172 and 4195 to be enabled, and do not go through the proxy server\.
Proxy servers that require authentication with a username and password are not supported\.

You can configure the device proxy server settings for your Windows WorkSpaces through Group Policy by following the steps in [ Configure device proxy and internet connectivity settings](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet) in the Microsoft documentation\.

For more information about configuring the proxy settings in the WorkSpaces Windows client application, see [ Proxy Server](https://docs.aws.amazon.com/workspaces/latest/userguide/amazon-workspaces-windows-client.html#windows_proxy_server) in the *Amazon WorkSpaces User Guide*\.