# Required configuration and service components for WorkSpaces<a name="required-service-components"></a>

As a WorkSpace administrator, you must understand the following about required configuration and service components\. 

## Required routing table configuration<a name="routing-table-configuration"></a>

We recommend that you not modify the operating system\-level routing table for a WorkSpace\. The WorkSpaces service requires the preconfigured routes in this table to monitor the system state and update system components\. If routing table changes are required for your organization, contact AWS Support or your AWS account team before applying any changes\. 

## Required service components<a name="required-service-components"></a>

On Windows WorkSpaces, the service components are installed in the following locations\. Do not delete, change, block, or quarantine these objects\. If you do so, the WorkSpace will not function correctly\.

If antivirus software is installed on the WorkSpace, make sure it does not interfere with the service components installed in the following locations\.

**Important**  
Starting March 29, 2021, we're updating the PCoIP agent from 32\-bit to 64\-bit\. For Windows WorkSpaces that are using the PCoIP protocol, this means that the location of the Teradici files changes from `C:\Program Files (x86)\Teradici` to `C:\Program Files\Teradici`\. These PCoIP agent updates will occur in waves during our regular maintenance windows, which means that some of your WorkSpaces might be using the 32\-bit agent and some might be using the 64\-bit agent during this transition\.  
If you've configured firewall rules, antivirus software exclusions \(on the client side and host side\), Group Policy Object \(GPO\) settings, or settings for Microsoft System Center Configuration Manager \(SCCM\), Microsoft Endpoint Configuration Manager, or similar configuration management tools based on the full path to the 32\-bit agent, you must also add the full path to the 64\-bit agent to those settings\.  
**If you're filtering on the paths to any 32\-bit PCoIP components, be sure to add the paths to the 64\-bit versions of the components\. Because your WorkSpaces might not all be updated at the same time, do not replace the 32\-bit path with the 64\-bit path, or some of your WorkSpaces might not work\.** For example, if you're basing your exclusions or communication filters on `C:\Program Files (x86)\Teradici\PCoIP Agent\bin\pcoip_server_win32.exe`, you must also add `C:\Program Files\Teradici\PCoIP Agent\bin\pcoip_server.exe`\. Likewise, if you're basing your exclusions or communications filters on `C:\Program Files (x86)\Teradici\PCoIP Agent\bin\pcoip_agent.exe`, you must also add `C:\Program Files\Teradici\PCoIP Agent\bin\pcoip_agent.exe`\.  
**PCoIP abiter service change** — Be aware that the PCoIP arbiter service \(`C:\Program Files (x86)\Teradici\PCoIP Agent\bin\pcoip_arbiter_win32.exe`\) is removed when your WorkSpaces are updated to use the 64\-bit agent\.  
**PCoIP zero clients and USB devices** — Starting with version 20\.10\.4 of the PCoIP agent, Amazon WorkSpaces disables USB redirection by default through the Windows registry\. This registry setting affects the behavior of USB peripherals when your users are using PCoIP zero client devices to connect to their WorkSpaces\. For more information, see [USB printers and other USB peripherals aren't working for PCoIP zero clients](amazon-workspaces-troubleshooting.md#pcoip_zero_client_usb)\.
+ `C:\Program Files\Amazon`
+ `C:\Program Files\Teradici`
+ `C:\Program Files (x86)\Teradici`
+ `C:\ProgramData\Amazon`
+ `C:\ProgramData\Teradici`

On Amazon Linux WorkSpaces, the service components are installed in the following locations\. Do not delete, change, block, or quarantine these objects\. If you do so, the WorkSpace will not function correctly\.

**Note**  
Making changes to files other than `/etc/pcoip-agent/pcoip-agent.conf` might cause your WorkSpaces to stop working and might require you to rebuild them\. For information about modifying `/etc/pcoip-agent/pcoip-agent.conf`, see [Manage your Amazon Linux WorkSpaces](manage_linux_workspace.md)\.
+ `/etc/dhcp/dhclient.conf`
+ `/etc/logrotate.d/pcoip-agent`
+ `/etc/logrotate.d/pcoip-server`
+ `/etc/os-release`
+ `/etc/pam.d/pcoip`
+ `/etc/pam.d/pcoip-session`
+ `/etc/pcoip-agent`
+ `/etc/profile.d/system-restart-check.sh`
+ `/etc/X11/default-display-manager`
+ `/etc/yum/pluginconf.d/halt_os_update_check.conf`
+ `/lib/systemd/system/pcoip.service`
+ `/lib/systemd/system/pcoip-agent.service`
+ `/lib64/security/pam_self.so`
+ `/usr/bin/pcoip-fne-view-license`
+ `/usr/bin/pcoip-list-licenses`
+ `/usr/bin/pcoip-validate-license`
+ `/usr/lib/firewalld/services/pcoip-agent.xml`
+ `/usr/lib/modules-load.d/usb-vhci.conf`
+ `/usr/lib/pcoip-agent`
+ `/usr/lib/skylight`
+ `/usr/lib/systemd/system/pcoip.service`
+ `/usr/lib/systemd/system/pcoip.service.d/`
+ `/usr/lib/systemd/system/skylight-agent.service`
+ `/usr/lib/tmpfiles.d/pcoip-agent.conf`
+ `/usr/lib/yum-plugins/halt_os_update_check.py`
+ `/usr/sbin/pcoip-agent`
+ `/usr/sbin/pcoip-register-host`
+ `/usr/sbin/pcoip-support-bundler`
+ `/usr/share/doc/pcoip-agent`
+ `/usr/share/pcoip-agent`
+ `/usr/share/selinux/packages/pcoip-agent.pp`
+ `/usr/share/X11`
+ `/var/crash/pcoip-agent`
+ `/var/lib/pcoip-agent`
+ `/var/lib/skylight`
+ `/var/log/pcoip-agent` 
+ `/var/log/skylight`