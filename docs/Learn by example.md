# Learn By Example - A Quick Guide to Learn ITF

## Create new test case with test identity
Most of the test cases you will use will be based on an identity. So it's a good idea to start by creating a new test 
case that sets up a test identity. Depending on what kind of test case you are creating, there are few things to consider:

- Will the identity id be used?
- Are the roles and role assignments important to you test case?
- What data cleaning is required?

To create new test case perform the following steps:

1. You can create new test case file manually or use the plugin to [create it for you here](IntelliJFunc.md#new-itf-test-case).
2. Select and add test identity to the test case. See [here](IntelliJFunc.md#insert-test-identity). Depending on you test requirement include required information from the identity.

Finishing these steps will give you a test case with a test identity that you can use to build your test case.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE TestCase PUBLIC "-//amidentity//DTD TestCase 2.0//EN" 
        "http://www.amidentity.com/dtd/AutomatedTestingXMLSchema20.dtd">
<TestCase deleteObjects="false" removeIds="false">
    <TestCaseName>My new test case</TestCaseName>
    <TestIdentity>Angela.Bell</TestIdentity>
    <TestCommands>

    </TestCommands>
</TestCase>
```
### Test identity with id
Sometimes in your test case you need a test identity that will always have the same id (for example when testing quick links with identity selection).
In this case, the xml file holding the identity needs to contain the id of the identity, and you need to set `removeIds="true"` in the test case.
But there is more. When uploading identity xml in to IIQ event if you set `removeIds="true"` if the identity already exists in the system with different id,
it will not be overwritten. So the solution is to delete the identity before uploading the new one. 
But in this case you will not be setting test identity directly on the test case.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE TestCase PUBLIC "-//amidentity//DTD TestCase 2.0//EN" 
        "http://www.amidentity.com/dtd/AutomatedTestingXMLSchema20.dtd">
<TestCase deleteObjects="false" removeIds="false">
    <TestCaseName>My new test case</TestCaseName>
    <TestCommands>

        <CleanObject objectClass="Identity">
            <NamePrefix>Angela.Bell</NamePrefix>
        </CleanObject>
        
        <LoadObjects>
            <ObjectsList>
                <Object>Angela.Bell.xml</Object>
            </ObjectsList>
        </LoadObjects>
        

    </TestCommands>
</TestCase>
```
### Data cleaning
Loading an identity may not be enough to prepare for running the test case. You may have to clean up some data.
Depending on your test case requirement you may have to use `Clean` command with some attribute set.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE TestCase PUBLIC "-//amidentity//DTD TestCase 2.0//EN" 
        "http://www.amidentity.com/dtd/AutomatedTestingXMLSchema20.dtd">
<TestCase deleteObjects="false" removeIds="true">
    <TestCaseName>My new test case</TestCaseName>
    <TestIdentity>Angela.Bell</TestIdentity>
    <TestCommands>

        <Clean workflowCase="true"
               deleteAccessRequest="true"
               identityEntitlements="true"
               auditEvent="true"
               emails="true"
               provisioningRequest="true">
            <IdentityName>Angela.Bell</IdentityName>
        </Clean>

    </TestCommands>
</TestCase>
```

### Role assignments in test identity
In your test case you may need the identity to have some roles assigned. IIQ implementation relies on role assignments to use ids of the roles, not the names.
This may present a bit of the problem when loading test identity xml. 
You may include role assignments in the identity xml, but you will have to match ids to existing roles int the system. 
this may prove difficult especially when you want your test cases to be portable between various environments (local, integration, test).

The best way to handle this is to use `Assign` command to assign roles to the test identity right at the beginning.
Your test identity xml will include all the links with required entitlements and with the `Assign` command you will assign roles to the identity.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE TestCase PUBLIC "-//amidentity//DTD TestCase 2.0//EN" 
        "http://www.amidentity.com/dtd/AutomatedTestingXMLSchema20.dtd">
<TestCase deleteObjects="false" removeIds="true">
    <TestCaseName>My new test case</TestCaseName>
    <TestIdentity>Angela.Bell</TestIdentity>
    <TestCommands>

        <Assign>
            <IdentityName>Angela.Bell</IdentityName>
            <Bundle>Employee</Bundle>
        </Assign>

    </TestCommands>
</TestCase>
```
You can skip `<IdentityName>` element when you specify `<TestIdentity>`.
 
## Make an LCM request for a role
Making a request for a role in IIQ is technically equivalent to starting an LCM workflow with provisioning plan that 
contains a role request and few other parameters.

Let's create an ITF test case that will request a role `Employee` for a user `Angela.Bell` by herself. 
Basic provisioning plan for such request will look like this:
```xml
<ProvisioningPlan>
    <AccountRequest application="IIQ" op="Modify">
        <AttributeRequest name="assignedRoles" op="Add" value="Employee"/>
    </AccountRequest>
</ProvisioningPlan>
```
Hence, the ITF test case to make that request will look like this (Assuming you are using OOTB LCM workflow):
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE TestCase PUBLIC "-//amidentity//DTD TestCase 2.0//EN" 
        "http://www.amidentity.com/dtd/AutomatedTestingXMLSchema20.dtd">
<TestCase deleteObjects="false" removeIds="true">
    <TestCaseName>Sef request for employee role</TestCaseName>
    <TestIdentity>Angela.Bell</TestIdentity>
    <TestCommands>
        <RunWfl workflowName="LCM Provisioning">
            <ProvisioningPlan>
                <AccountRequest application="IIQ" op="Modify">
                    <AttributeRequest name="assignedRoles" op="Add" value="Employee"/>
                </AccountRequest>
            </ProvisioningPlan>
        </RunWfl>
    </TestCommands>
</TestCase>
```
This will work but when you make a request using GUI, IdentityIQ adds some additional parameters to the workflow case.
So the fastest and most precise way to create a test case for a role request is to use the plugin's functionality to generate
`RunWfl` command from the real request.

To do that you need to:

1. Create new test case with `Angela.Bell` as test identity. See [here](#new-itf-test-case)
2. Login in to IdentityIQ as `Angela.Bell` and make a request for an `Employee` role. 
3. Generate `RunWfl` command from the existing request. See [insert account request](IntelliJFunc.md#insert-account-request)

This will generate a `RunWfl` command with all the variables as if you made the request from the GUI:
```xml
        <RunWfl asynchronousDelay="0" compilePlan="false" startAsynchronously="false" targetClass="Identity"
                targetName="Angela.Bell" updateRoleIdsInAccountRequests="false"
                workflowCaseName="Update Identity Angela.Bell AccessRequest" workflowName="LCM Provisioning">
            <Plan identityName="Angela.Bell">
                <AccountRequest application="IIQ" op="Modify">
                    <Attributes>
                        <entry key="attachments"/>
                        <entry key="interface" value="LCM"/>
                        <entry key="attachmentConfigList"/>
                        <entry key="operation" value="RoleAdd"/>
                        <entry key="flow" value="AccessRequest"/>
                    </Attributes>
                    <AttributeRequest assignmentId="6efaa6167da24e689ac971eaa0867802" name="assignedRoles" op="Add"
                                      value="Employee"/>
                </AccountRequest>
                <ProvisioningTargets>
                    <ProvisioningTarget assignmentId="6efaa6167da24e689ac971eaa0867802" role="Employee">
                        <AccountSelection applicationName="LDAP" doCreate="true" implicitCreate="false"/>
                    </ProvisioningTarget>
                </ProvisioningTargets>
                <Requesters>
                    <Reference class="sailpoint.object.Identity" name="Angela.Bell"/>
                </Requesters>
            </Plan>
            <Attributes>
                <entry key="enableRetryRequest" value="false"/>
                <entry key="fallbackApprover" value="spadmin"/>
                <entry key="endOnManualWorkItems" value="false"/>
                <entry key="sessionOwner" value="Angela.Bell"/>
                <entry key="userEmailTemplate" value="LCM User Notification"/>
                <entry key="source" value="LCM"/>
                <entry key="identityDisplayName" value="Angela.Bell"/>
                <entry key="saveUnmanagedPlan_WithProjectArgument" value="false"/>
                <entry key="flow" value="AccessRequest"/>
                <entry key="identityName" value="Angela.Bell"/>
                <entry key="filterRejects" value="true"/>
                <entry key="requireCommentsForDenial" value="false"/>
                <entry key="requesterEmailTemplate" value="LCM Requester Notification"/>
                <entry key="approvalEmailTemplate" value="LCM Identity Update Approval"/>
                <entry key="managerEmailTemplate" value="LCM Manager Notification"/>
                <entry key="launcher" value="Angela.Bell"/>
                <entry key="approvalScheme" value="manager,owner"/>
                <entry key="approvalMode" value="serial"/>
                <entry key="endOnProvisioningForms" value="false"/>
                <entry key="requireCommentsForApproval" value="false"/>
                <entry key="notificationScheme" value="user,requester"/>
                <entry key="policyScheme" value="continue"/>
                <entry key="setPreviousApprovalDecisions" value="false"/>
            </Attributes>
        </RunWfl>
```

## Approve a role request

TBD

## Fill out and validate custom form

TBD

## Trigger an event (leaver)

TBD

## Run workflow from Quicklink

TBD

## Simulating provisioning or not

TBD