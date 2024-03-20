## __Platform Developers Guide__

* __Description__
This page provides guidance to platform developers and operations teams.
### Sections
* CloudFormation Templates
* How to add AWS service monitoring


## __CloudFormation Templates__

### __MSP-AWS-Ops-Mgmt-Tenant-Implementation.yaml__
Deployed on each AWS account to be managed by the platform. See installation documentation for further details.
<table>
<tr><th>CF Resource</th><th>Type</th><th>AWS Resource Name</th><th>Purpose</th></tr>
<tr><td>CentralAccount</td><td>String</td><td>CF Input Parameter</td><td>AWS Account Number for the central managmeent account</td></tr>
<tr><td>CentralRegion</td><td>String</td><td>CF Input Parameter</td><td>AWS Region that corresponds with the central managmeent account</td></tr>
</table>

### How to add new AWS Service monitoring to CloudWatch Feature
The following are steps and files that need to be updated when adding support for an additional AWS service to the CloudWatch monitoring feature within Hive.
1. Define namespace, metrics and profiles 
    * Modify DynamoDB records at msptype = cw-profiles, mspname=types (review existing data model to match)
        * Add new top level entry (under resouretypes) for service type, service-profile.
        * Add service name and namespaces, followed by profile types and then metrics
    * Update DynamoDB import file (platform-configuration.json) with changes to above record
2. Create one or more initial profiles
    * Update (platform-configuration.json) with profiles
    * Use json to create profiles in DynamoDB
3. Update GUI to manage profiles
    * Update CloudWatch Widget settings if needed in MSP-Platform-Configuration-Manager
    * Update MSP-CloudWatch-Dashboard-Widget Function which provides initial front-end widget GUI along with profile duplication
    * Update MSP-Platform-Configuration-Manager Function which provides Modify/Update/Duplicate for individual alarm rows within profile
        * Enable individual row modifications: update, row duplication and deletion
4. Update CloudWatch Alarm Creator Function
    * Update MSP-CloudWatch-Alarm-Creator (Mode = 3) and cw.py shared library
        * Update get_alarm_prefix function to creating the alarm name prefix
        * Single instance update
        * Tag/Value target instance update
5. Update CloudWatch Lifecycle Automation Function
    * Update to enable auto creation and deletion when Lambda functions are created and deleted.


### RDS CloudWatch Monitoring Lifecycle Management
The standard profile based approach is used for RDS. Profiles are defined for what metrics and thresholds to monitor for. EventBridge rules detect RDS resource state changes and based on tags for CloudWatch Profile number, will automatically create, supporess or delete CloudWatch Alarms.

* EventBridge Rule settings
    *  DB Instance events:
        * RDS-EVENT-0003 - DB instance deleted
        * RDS-EVENT-0005 - DB instance created
        * RDS-EVENT-0087 - DB instance has been stopped
        * RDS-EVENT-0088 - DB instance has been started

## __HCOM Automations Create EventBridge Events___
Core features of the platform generate EventBridge events so you can detect when automations have completed. The following provides the JSON based Event patterns.

1. CloudWatch Alarm Lifecycle events
    *   Alarms
        *   JSON is defined in DynamoDB as msptype = cw-event, mspname = alarms
        *   'DetailType' = 'CloudWatch Alarm-State-Change-Notification'
        *   'state' possible values: created, suppressed, deleted
        *   'Source' = 'hcom.alarm'
    *   OnPrem
        *   JSON is defined in DynamoDB as msptype = cw-event, mspname = onprem
        *   'DetailType' = 'OnPrem-Instance-State-Change-Notification'
        *   'state' possible values: running, stopped
        *   'Source' = 'hcom.onprem'
    *   Account
        *   JSON is defined in DynamoDB as msptype = cw-event, mspname = account
        *   'DetailType' = 'Tenant Account-State-Change-Notification'
        *   'Detail' contains the following strings: account, account-name, tenant, state
        *   'state' possible values: updated
        *   'Source' = 'hcom.account'
    *   Tenant
        *   JSON is defined in DynamoDB as msptype = cw-event, mspname = tenant
        *   'DetailType' = 'Tenant State-Change-Notification'
        *   'Detail' contains the following strings: tenant, state
        *   'state' possible values: added
        *   'Source' = 'hcom.tenant'
    *   KMS
        *   JSON is defined in DynamoDB as msptype = cw-event, mspname = kms
        *   'DetailType' = 'Key Policy Configuration-Update-Change-Notification'
        *   'Detail' contains the following strings: account, key-id, region, state
        *   'state' possible values: updated
        *   'Source' = 'hcom.kms'