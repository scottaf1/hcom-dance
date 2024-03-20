## __Hive Hybrid Cloud Operations Manager (HCOM) - AWS Platform Configuration Manager__  
(click Widget refresh in upper right corner to return to User Guide Index)  
Last updated on: 7/12/2023    
## __Description__  

This page provides guidance on how to use the widgets on this __MSP Platform Configuration Manager__ - CloudWatch Dashboard to view and update Hive HCOM platform configuration settings.  

1. __Configuration Items__  
    * __Platform Settings__  
        * Top Level - manage once for the entire platform deployment  
        * 2nd Level - AWS account managed by the platform deployment (for each Tenant)  
        * 3rd Level - Platform settings by AWS account (for each AWS Account)  
2. __CloudWatch Dashboard Widgets__   
You can add and modify widgets using the following default settings. For example, you can create a new CloudWatch Dashboard and add a Custom Widget and use the settings below to enable any of the widgets below.  

    * AWS Account Management  
        *   Custom Widget Parameters: {"Account": "***replace with AWS account***","Tenant": "VDMS","widget": 4,"mode": 8}  
    * CloudWatch Profiles  
        *   Custom Widget Parameters: {"widget": 4,"mode": 3,"Profile": 1,"resourcetype": "dynamodb-profile"}  
    * State Configuration Profiles (includes Patch Management)  
        *   Custom Widget Parameters: {"Account": "***replace with AWS account***","Tenant": "VDMS","widget": 4,"mode": 2,"Profile": 1,"profiletype": "state-profile"} 
    * Hive Platform User's Guide  
        *   Custom Widget Parameters: {"widget": 6,"mode": 1,"docs": "index.md"}   


## __Configuration Items__  

1. The following configuration items or Settings are managed once for the entire platform deployment. Each deployment is defined as a unique deployment of the HCOM-Ops-Foundation CloudFormation Template to a primary and secondary region.
    * __Central Management__ AWS Account Number and primary region
    * __GovCloud__ - this feature flag controls the partition setting for dynamically created ARNs within the platform and the exepected names for regions.
    * S3 Bucket for platform software components used during platform deployment and operations
    * S3 Bucket and Region for platform generated reports during operations
    * Tenant Name - this uniquely defines the name for one or more AWS accounts associated with an organizational unit.

2. The following configuration items are managed once for each AWS Account to be managed by the platform. AWS accounts are associated to Tenants
    * __AWS Account Number__
        * __Account Name__ (friendly name - used in platform object naming and forms)
        * Status - is this Account Active or not. Active accounts are visible in forms and referenced in lifecycle automations. Inactive or disabled accounts will not show up in forms and will not be referenced in lifecycle automations. All platform functions will be disabled for inactive AWS accounts.
        * __Regions__ - These are the AWS regions for the named AWS Account
        * __Environments__ - DEV, TEST, STAGE, PROD, BCDR (Examples of typical environments)
        * __Platform Features__
            1. __AWS Backup__ (Most Recent Backup - maximum time since last successful backup. Anything older is non-compliant.)
            2. __CloudWatch__ (EC2,OnPrem). AWS CloudWatch charges for metrics, alarms and Dashboards but includes a set number of metrics and alarms for free.
            3. __Systems Manager Operation Management (OpsItems)__ - will create OpsItems when CloudWatch alarms trigger. You are charged by AWS based on the number of OpsItem API calls per month and related API calls. See pricing at: http://aws.amazon.com/systems-manager/pricing/. As an alternative to using this feature, you may choose to Enable AWS SSM Operation Management to automatically create OpsItems for CloudWatch events. See details at: https://docs.aws.amazon.com/systems-manager/latest/userguide/OpsCenter-setup.html
            4. __OnPremise__ - this feature enables managing and monitoring onpremise Linux and Windows servers
            5. __State Configuration Management__ - define and enable lifecycle management of reusable profiles that utilize Ansible or Saltstack playbooks to install and/or maintain configuration state. You can also specify a STIG level (Low, Medium, or High). This feature utilizes pre-existing SSM Run Documents with State Manager Assoiations to schedule maintaining state and report node compliance.
            6. __Patch Management__
            7. __Webhooks__
        * __Standard Tags__ (standard tag names can be customized)
            1. __Description__ - general purpose tag for describing the instance or workload
            2. __Environment__ - to describe which environment this workload belongs to use. Often used for operational automations
            3. __Product__ - what is the primary product installed on this workload
            4. __Role__ - what sub-feature of product is installed or functional role of this workload
            5. __Patch__ - (traditional Patch Group) tag used for patch automation or other operational automation
        * __Communication and Alerts__ (via SNS Topics)
            1. __General__ - send alerts to the business team
            2. __HelpDesk__ - send alerts to the operations team
---
## __Tenant Management Widget__
__Overview__  
The purpose of this widget is to add named Tenants which is a construct to group one or more AWS accounts that belong to an organizational unit. Configuration management of the Hive HCOM platform (The CloudWatch Dashboard widgets described on this page) and audit reports generated by the platform group information at various levels starting with Tenant. Platform objects that are created embed the Tenant name for easy identification and management.  

__General Guidance__  
Create a Tenant for each unique organizational unit to be managed with the Hive HCOM platform. It represents the top level of the meta model with all other information elements rolling up to it. Examples would to define by department or product team.
__USE CAUTION__ when editing/modifying!! Names are a critical part of platform object names. Do not modify existing tenant names if platform objects like CloudWatch,State, and Patch profiles exist. Delete all platform components before changing an existing Tenant name. Otherwise, you will orphan platform objects that will no longer be managed but may still incur AWS usage charges.

1. __Select Tenant__  
    *   Select Tenant to edit the name by selecting in the drop-down and clicking __'Edit'__. Review the caution state listed above and within the Edit form before saving any changes.  

2. __Options__  
    * __New__    
        * Provides form to add/create a new named Tenant not already in the drop-down list of Tenants. This is requried if you are trying to manage AWS Accounts and/or profiles for a Tenant not already in the Tenant drop-down list.  

    * __Clear Tenant & Account Cache__  
        * Clicking this button will clear Hive HCOM platform caches. Caches are used to speed up UI performance and reduce unnecessary AWS API calls. If you are making changes using the widgets on the HCOM-Platform-Configuration-Manager dashboard but are not seeing the changes, you may need to Click __'Clear Tenant & Account Cache'__. Then refresh the browser page for the dashboard. Your changes should now be visible.  

---  
## __AWS Account Management Widget__
__Overview__   
The purpose of his widget is to enroll and manage AWS accounts and all the platform settings controlled at the AWS account level. Enrollment simply adds an existing AWS account to the platform for management (this does not create a new AWS account within AWS Organizations).  

__General Guidance__  
You enroll a new account by clicking the __'Enroll pre-exising AWS account for the selected Tenant'__ button. This will create an AWS account profile with a faux AWS account number ending with the letter __'Z'__, faux friendly account name also ending in __'Z'__, and default profile settings. This new AWS account profile will be disabled by default and must be subsequently modified to adjust profile settings and replace the faux AWS account number and faux account name with the real/actual AWS account number and a friendly account name to be enrolled and managed by the Hive HCOM platform. All of these elements will be used within object naming conventions and should not be change later. Failure to remove the __'Z'__ from the AWS account and Account Name fields will prevent the account from being Acive. Failure to enter a valid AWS Account number may prevent some platform features from working for all valid AWS accounts.  

1. __Default form__  - used to set filters for managing and enrolling existing AWS Accounts for specified Tenant.  
    * Select Tenant from drop-down (must be previously created). Click __'Select Tenant'__ to refresh form and populate AWS accounts enrolled for selected Tenant.  
    * Select AWS Managed Account from drop-down (must be previous enrolled to be in drop-down). Click __'Select Managed Account'__ to load account profile settings into the account update form below.  

2. __Update AWS Account profile settings form__  

    * __Settings__ form fields:
        * Active? (drop-down)
            *   __'True'__ sets the AWS account to active. Active accounts are visible throughout Hive platform configuration forms to be managed and will be included in audit reporting.  
            *   __'False'__ disables the acccount and thus, will not longer be visible throughout Hive Platform configuration forms and scheduled audits will not attempt to audit the account.  
        * AWS #  
            *   Replace initial faux number with __'Z'__ with a valid AWS account number.  
        * Account Name  
            *   Enter a friendly, unique name for the AWS account. This will be used in object naming and audit reports to make it easier to translate the account number to something more meaningful to operations teams managing the platform.  
        * Regions  
            *   This is a comma separated list of AWS Region names using Python List notation. Include only regions where resources are to be managed by the platform.  
        * Environments  
            *   This is a comma separated list of friendly environment names using Python List notation. It is common to have separate VPCs within an account for different SDLC environments such as: TEST, DEV, PROD, etc. List only environments that will be used.  

    * __Features Enabled__ (these are all platform feature flags):  
        * SSMOpsItems   
            *   __'True'__ sets the Sysems Manager (SSM) OpsItems to active. Will create an alarm action to create OpsItem. Alarm creation must be done after the flag is set to 'True' to create OpsItems Alarm Actions.  
            *   __'False'__ disables SSM OpsIems. Will create CloudWatch alarms without alarm actions that create OpsItem. If disabling after having it enabled, will remove previous alarm actions when alarms are recreated during workload lifecycle event like 'star' or 'reboot' or manual alarm creation is initiated.
        * CloudWatch  
            *   __'True'__ sets the use of CloudWatch Alarms to active. Will automatically create alarms for supported workloads during lifecycle events that have the requrired tags that reference a valid CloudWatch Profile defined in the platform.  
            *   __'False'__ disables use of CloudWatch Alarms. Will prevent CloudWatch alarm lifecycle management. If disabling after having it enabled, will not automatically remove pre-existing alarms. You can either manually delete them using CloudWatch console to filter by instance id or wait 7 days (you must remove or disable the CloudWatch Agent on the workload. This will stop metrics from being sent.) and the alarms will automatically be deleted by the daily CloudWatch Alarm audit report. Any managed alarm that does not receive metrics for 7 days will be deleted by the platform.  
        * StateMgmt  
            *   __'True'__ sets the use of Sysems Manager (SSM) State Manager to active. Allows you to 'Enable' State Profile Configurations.  
            *   __'False'__ disables the use of Sysems Manager (SSM) State Manager. You can still create profiles and configurations but will not be able to 'Enable' configuraions. If disabling after having it enabled, you will need to manually go in to each 'Enabled' configuraion to 'Disable' it to remove the current SSM State Association.  
        * OnPremise  
            *   __'True'__ enables platform features supporting non-AWS workloads.   
            *   __'False'__ disables platform features supporting non-AWS workloads. If disabling after having it enabled, will not automatically remove all profiles and objects associated with the non-AWS workloads. It will disable auditing and reporting of OnPremise workloads.  
        * AWSBackup  
            *   __'True'__ sets the AWS Backup feature to active. This currently controls the AWS Audit report.  
            *   __'False'__ disables AWS Backup feature and daily audit report.   
        * Webhooks  
            *   __'True'__ sets Webhooks feature to active. The platform supports use of Webhooks (MS Teams and Slack) for certains alerts and automation notifications. Be sure to properly configure webhook settings if enabling this feature found in the Webhooks further down in this form.  
            *   __'False'__ disables Webhooks feature.  Will ignore Webhook settings visible further down in this form.  

    * __STANDARD Tag Names__  
        * description  
            *   Tag used on workload to describe the workload  
        * environment  
            *   Tag used on workload to describe the environment as described earlier in this documentation   
        * product  
            *   Tag used on workload to describe the product installed on this workload  
        * role  
            *   Tag used on workload to describe the functional role of this workload  
        * Patch  
            *   Tag used on workload to describe the Patch Group  

    * __SNS Topic ARNs__    
        * General  
            *   AWS ARN to SNS topic in the central account. Uses dynamically replace keywords in the ARN for example, partition, region, centralaccount, will dynamically get replaced with the actual values. The only part that should be change is the SNS topic name found at the end of the ARN. Be advised that this platform does not create the SNS topics nor add supscriptions and subscription validation. This should be done out side the Hive HCOM platform.   
        * Help Desk  
            *   AWS ARN to SNS topic in the central account. Uses dynamically replace keywords in the ARN for example, partition, region, centralaccount, will dynamically get replaced with the actual values. The only part that should be change is the SNS topic name found at the end of the ARN. Be advised that this platform does not create the SNS topics nor add supscriptions and subscription validation. This should be done out side the Hive HCOM platform.  

    * __Webhook__  - if the feaure is enable above
        * Channel Friendly Name  
            *   Self explanatory. This is the name that will appear in Teams/Slack.  
        * Channel URL  
            *   Secure URL that will be used to send the message to the channel. Be advised the Hive HCOM platform does not create these channels. They should be created outside of the Hive HCOM platform. You will need appropriate administrator permissions within those systems to create channels.  

    * __Backup__  
        * If the AWS Backup feature is enabled above, this field specifies when the Most Recent Backup (in hours) should exist when the daily backup report runs.  

---
## __State Profile Management Widget__
__Overview__  
The purpose of this widget is to manage state profiles and their underlying configurations. To create a new profile, select __'New Profile'__ in the __'Select Profile #'__ drop-down after adjusting the other drop-downs to associate the new profile with the intended Tenant and AWS Account for Tenant. If the intended Tenant or AWS Account is not visible, you will need to add them first using Tenant Account Management Widget for adding Tenant, or AWS Account Management Widget for adding an existing AWS Account to manage.  

__General guidance__

1. __Default__ form - used to set filters for managing and creating existing State Profiles and Configurations within State Profiles.  
    *   Select from each dropdown successively proceeding from left to right or adjust as needed with the final click on __'Select Profile #'__ to reveal the __'Select Configuration form'__.  
    * Default form fields
        * Select Tenant - unique organizational unit  
        * Select Managed Account - will show all AWS Accounts added to selected Tenant. Accounts ending with __'Z'__ are not validate accounts yet and must be modified before used.  
        * Select Profile # - unique profile numbers are visible, __'new profile'__ option is listed here as well. 
        * New Profile - enables creation of a new State Profile
        * Remove All Configurations from Profile # (This only displays once you have selected a profile from the drop-down and clicked Select Profile #) - This will remove all current configurations and active state assocations. Essentially, get back to a clean slate. Profile is now ready to be reused.

2. __Select Configuration__ - form
    *   Select from the drop-down of existing configurations for this profile to modify. Format is: Configuration # | Configuration Name | Disabled. The Configuration name format is: ___HCOM-State-Profile-Profile#-Configuration#-Tenant-AWSAccount#-SSMRunDocument-TagType-Tag-Value___. Disabled configurations are visible with a __'| Disabled'__ on the right end of the Configuration Name in the drop-down. Disabled configurations have no current 'State Association' enforcing them. When you Enable a configuration it creates a Systems Manager State Association in the designated Tenant AWS Account and region to begin enforcing the configuration at the specified interval and resource targets.  Click __'Modify Configuration'__ to load the Modify Configuration form.

3. __Add an additional configuration to this profile__ - form to create a new configuration
    *   There are 2 steps to creating a Configuration. This form represent Step 1.
    *   In Step 1 the Tenant and AWS Account values are inherited from the default form so these values are automatically pulled from there.
    *   Editable fields
        *   __Select AWS Systems Manager (SSM) RunDocs__ - these are all the supported standard RunDocs for managing configuration and patching. The format in the drop-down is: ___SSMRunDoc | Description for RunDoc___  The RunDoc is the automation that will run for managing State or Patching.
        *   __Select Region__ - select which region a State Assocation will be created when enabled to invoke the RunDoc on specified schedule. The schedule is specified in Step 2 - Modify Configuration.  
        *   __Target Key Type__ - sets what will be used to determine the target workloads. Default is tag on workload. Other options while visible are not fully tested and working.   
        *   __Target Name__ - corresponds to Target type, if tag type then this is the tag name. Any Tag name on the workload can be used.    
        *   __Target Value__ - corresponds to Target Name, if tag type then this is tag value  
        *   __Add Configuration__ button will complete Step 1 and proceed to Step 2 - Modify Configuration  

4. __Modify Configuration__ - form to modify an existing configuration  
    *  This form contains a mix of read only fields and editable fields.  
    * __Editable Fields:__  
        *   __SSM Compliance Severity__ - drop-down allows you select what level to report to SSM Compliance if the SSM Run Doc fails.
        *   __Schedule__ - enter in rate or cron format. This controls how often the specified SSM RunDoc is executed against the specified targets.  
        *   __Target Type__ - sets what will be used to determine the target workloads. Default is tag on workload. Other options while visible are not fully tested and working.  
        *   __Target Name__ - corresponds to Target type, if tag type then this is the tag name  
        *   __Target Value__ - corresponds to Target Name, if tag type then this is tag value  
        *   __Parameers (JSON)__ - these parameters are based on the SSM RunDoc being used and pass values at runtime to the RunDoc and control behavior of automation.  
        *   __Is Enabled?__ - If enabled will create SSM State Association to begin enforcing configuration based on schedule. If disabled, will delete SSM State Association.   
        *   __Change Calendar__ - Allows you to utilize a SSM Change Calendar with this configuration and State Association. The RunDoc will adhere to what the linked Change Calendar settings are to allow or block changes.  Be advised, the Hive HCOM platform does not allow you to create or manage Change Calendars but merely allows you to utilize them if they exist. Hive HCOM will create a default State calendar __(HCOM-State-Calendar)__ that always allows changes unless you modify. It also creates a default Patch calendar __(HCOM-Patch-Calendar)__ that always denies changes unless you add events to the calendar for when patches are allowed to be applied. See AWS document on SSM Change calendars for how they work.  
        *   __Apply only at Cron Interval?__ - if enabling configuration or configuration is already enabled and you are just making a change, controls if the RunDoc is immediately invoked or will wait to run according the specified schedule. False, will invoke on Update, True, will wait for schedule to run.  
        *   __Update Button__ - will save changes to configuraion. Any existing SSM State Associations (if Enabled) will get deleted and recreated, or if it was disabled but now enabled will create SSM State Association. State Associations are only created in the specified AWS Account and Region.    
        *   __Delete Button__ - will delete configuration and any SSM State Associations  
      
---
## __CloudWatch Profiles Widget__  
__Overview__  

The purpose of this widget is to manage CloudWatch profiles for all supported service types which is currently: EC2 / external workload (OnPrem or other CSP like Azure/GCP - must support running AWS SSM and CloudWatch Agents), RDS, DynamoDB (containers is not currently supported). Profiles are simply a collection of alarms with each alarm monitoring a specific metric for a specific threthold. When thresholds are breached, alarm actions can trigger SNS topics for email, alarm actions for creating Systems Manager OpsItems, and trigger response automations (Lambda, SSM Automation, SSM RunDocs).  Monitroing Custom metrics are supported.  

__General Guidance__  
    
1.  __Default Form__ - There for 4 potential rows of filters and options 
    *   Rows 1 - 3  Select from each dropdown successively proceeding from left to right or rows (1) and (2) as needed with the final click on __'Select Profile '__ to reveal the __'(3) Profile Options and detailed view of the selected profile.'__.  
    *   Row 4 for Alarm Creation will show up after an editable profile has been selected (Profile numbers higher than 10)
    *   __Select Service__ - Choose the Service type to filter for profiles  
    *   __Select Profile__ - Choose the profile number to view  (numbers lower than 10 are manage templates and can only be copied and not directly edited)
    *   __Duplicate Profile__  - This button allows you to create a new profile by copying the currently selected and visible profile  
    *   __Delete Profile__  - This button allows you to delete the selected profile. Deletion is permanent and is not reversible.
    *   __Update Region__ - This button allows you to update the target/supported regions for the editable profile. This makes it available for use in the account/region. Saving changes will automatically generate a new CloudWatch Agent Configuration and store it in the SSM Parameter store for the account/region.
    *   __Create Alarms__ - This section of buttons are for initiating the automated alarm creation based on all the previously selected fields. Each button provides a specific target scope and functionality.
        *   __All Regions & push CWAgent config__ - This button will initiate automatic discovery and alarm create for all regions and push the latest CW Agent configuration to each target instance.
            *   Smaller buttons below previous button - Same as previous but only for the listed region
        *   __All Regions__ - This button will initiate automatic discovery and alarm creation for all regions but will not push the latest CW Agent configuration to each target instance. This method is faster and recommended when changes to the profile do not require changes to the CW Agent configuration.
            *   Smaller buttons below previous button - Same as previous but only for the listed region
        *   __i-insertinstanceid__ - Use this field when you only want to create/recreate alarms for a single instance. Replace with the Instance Id
            *   The following buttons to the right are submit buttons for the single instance field. One will push the CW Agent configuration, the only will only create alarms.

2.  __View Profile Details__ - form for viewing a summary of all alarms for the selected profile. Each row represents one alarm (alarm id)  
    *   __Modify__ - click to modify the settings for the alarm depicted in that row.  

3.  __Update__ - form to modify alarm  
    *   __Update__ - button to click after making changes to alarm settings for that row.  
    *   __Metric Name | Namespace__ - provides the metric name and the namespace for the metric  
    *   __Process Name__ - only applicable for process metric  
    *   __Alarm Options__ - only applicable for certain metrics  
    *   __Threshold__ - sets the threshold for the metric value being monitored  
    *   __Operator__ - the key operator that specifies how threshold values are evaluated  
    *   __Data Points__ - sets number of polling periods within eval periods to determine when to trigger alarms (should be less than Eval Periods)  
    *   __Eval Periods__ - sets the total number of polling periods to consider when evaluating alarm status  
    *   __Polling Period__ - sets the polling period or interval for getting an updated metric value  
    *   __Treat Missing Data__ - when metric data is not received (for example when EC2 is shutdown), should it trigger an alarm or ignore, etc.  
    *   __OpsItem Category__ - If OpsItem feature is enabled, what category to report if this alarm is triggered  
    *   __OpsItem Severity__ - If OpsItem feature is enabled, what severity to report if this alarm is triggered  
    *   __Action Type__ - specifies what type of automation to invoke in response to triggered alarm. Options are: [Lambda, SSM-Rundoc,SSM-Runbook, State Profile, Stop, Reboot, Terminate]  
    *   __Action Name__ - specifies the name of the action type. For example if Action Type is SSM-RunDoc, this is the RunDoc name visible in SSM Documents & Run Command
    *   __Action Settings__ - specifies parameters to pass with SSM Rundoc. For Lambda or SSM Automation the full alarm 'event' parameters are passed.  
    *   __Delete__ - button deletes the current (row/alarm id) alarm  
    *   __Modify__ - click to modify the settings for the alarm depicted in that row.  
    *   __Duplicate__ - will create a new alarm (alarm id) row and copy this row's settings  

---
## __AWS SSM Run Documents__  
__Overview__  
This section provides details on the supported SSM RunDocs that are the heart of SSM State Manager and used within Hive HCOM State Profile Configurations.
__General Guidance__  

The following table provides common AWS SSM Run Documents to consider for managing state and patching and the corresponding parameters to use when creating or updating profiles.  

---
<table>
    <tr><th>SSM Run Documents</th><th>Parameters</th><th>Description</th><th>Link</th></tr>
    <tr><td>AWS-ApplyAnsiblePlaybooks</td><td><p style="font-family:'Lucida Console', monospace">{'TimeoutSeconds': ['180'], 'ExtraVariables': [''], 'SourceInfo': [''], 'Verbose': ['-v'], 'SourceType': ['S3'], 'PlaybookFile': ['playbooks/ansible/configuration.yml'], 'Check': ['True'], 'InstallDependencies': ['True']}</p></td><td>Run Ansible Playbooks on Systems Manager managed instances w/Ansible agent installed.</td><td><a href="https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-state-manager-ansible.html" target=_new>AWS Docs</a></td></tr>
    <tr><td>AWS-ApplyChefRecipes</td><td><p style="font-family:'Lucida Console', monospace">{'ChefClientVersion': ['14'], 'RunList': ['recipe[apply-chef-recipes-example-cookbook::default]'], 'SourceInfo': ['s3://bucket/templates/configuration.tar.gz'], 'WhyRun': ['False'], 'ComplianceType': ['Custom:Chef'], 'SourceType': ['S3'], 'ComplianceSeverity': ['None'], 'JsonAttributesContent': [''], 'ComplianceReportBucket': [''], 'ChefClientArguments': ['']}</p></td><td>Run Chef recipes on AWS Systems Manager managed instances</td><td><a href="" target=_new>AWS Docs</a></td></tr>
    <tr><td>AWS-InstallPowerShellModule</td><td><p style="font-family:'Lucida Console', monospace">{}</p></td><td></td><td><a href="" target=_new>AWS Docs</a></td></tr>
    <tr><td>AWS-InstallApplication</td><td><p style="font-family:'Lucida Console', monospace">{}</p></td><td></td><td><a href="https://github.com/awsdocs/aws-systems-manager-user-guide/blob/main/doc_source/walkthrough-powershell.md" target=_new>AWS Docs</a></td></tr>
    <tr><td>AWS-InstallWindowsUpdates</td><td><p style="font-family:'Lucida Console', monospace">{}</p></td><td></td><td><a href="" target=_new>AWS Docs</a></td></tr>
    <tr><td>AWS-JoinDirectoryServiceDomain</td><td><p style="font-family:'Lucida Console', monospace">{}</p></td><td></td><td><a href="https://github.com/awsdocs/aws-systems-manager-user-guide/blob/main/doc_source/walkthrough-powershell.md" target=_new>AWS Docs</a></td></tr>
    <tr><td>AWS-RunSaltState</td><td><p style="font-family:'Lucida Console', monospace">{'state': [''], 'stateurl': ['s3://bucket/templates/configuration.sls'], 'test': ['False']}</p></td><td>Run Salt States on Systems Manager managed instances w/Salt Agent installed.</td><td><a href="https://aws.amazon.com/blogs/mt/running-salt-states-using-amazon-ec2-systems-manager/" target=_new>AWS Docs</a></td></tr>
    <tr><td>AWSEC2-ConfigureSTIG</td><td><p style="font-family:'Lucida Console', monospace">{'Level': ['Low']}</p></td><td>Pushes configuration hardening standards created by DISA to secure systems and software.</td><td><a href="https://docs.aws.amazon.com/systems-manager-automation-runbooks/latest/userguide/awsec2-configurestig.html" target=_new>AWS Docs</a></td></tr>
    <tr><td>AWS-RunPatchBaseline</td><td><p style="font-family:'Lucida Console', monospace">{'Operation': ['Scan'], 'RebootOption': ['RebootIfNeeded']}</p></td><td></td><td><a href="" target=_new>AWS Docs</a></td></tr>
    <tr><td>AWS-RunPatchBaselineWithHooks</td><td><p style="font-family:'Lucida Console', monospace">{'PreInstallHookDocName': [''], 'PostInstallHookDocName': [''], 'SnapshotId': [''], 'Operation': ['Scan'], 'RebootOption': ['RebootIfNeeded'], 'OnExitHookDocName': ['']}</p></td><td>Wrapper to compose more complex patch install scenarios for Systems Manager managed instances</td><td><a href="https://docs.aws.amazon.com/systems-manager/latest/userguide/patch-manager-aws-runpatchbaselinewithhooks.html" target=_new>AWS Docs</a></td></tr>
    <tr><td>AWS-RunPatchBaselineAssociation</td><td><p style="font-family:'Lucida Console', monospace">{'Operation': ['Scan'], 'BaselineTags': [''], 'RebootOption': ['RebootIfNeeded'], 'AssociationId': [''], 'InstallOverrideList': ['']}</p></td><td>Scans for or installs patches from a patch baseline to Linux or Windows Systems Manager managed instances</td><td><a href="https://docs.aws.amazon.com/systems-manager/latest/userguide/patch-manager-aws-runpatchbaselineassociation.html" target=_new>AWS Docs</a></td></tr>
    <tr><td>AWS-RunPowerShellScript</td><td><p style="font-family:'Lucida Console', monospace">{'workingDirectory': [''], 'executionTimeout': ['3600'], 'commands': ['command1,command2']}</p></td><td>Run a PowerShell script or specify the paths to scripts to run.</td><td><a href="" target=_new>AWS Docs</a></td></tr>
    <tr><td>AWS-RunRemoteScript</td><td><p style="font-family:'Lucida Console', monospace">{'executionTimeout': ['3600'], 'SourceInfo': [''], 'commandLine': [''], 'workingDirectory': [''], 'SourceType': ['S3']}</p></td><td>Execute scripts stored in a remote location.</td><td><a href="" target=_new>AWS Docs</a></td></tr>
    <tr><td>AWS-ConfigureAWSPackage</td><td><p style="font-family:'Lucida Console', monospace">{'name': [''], 'action': ['Install'], 'installationType': ['Uninstall and reinstall'], 'additionalArguments': [''], 'version': ['']}</p></td><td>Install or uninstall a Distributor package. You can install the latest version, default version, or a version of the package.</td><td><a href="https://docs.aws.amazon.com/systems-manager/latest/userguide/distributor-working-with-packages-deploy.html" target=_new>AWS Docs</a></td></tr>
    <tr><td>AWS-ConfigureCloudWatch</td><td><p style="font-family:'Lucida Console', monospace">{'properties': [''], 'status': ['Enabled']}</p></td><td>Export metrics and log files from your instances to Amazon CloudWatch</td><td><a href="https://github.com/awsdocs/aws-systems-manager-user-guide/blob/main/doc_source/walkthrough-powershell.md" target=_new>AWS Docs</a></td></tr>
    <tr><td>AWS-ConfigureWindowsUpdate</td><td><p style="font-family:'Lucida Console', monospace">{}</p></td><td></td><td><a href="https://github.com/awsdocs/aws-systems-manager-user-guide/blob/main/doc_source/walkthrough-powershell.md" target=_new>AWS Docs</a></td></tr>
    <tr><td>AWS-RunDocument</td><td><p style="font-family:'Lucida Console', monospace">{'SourceInfo': [''], 'documentParameters': [''], 'SourceType': ['SSMDocument']}</p></td><td>Execute composite or nested Systems Manager documents (SSM documents) stored in a remote location.</td><td><a href="https://docs.aws.amazon.com/systems-manager/latest/userguide/documents-running-remote-github-s3.html" target=_new>AWS Docs</a></td></tr>
    <tr><td>AWS-RunInspecChecks</td><td><p style="font-family:'Lucida Console', monospace">{'sourceType': ['S3'], 'sourceInfo': ['{}']}</p></td><td>Run a single InSpec test or an InSpec profile on a group of managed instances.</td><td><a href="https://docs.aws.amazon.com/systems-manager/latest/userguide/integration-chef-inspec.html" target=_new>AWS Docs</a></td></tr>
    <tr><td>AWS-UpdateEC2Config</td><td><p style="font-family:'Lucida Console', monospace">{}</p></td><td></td><td><a href="" target=_new>AWS Docs</a></td></tr>
</table>