## Setup On-Prem Server

Steps:
1. Install Admin Center https://www.microsoft.com/en-us/evalcenter/evaluate-windows-admin-center
2. run the powershell script to install SSM Agent and AWS CLI
3. create activation: aws ssm create-activation --description MSP-OnPrem-Linux-2 --default-instance-name MSP-OnPrem-Linux-2 --iam-role HCOM-AmazonEC2RunCommandRoleForManagedInstances --tags Key=CloudWatch-Profile,Value=3 
4. register on-prem instance (if reinstalling, make sure you uninstall the SSM agent first or it won't work): 
    ### Use the code below to install and register instance for Windows. replace code, id, region to match activation
    $code = "replace with code"
    $id = "replace with id"
    $region = "us-gov-east-1"
    $dir = $env:TEMP + "\ssm"
    New-Item -ItemType directory -Path $dir -Force
    cd $dir
    (New-Object System.Net.WebClient).DownloadFile("https://amazon-ssm-$region.s3.$region.amazonaws.com/latest/windows_amd64/AmazonSSMAgentSetup.exe", $dir + "\AmazonSSMAgentSetup.exe")
    Start-Process .\AmazonSSMAgentSetup.exe -ArgumentList @("/q", "/log", "install.log", "CODE=$code", "ID=$id", "REGION=$region") -Wait
5. use SSM run doc to install CloudWatch Agent
6. configure Cloudwatch Agent using c:\Program Files\Amazon\AmazonCloudWatchAgent\amazon-cloudwatch-agent-config-wizard.exe (make sure you select OnPremise option, push to SSM Parameter store, then modify and add mi- to dimensions)
7. make sure c:\users\Administrator\.aws\config and credentials is setup with AmazonCloudWatchAgent sections
8. make sure C:\ProgramData\Amazon\AmazonCloudWatchAgent\common-config.toml is modified as follows (uncomment and update this enable SSM run doc to work):
    [credentials]
    shared_credential_profile = "AmazonCloudWatchAgent"
    shared_credential_file = "C:\\Users\\Administrator\\.aws\\credentials"
8. use command line to start and reference new configuration from ssm: 
    > & "c:\Program Files\Amazon\AmazonCloudWatchAgent\amazon-cloudwatch-agent-ctl.ps1 -a fetch-config -m onPremise -s -c ssm:CloudWatch-Profile-7"

Setting up startup and shutdown scripts in SSM OnPrem RunDoc
Sources:
* https://devblogs.microsoft.com/scripting/use-powershell-to-create-job-that-runs-at-startup/#:~:text=A%20few%20steps%20are%20required%20to%20create%20a,and%20the%20full%20path%20to%20the%20startup%20script.
* https://social.technet.microsoft.com/wiki/contents/articles/7703.powershell-running-executables.aspx
* https://learn.microsoft.com/en-us/powershell/module/psscheduledjob/new-jobtrigger?view=powershell-5.1
Steps to enable Start and Stop AWS Custom Events:
1. add GPO at: Computer Configuration -> Windows Settings -> Scripts(Startup/Shutdown) -> Startup, add item to execute PowerShell script located at C:\ProgramData\Amazon\SSM\Download\start.ps1
2. add GPO at: Computer Configuration -> Windows Settings -> Scripts(Startup/Shutdown) -> Shutdown, add item to execute PowerShell script located at C:\ProgramData\Amazon\SSM\Download\stop.ps1

NOTE: 
    * push from SSM run doc does not seem to work and may have something to do with user identity it uses but from Administrator login can invoke from command line to start with push from SSM param for configuration. Reboot of servers shows it starts with correct profile and pushes metrics.
    * you cannot use the ${aws:InstanceId} to dynamically populate InstanceId in CloudWatch Agent Configuration for append_dimensions. This only works for on-Prem instances

** Future Feature:
    * copy AmazonCloudWatch-ManageAgent to HCOM-AmazonCloudWatch-ManageAgent and modify to dynamically capture managed instance ID and replaces it in CW agent configuration file so you don't need a separate CW config file for every on-prem instance. This is required for append_dimensions feature to include instance ID with every metric.

