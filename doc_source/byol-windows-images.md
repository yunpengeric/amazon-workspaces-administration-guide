# Bring Your Own Windows desktop licenses<a name="byol-windows-images"></a>

If your licensing agreement with Microsoft allows it, you can use your Windows 10 Enterprise or Windows 10 Pro desktop licenses for your WorkSpaces\. To do this, you must Bring Your Own License \(BYOL\) and provide a Windows 10 license that meets the following requirements\. For more information about using Microsoft software on AWS, see [Amazon Web Services and Microsoft](http://aws.amazon.com/windows/faq/)\.

To stay compliant with Microsoft licensing terms, AWS runs your BYOL WorkSpaces on hardware that is dedicated to you in the AWS Cloud\. By bringing your own license, you can provide a consistent experience for your users\. For more information, see [WorkSpaces Pricing](https://aws.amazon.com/workspaces/pricing/)\.

**Important**  
Image creation is not supported on Windows 10 systems that have been upgraded from one version of Windows 10 to a newer version of Windows 10 \(a Windows feature/version upgrade\)\. However, Windows cumulative or security updates are supported by the WorkSpaces image\-creation process\. 

**Topics**
+ [Requirements](#windows_images_prerequisites)
+ [Windows versions supported for BYOL](#windows_images_supported_versions)
+ [Add Microsoft Office to Your BYOL image](#windows_images_adding_office)
+ [Step 1: Check the eligibility of your account for BYOL using the Amazon WorkSpaces console](#windows_images_eligibility_byol)
+ [Step 2: Enable BYOL for your account for BYOL using the Amazon WorkSpaces console](#windows_images_enable_byol)
+ [Step 3: Run the BYOL Checker PowerShell script on a Windows VM](#windows_images_run_byol_checker_script)
+ [Step 4: Export the VM from your virtualization environment](#windows_images_create_image_byol)
+ [Step 5: Import the VM as an image into Amazon EC2](#windows_images_import_image_ec2_byol)
+ [Step 6: Create a BYOL image using the WorkSpaces console](#windows_images_create_byol_image_console)
+ [Step 7: Create a custom bundle from the BYOL image](#windows_images_create_custom_bundle_byol)
+ [Step 8: Register a dedicated directory for WorkSpaces](#windows_images_dedicate_directory_for_byol)
+ [Step 9: Launch your BYOL WorkSpaces](#windows_images_launch_byol_workspaces)

## Requirements<a name="windows_images_prerequisites"></a>

Before you begin, verify the following:
+ Your Microsoft licensing agreement allows Windows to be run in a virtual hosted environment\.
+ If you will be using non\-GPU\-enabled bundles \(bundles other than Graphics and GraphicsPro\), verify that you will use a minimum of 200 WorkSpaces per Region\. These 200 WorkSpaces can be any mix of AlwaysOn and AutoStop WorkSpaces\. Using a minimum of 200 WorkSpaces per Region is a requirement for running your WorkSpaces on dedicated hardware\. Running your WorkSpaces on dedicated hardware is necessary to comply with Microsoft licensing requirements\. The dedicated hardware is provisioned on the AWS side, so your VPC can stay on default tenancy\.

  If you plan to use GPU\-enabled \(Graphics and GraphicsPro\) bundles, verify that you will run a minimum of 4 AlwaysOn or 20 AutoStop GPU\-enabled WorkSpaces in a Region per month on dedicated hardware\.
**Note**  
Graphics and GraphicsPro bundles can be created only for the PCoIP protocol at this time\.
Graphics and GraphicsPro bundles aren't currently available in the Asia Pacific \(Mumbai\) Region\.
+ WorkSpaces can use a management interface in the /16 IP address range\. The management interface is connected to a secure WorkSpaces management network used for interactive streaming\. This allows WorkSpaces to manage your WorkSpaces\. For more information, see [Network interfaces](workspaces-port-requirements.md#network-interfaces)\. You must reserve a /16 netmask from at least one of the following IP address ranges for this purpose:
  + 10\.0\.0\.0/8
  + 100\.64\.0\.0/10
  + 172\.16\.0\.0/12
  + 192\.168\.0\.0/16
  + 198\.18\.0\.0/15
**Note**  
As you adopt the WorkSpaces service, the available management interface IP address ranges frequently change\. To determine which ranges are currently available, run the [ list\-available\-management\-cidr\-ranges](https://docs.aws.amazon.com/cli/latest/reference/workspaces/list-available-management-cidr-ranges.html) AWS Command Line Interface \(AWS CLI\) command\.
In addition to the /16 CIDR block that you select, the 54\.239\.224\.0/20 IP address range is used for management interface traffic in all AWS Regions\.
+ Make sure you have opened the necessary management interface ports for Microsoft Windows and Microsoft Office KMS activation for BYOL WorkSpaces\. For more information, see [Management interface ports](workspaces-port-requirements.md#management_ports)\.
+ You have a virtual machine \(VM\) that runs a supported 64\-bit version of Windows\. For a list of supported versions, see the next section in this topic, [Windows versions supported for BYOL](#windows_images_supported_versions)\. The VM must also meet these requirements: 
  + The Windows operating system must be activated against your key management servers\.
  + The Windows operating system must have **English \(United States\)** as the primary language\.
  + No software beyond what is included with Windows can be installed on the VM\. You can add additional software, such as an antivirus solution, when you later create a custom image\.
  + Do not customize the default user profile \(`C:\Users\Default`\) or make other customizations before creating an image\. All customizations should be made after image creation\. We recommend making any customizations to the user profile through Group Policy Objects \(GPOs\) and applying them after image creation\. This is because customizations done through GPOs can be easily modified or rolled back and are less prone to error than customizations made to the default user profile\.
  + You must create a **WorkSpaces\_BYOL** account with local administrator access before you share the image\. The password for this account might be required later, so make note of it\.
  + The VM must be on a single volume with a maximum size of 70 GB and at least 10 GB of free space\. If you're also planning to subscribe to Microsoft Office for your BYOL image, the VM must be on a single volume with a maximum size of 70 GB and at least 20 GB of free space\.
  + Your VM must run Windows PowerShell version 4 or later\.
+ Make sure that you have installed the latest Microsoft Windows patches before you run the BYOL checker script in [Step 3: Run the BYOL Checker PowerShell script on a Windows VM](#windows_images_run_byol_checker_script)\.

**Note**  
For BYOL AutoStop WorkSpaces, a large number of concurrent logins could result in significantly increased time for WorkSpaces to be available\. If you expect many users to log into your BYOL AutoStop WorkSpaces at the same time, please consult your account manager for advice\.

## Windows versions supported for BYOL<a name="windows_images_supported_versions"></a>

Your VM must run one of the following Windows versions:
+ Windows 10 Version 1809 \(October 2018 Update\)
+ Windows 10 Version 1903 \(May 2019 Update\)
+ Windows 10 Version 1909 \(November 2019 Update\)
+ Windows 10 Version 2004 \(May 2020 Update\)
+ Windows 10 Version 20H2 \(October 2020 Update\)

All supported OS versions support all of the compute types available in the AWS Region where you're using WorkSpaces\. Versions of Windows that are no longer supported by Microsoft are not guaranteed to work and are not supported by AWS Support\.

**Note**  
Windows 10 N versions are not supported for BYOL at this time\.

## Add Microsoft Office to Your BYOL image<a name="windows_images_adding_office"></a>

During the BYOL image ingestion process, if you are using Windows 10, you have the option to subscribe to Microsoft Office Professional 2016 \(32\-bit\) or 2019 \(64\-bit\) through AWS\. If you choose this option, Office is pre\-installed in your BYOL image and included on any WorkSpaces that you launch from this image\.

If you choose to subscribe to Office through AWS, additional charges will apply\. For more information, see [WorkSpaces Pricing](https://aws.amazon.com/workspaces/pricing/)\.

**Important**  
If Microsoft Office is already installed on the VM that you are using to create your BYOL image, you must uninstall it from the VM if you want to subscribe to Office through AWS\.
If you plan to subscribe to Office through AWS, make sure that your VM has at least 20 GB of free disk space\.

If you choose to subscribe to Office, the BYOL image ingestion process takes a minimum of 3 hours\.

For details about subscribing to Office during the BYOL ingestion process, see [Step 6: Create a BYOL image using the WorkSpaces console](#windows_images_create_byol_image_console)\.

**Office language settings**  
We choose the language used for your Office subscription based on the AWS Region where you're performing your BYOL image ingestion\. For example, if you're performing your BYOL image ingestion in the Asia Pacific \(Tokyo\) Region, your Office subscription has Japanese as its language\.

By default, we install a number of frequently used Office language packs on your WorkSpaces\. If the language pack that you want isn't installed, you can download additional language packs from Microsoft\. For more information, see [ Language Accessory Pack for Office](https://support.microsoft.com/office/language-accessory-pack-for-office-82ee1236-0f9a-45ee-9c72-05b026ee809f) in the Microsoft documentation\.

To change the language for Office, you have several options:

### Option 1: Allow individual users to customize their Office language settings<a name="option1_office_languages"></a>

Individual users can adjust the Office language settings on their WorkSpaces\. For more information, see [ Add an editing or authoring language or set language preferences in Office](https://support.microsoft.com/office/add-an-editing-or-authoring-language-or-set-language-preferences-in-office-663d9d94-ca99-4a0d-973e-7c4a6b8a827d#ID0EAACAAA=Newer_versions) in the Microsoft documentation\.

### Option 2: Use GPO administrative templates \(\.admx/\.adml\) to enforce default Office language settings for all of your WorkSpaces users<a name="option2_office_languages"></a>

You can use Group Policy Object \(GPO\) settings to enforce default Office language settings for your WorkSpaces users\.

**Note**  
Your WorkSpaces users will not be able to override language settings enforced through GPO\.

For more information about using GPO to set the language for Office, see [ Customize language setup and settings for Office](https://docs.microsoft.com/deployoffice/office2016/customize-language-setup-and-settings-for-office-2016#customize-language-settings) in the Microsoft documentation\. Office 2016 and Office 2019 use the same GPO settings \(labeled with Office 2016\)\.

To work with GPOs, you must install the Active Directory administration tools\. For information about using the Active Directory administration tools to work with GPOs, see [Set up Active Directory Administration Tools for WorkSpaces](directory_administration.md)\.

Before you can configure Office 2016 or Office 2019 policy settings, you must download the [ administrative template files \(\.admx/\.adml\) for Office](https://www.microsoft.com/download/details.aspx?id=49030) from the Microsoft Download Center\. After you download the administrative template files, you must add the `office16.admx` and `office16.adml` files to the Central Store of the domain controller for your WorkSpaces directory\. \(The `office16.admx` and `office16.adml` files apply to both Office 2016 and Office 2019\.\) For more information about working with `.admx` and `.adml` files, see [ How to create and manage the Central Store for Group Policy Administrative Templates in Windows](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) in the Microsoft documentation\.

The following procedure describes how to create the Central Store and add the administrative template files to it\. Perform the following procedure on a directory administration WorkSpace or Amazon EC2 instance that is joined to your WorkSpaces directory\.

**To install the Group Policy administrative template files for Office**

1. Download the [ administrative template files \(\.admx/\.adml\) for Office](https://www.microsoft.com/download/details.aspx?id=49030) from the Microsoft Download Center\.

1. On a directory administration WorkSpace or an Amazon EC2 instance that is joined to your WorkSpaces directory, open Windows File Explorer, and in the address bar, enter your organization's fully qualified domain name \(FQDN\), such as `\\example.com`\.

1. Open the `SYSVOL` folder\.

1. Open the folder with the `FQDN` name\.

1. Open the `Policies` folder\. You should now be in `\\FQDN\SYSVOL\FQDN\Policies`\.

1. If it doesn't already exist, create a folder named `PolicyDefinitions`\.

1. Open the `PolicyDefinitions` folder\.

1. Copy the `office16.admx` file into the `\\FQDN\SYSVOL\FQDN\Policies\PolicyDefinitions` folder\.

1. Create a folder named `en-US` in the `PolicyDefinitions` folder\.

1. Open the `en-US` folder\.

1. Copy the `office16.adml` file into the `\\FQDN\SYSVOL\FQDN\Policies\PolicyDefinitions\en-US` folder\.

**To configure the GPO language settings for Office**

1. On your directory administration WorkSpace or Amazon EC2 instance that is joined to your WorkSpaces directory, open the Group Policy Management tool \(gpmc\.msc\)\.

1. Expand the forest \(**Forest:*FQDN***\)\.

1. Expand **Domains**\. 

1. Expand your FQDN \(for example, `example.com`\)\.

1. Select your FQDN, open the context \(right\-click\) menu or open the **Action** menu, and choose **Create a GPO in this domain, and Link it here**\.

1. Name your GPO \(for example, **Office**\)\.

1. Select your GPO, open the context \(right\-click\) menu or open the **Action** menu, and choose **Edit**\.

1. In the Group Policy Management Editor, choose **User Configuration**, **Policies**, **Administrative Template Policy definitions \(ADMX files\) retrieved from the local computer**, **Microsoft Office 2016**, and **Language Preferences**\.
**Note**  
Office 2016 and Office 2019 use the same GPO settings \(labeled with Office 2016\)\. If you don't see **Administrative Template Policy definitions \(ADMX files\) retrieved from the local computer** under **User Configuration**, **Policies**, the `office16.admx` and `office16.adml` files aren't correctly installed on your domain controller\.

1. Under **Language Preferences**, specify the language that you want for the following settings\. Be sure to set each setting to **Enabled**, and then under **Options**, select the language you want\. Choose **OK** to save each setting\.
   + **Display Language** > **Display help in**
   + **Display Language** > **Display menus and dialog boxes in**
   + **Editing languages** > **Primary Editing Language**

1. Close the Group Policy Management tool when you're finished\.

1. Group Policy setting changes take effect after the next Group Policy update for the WorkSpace and after the WorkSpace session is restarted\. To apply the Group Policy changes, do one of the following:
   + Reboot the WorkSpace \(in the Amazon WorkSpaces console, select the WorkSpace, then choose **Actions**, **Reboot WorkSpaces**\)\.
   + From an administrative command prompt, enter gpupdate /force\.

### Option 3: Update the Office language registry settings on your WorkSpaces<a name="option3_office_languages"></a>

To set the Office language settings through the registry, update the following registry settings:
+ **HKEY\_CURRENT\_USER\\SOFTWARE\\Microsoft\\Office\\16\.0\\Common\\LanguageResources\\UILanguage**
+ **HKEY\_CURRENT\_USER\\SOFTWARE\\Microsoft\\Office\\16\.0\\Common\\LanguageResources\\HelpLanguage**

For these settings, add a DWORD key value with the appropriate Office locale ID \(LCID\)\. For example, the LCID for English \(US\) is 1033\. Because LCIDs are decimal values, you must set the **Base** option for the DWORD value to **Decimal**\. For a list of the Office LCIDs, see [Language identifiers and OptionState Id values in Office 2016](https://docs.microsoft.com/deployoffice/office2016/language-identifiers-and-optionstate-id-values-in-office-2016) in the Microsoft documentation\.

You can apply these registry settings to your WorkSpaces through GPO settings or a logon script\.

For more information about working with the language settings for Office, see [ Customize language setup and settings for Office](https://docs.microsoft.com/deployoffice/office2016/customize-language-setup-and-settings-for-office-2016) in the Microsoft documentation\. 

**Add Office to your existing BYOL WorkSpaces**  
You can also add a subscription to Office to your existing BYOL WorkSpaces\. After you have created a BYOL bundle with Office installed, you can use the WorkSpaces migration feature to migrate your existing BYOL WorkSpaces to the BYOL bundle that is subscribed to Office\. For more information, see [Migrate a WorkSpace](migrate-workspaces.md)\.

**Migrate between versions of Microsoft Office**  
To migrate from Office 2016 to Office 2019 or from Office 2019 to Office 2016, you must create a BYOL bundle that is subscribed to the version of Office that you want to migrate to\. Then, you use the WorkSpaces migration feature to migrate your existing BYOL WorkSpaces that are subscribed to Office to the BYOL bundle that is subscribed to the version of Office that you want to migrate to\.

For example, to migrate from Office 2016 to Office 2019, create a BYOL bundle that is subscribed to Office 2019\. Then use the WorkSpaces migration feature to migrate your existing BYOL WorkSpaces that are subscribed to Office 2016 to the BYOL bundle that is subscribed to Office 2019\.

For more information about the migration process, see [Migrate a WorkSpace](migrate-workspaces.md)\.

**Unsubscribe from Office**  
To unsubscribe from Office, you must create a BYOL bundle that is not subscribed to Office\. Then use the WorkSpaces migration feature to migrate your existing BYOL WorkSpaces to the BYOL bundle that is not subscribed to Office\. For more information, see [Migrate a WorkSpace](migrate-workspaces.md)\.

**Office updates**  
If you have subscribed to Office through AWS, Office updates are included as part of your regular Windows updates\. To stay current on all security patches and updates, we recommend that you periodically update your BYOL base images\.

## Step 1: Check the eligibility of your account for BYOL using the Amazon WorkSpaces console<a name="windows_images_eligibility_byol"></a>

Before you can enable your account for BYOL, you must go through a verification process to confirm your eligibility for BYOL\. Until you go through this process, the **Enable BYOL** option will not be available in your Amazon WorkSpaces console\. 

**Note**  
The verification process takes at least one business day and can take longer if you want to link two or more BYOL\-enabled AWS accounts together so that they use the same underlying hardware\.

**To check the eligibility of your account for BYOL by using the Amazon WorkSpaces console**

1. Open the WorkSpaces console at [https://console\.aws\.amazon\.com/workspaces/](https://console.aws.amazon.com/workspaces/)\.

1. In the navigation pane, choose **Account Settings**, and then under **Bring your own license \(BYOL\)**, choose **View WorkSpaces BYOL settings**\. If your account is not currently eligible for BYOL, a message provides guidance for next steps\. To get started, contact your AWS account manager or sales representative, or contact the [AWS Support Center](https://console.aws.amazon.com/support/home#/)\. Your contact will verify your eligibility for BYOL\.

   To determine your eligibility for BYOL, your contact will need certain information from you\. For example, you might be asked to answer the following questions\.
   + Have you reviewed and accepted the [ BYOL requirements](#windows_images_prerequisites) listed earlier?
   + In which AWS Regions do you need your account enabled for BYOL?
   + How many BYOL WorkSpaces do you plan to deploy per AWS Region?
   + What is your ramp\-up plan?
   + Are you purchasing WorkSpaces from a reseller?
   + What bundle types do you need for BYOL?
   + Does your organization have any other AWS accounts enabled for BYOL in the same Region? If yes, do you want to link these accounts so that they use the same underlying hardware?

     If the accounts are linked, the total number of WorkSpaces deployed in these accounts is aggregated together for the purposes of determining your eligibility for BYOL\. Be aware that linking the accounts will take additional time\. If you want to link the accounts, be ready to provide the account numbers to your contact\.

1. After your eligibility is confirmed for BYOL, you can proceed to the next step, where you enable BYOL for your account in the Amazon WorkSpaces console\.

## Step 2: Enable BYOL for your account for BYOL using the Amazon WorkSpaces console<a name="windows_images_enable_byol"></a>

To enable BYOL for your account, you must specify a management network interface\. This interface is connected to a secure Amazon WorkSpaces management network\. It is used for interactive streaming of the WorkSpace desktop to Amazon WorkSpaces clients, and to allow Amazon WorkSpaces to manage the WorkSpace\.

**Note**  
The steps in this procedure for enabling BYOL for your account need to be performed only once per account, per Region\.

**To enable BYOL for your account by using the Amazon WorkSpaces console**

1. Open the WorkSpaces console at [https://console\.aws\.amazon\.com/workspaces/](https://console.aws.amazon.com/workspaces/)\.

1. In the navigation pane, choose **Account Settings**, and then under **Bring your own license \(BYOL\)**, choose **View WorkSpaces BYOL settings**\. 

1. On the **Account Settings** page, under **Bring Your Own License \(BYOL\)**, choose **Enable BYOL**\.

   If you don't see the **Enable BYOL** option, this means that your account is not currently eligible for BYOL\. For more information, see [Step 1: Check the eligibility of your account for BYOL using the Amazon WorkSpaces console](#windows_images_eligibility_byol)\.

1. Under **Bring Your Own License \(BYOL\)**, in the **Management network interface IP address range** area, choose an IP address range, and then choose **Display available CIDR blocks**\.

   Amazon WorkSpaces searches for and displays available IP address ranges as IPv4 Classless Inter\-Domain Routing \(CIDR\) blocks, within the range that you specify\. If you require a specific IP address range, you can edit the search range\.
**Important**  
**After you specify an IP address range, you cannot modify it\.** Make sure to specify an IP address range that doesn't conflict with the ranges used by your internal network\. If you have any questions about which range to specify, contact your AWS account manager or sales representative, or contact the [AWS Support Center](https://console.aws.amazon.com/support/home#/) before proceeding\.

1. Choose the CIDR block that you want from the list of results, and then choose **Enable BYOL**\.

   This process may take several hours\. While WorkSpaces is enabling your account for BYOL, proceed to the next step\. 

## Step 3: Run the BYOL Checker PowerShell script on a Windows VM<a name="windows_images_run_byol_checker_script"></a>

After you enable BYOL for your account, you must confirm that your VM meets the requirements for BYOL\. To do so, perform these steps to download and run the WorkSpaces BYOL Checker PowerShell script\. The script performs a series of tests on the VM that you plan to use to create your image\. 

**Important**  
The VM must pass all tests before you can use it for BYOL\.

**To download the BYOL Checker script**

Before you download and run the BYOL Checker script, verify that the latest Windows security updates are installed on your VM\. While this script runs, it disables the Windows Update service\. 

1. Download the BYOL Checker script \.zip file from [https://tools\.amazonworkspaces\.com/BYOLChecker\.zip](https://tools.amazonworkspaces.com/BYOLChecker.zip) to your `Downloads` folder\.

1. In your `Downloads` folder, create a `BYOL` folder\.

1. Extract the files from `BYOLChecker.zip` and copy them to the `Downloads\BYOL` folder\.

1. Delete the `Downloads\BYOLChecker.zip` folder so that only the extracted files remain\.

Perform these steps to run the BYOL Checker script\.

**To run the BYOL Checker script**

1. From the Windows desktop, open Windows PowerShell\. Choose the Windows **Start** button, right\-click **Windows PowerShell**, and choose **Run as administrator**\. If you are prompted by User Account Control to choose whether you want PowerShell to make changes to your device, choose **Yes**\. 

1. At the PowerShell command prompt, change to the directory where the BYOL Checker script is located\. For example, if the script is located in the `Downloads\BYOL` directory, enter the following command and press **Enter**:

   `cd C:\Users\username\Downloads\BYOL`

1. Enter the following command to update the PowerShell execution policy on the computer\. Doing so allows the BYOL Checker script to run: 

   `Set-ExecutionPolicy Unrestricted`

1. When prompted to confirm whether to change the PowerShell execution policy, enter A to specify Yes to All\.

1. Enter the following command to run the BYOL Checker script:

   `.\BYOLChecker.ps1`

1. If a security notification appears, press the **R** key to Run Once\.

1. <a name="step_begin_tests"></a>In the **WorkSpaces Image Validation** dialog box, choose **Begin Tests**\.

1. <a name="step_resolve_issues"></a>After each test is completed, you can view the status of the test\. For any test with a status of **FAILED**, choose **Info** to display information about how to resolve the issue that caused the failure\. If any tests display a status of **WARNING**, choose the **Fix All Warnings** button\.

1. If applicable, resolve any issues that cause test failures and warnings, and repeat [Step 7](#step_begin_tests) and [Step 8](#step_resolve_issues) until the VM passes all tests\. All failures and warnings must be resolved before you export the VM\.

1. The BYOL script checker generates two log files, `BYOLPrevalidationlogYYYY-MM-DD_HHmmss.txt` and `ImageInfo.text`\. These files are located in the directory that contains the BYOL Checker script files\.
**Tip**  
Do not delete these files\. If an issue occurs, they might be helpful in troubleshooting\.

1. After your VM passes all tests, you get a **Validation Successful** message\. Review the VM locale settings displayed in the tool\. To update the locale settings, follow [these instructions](https://docs.microsoft.com/previous-versions/windows/hardware/previsioning-framework/dn965674(v=vs.85)) in the Microsoft documentation and run the BYOL Checker script again\.

1. <a name="step_create_VM_snapshot"></a>Shut down the VM and create a snapshot of it\.

1. Start the VM again\. Choose **Run Sysprep**\. If Sysprep is successful, your VM that you exported after [Step 12](#step_create_VM_snapshot) can be imported into Amazon Elastic Compute Cloud \(Amazon EC2\)\. Otherwise, review the Sysprep logs, roll back to the snapshot taken at [Step 12](#step_create_VM_snapshot), resolve the reported issues, take a new snapshot, and run the BYOL Checker script again\.

   The most common reason that Sysprep fails is that the Modern AppX Packages are not uninstalled for all users\. Use the Remove\-AppxPackage PowerShell cmdlet to remove the AppX Packages\.

1. After you have successfully created your image, you can remove the **WorkSpaces\_BYOL** account\.

## Step 4: Export the VM from your virtualization environment<a name="windows_images_create_image_byol"></a>

To create an image for BYOL, you must first export the VM from your virtualization environment\. The VM must be on a single volume with a maximum size of 70 GB and at least 10 GB of free space\. For more information, see the documentation for your virtualization environment and [Export Your VM from its Virtualization Environment](https://docs.aws.amazon.com/vm-import/latest/userguide/vmimport-image-import.html#export-vm-image) in the *VM Import/Export User Guide*\.

## Step 5: Import the VM as an image into Amazon EC2<a name="windows_images_import_image_ec2_byol"></a>

After you export your VM, review the requirements for importing Windows operating systems from a VM\. Take action as needed\. For more information, see [VM Import/Export Requirements](https://docs.aws.amazon.com/vm-import/latest/userguide/vmie_prereqs.html)\.

**Note**  
Importing a VM with an encrypted disk is not supported\. If you've opted in to default encryption for Amazon Elastic Block Store \(Amazon EBS\) volumes, you must deselect that option before importing your VM\.

Import your VM into Amazon EC2 as an Amazon Machine Image \(AMI\)\. Use one of the following methods:
+ Use the import\-image command with the AWS CLI\. For more information, see [import\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/import-image.html) in the *AWS CLI Command Reference*\.
+ Use the ImportImage API operation\. For more information, see [ImportImage](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ImportImage.html) in the *Amazon EC2 API Reference*\.

For more information, see [Importing a VM as an Image](https://docs.aws.amazon.com/vm-import/latest/userguide/vmimport-image-import.html) in the *VM Import/Export User Guide*\.

## Step 6: Create a BYOL image using the WorkSpaces console<a name="windows_images_create_byol_image_console"></a>

Perform these steps to create an WorkSpaces BYOL image\. 

**Note**  
To perform this procedure, verify that you have AWS Identity and Access Management \(IAM\) permissions to:  
Call WorkSpaces **ImportWorkspaceImage**\.
Call Amazon EC2 **DescribeImages** on the Amazon EC2 image that you want to use to create the BYOL image\.
Call Amazon EC2 **ModifyImageAttribute** on the Amazon EC2 image that you want to use to create the BYOL image\. Make sure that the launch permissions on the Amazon EC2 image are not restricted\. The image must be shareable throughout the BYOL image creation process\.
For an example IAM policy specific to BYOL WorkSpaces, see [Identity and access management for WorkSpaces](workspaces-access-control.md)\. For more information about working with IAM permissions, see [Changing Permissions for an IAM User ](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html) in the *IAM User Guide*\.  
To create a Graphics or GraphicsPro bundle from your image, contact the [AWS Support Center](https://console.aws.amazon.com/support/home#/) to get your account added to the allow list\. After your account is on the allow list, you can use the AWS CLI import\-workspace\-image command to ingest the Graphics or GraphicsPro image\. For more information, see [import\-workspace\-image](https://docs.aws.amazon.com/cli/latest/reference/workspaces/import-workspace-image.html) in the *AWS CLI Command Reference*\.

**To create an image from the Windows VM**

1. Open the WorkSpaces console at [https://console\.aws\.amazon\.com/workspaces/](https://console.aws.amazon.com/workspaces/)\.

1. In the navigation pane, choose **Images**\.

1. Choose **Create BYOL image**\. 

1. On the **Create BYOL image** page, do the following: 
   + For **AMI ID**, choose the **EC2 Console** link, and choose the Amazon EC2 image that you imported as described in the previous section \([Step 5: Import the VM as an image into Amazon EC2](#windows_images_import_image_ec2_byol)\)\. The image name must begin with `ami-` and be followed by the identifier for the AMI \(for example, `ami-1234567e`\)\.
   + For **Image name**, enter a unique name for the image\.
   + For **Description**, enter a description to help you quickly identify the image\.
   + For **Instance type**, choose the appropriate bundle type \(either **Regular**, **Graphics**, or **GraphicsPro**\), depending on which protocol you want to use for your image, either PCoIP or WorkSpaces Streaming Protocol \(WSP\)\. For non\-GPU\-enabled bundles \(bundles other than Graphics or GraphicsPro\), choose **Regular**\.
**Note**  
Graphics and GraphicsPro images can be created only for the PCoIP protocol at this time\.
   + \(Optional\) For **Select applications**, choose which version of Microsoft Office you want to subscribe to\. For more information, see [Add Microsoft Office to Your BYOL image](#windows_images_adding_office)\.
   + \(Optional\) For **Tags**, choose **Add new tag** to associate tags with this image\. For more information, see [Tag WorkSpaces resources](tag-workspaces-resources.md)\.

1. Choose **Create BYOL image**\.

   While your image is being created, the image's status on the **Images** page of the console appears as **Pending**\. The BYOL ingestion process takes a minimum of 90 minutes\. If you have subscribed to Office as well, expect the process to take a minimum of 3 hours\. 

   If the image validation does not succeed, the console displays an error code\. When the image creation is complete, the status changes to** Available**\.

## Step 7: Create a custom bundle from the BYOL image<a name="windows_images_create_custom_bundle_byol"></a>

After your BYOL image is created, you can use the image to create a custom bundle\. For information, see [Create a custom WorkSpaces image and bundle](create-custom-bundle.md)\.

## Step 8: Register a dedicated directory for WorkSpaces<a name="windows_images_dedicate_directory_for_byol"></a>

To use BYOL images for WorkSpaces, you must register a directory for this purpose\.

**To register a directory for WorkSpaces**

1. Open the WorkSpaces console at [https://console\.aws\.amazon\.com/workspaces/](https://console.aws.amazon.com/workspaces/)\.

1. In the navigation pane, choose **Directories**\.

1. Select the directory and choose **Actions**, **Register**\.

1. In the **Register directory** dialog box, for **Enable Dedicated WorkSpaces**, choose **Yes**\.

1. Choose **Register**\.

If you have already registered an AWS Managed Microsoft AD directory or an AD Connector directory for WorkSpaces that does not run on dedicated hardware, you can set up a new AWS Managed Microsoft AD directory or AD Connector directory for this purpose\. You can also deregister the directory and then reregister it as a directory for dedicated WorkSpaces\. To do so, perform these steps\.

**Note**  
You can only perform this procedure if no WorkSpaces are associated with the directory\.

**To deregister a directory and reregister it for dedicated WorkSpaces**

1. Open the WorkSpaces console at [https://console\.aws\.amazon\.com/workspaces/](https://console.aws.amazon.com/workspaces/)\.

1. Terminate existing WorkSpaces\.

1. In the navigation pane, choose **Directories**\.

1. Select the directory and choose **Actions**, **Deregister**\.

1. When prompted for confirmation, choose **Deregister**\.

1. Select the directory again and choose **Actions**, **Register**\.

1. In the **Register directory** dialog box, for **Enable Dedicated WorkSpaces**, choose **Yes**\.

1. Choose **Register**\.

## Step 9: Launch your BYOL WorkSpaces<a name="windows_images_launch_byol_workspaces"></a>

After you register a directory for dedicated WorkSpaces, you can launch your BYOL WorkSpaces in this directory\. For information about how to launch WorkSpaces, see [Launch a virtual desktop using WorkSpaces](launch-workspaces-tutorials.md)\.