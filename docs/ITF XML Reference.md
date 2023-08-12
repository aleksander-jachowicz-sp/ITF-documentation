
# ITF XML Reference

* * *

## ITF test case <TestCase>

The content of ITF test case is represented by XML file.

```xml
<?xml version='1.0' encoding='UTF-8'?>
<!DOCTYPE TestCase PUBLIC "-//amidentity//DTD TestCase 2.0//EN" 
            "http://www.amidentity.com/dtd/AutomatedTestingXMLSchema20.dtd">
<TestCase removeIds="true" deleteObjects="false">
	<TestCaseName>My first test case</TestCaseName>
	<Description>This is optional test case description</Description>
	<TestIdentity>John.Doe</TestIdentity>
	<ObjectsToLoad>
		<Object>RoleA</Object>
		<Object>ManagerZ</Object>
	</ObjectsToLoad>
	<TestCommands>
		...
	</TestCommands>
</TestCase>
```

the 1st and 2nd line represents the header that should be present in each test case file. It contains reference to the DTD configuration in your IDE.

`<TestCase>` - this is the main test case node. All test case content is placed inside this node.

`removeIds` - optional attribute. When set to false all xml files containing object in the test case will be loaded without removing any ids (if present) from them. The default value is true.

`deleteObjects` - optional attribute. When set to true, all objects loaded by ITF during the test case will be deleted from IIQ instance. This is very useful to keep your IdentityIQ instance clean from any test data.

`<TestCaseName>` - name of your test case.

`<Description>` - optional test case description.

`<TestIdentity>` - node to provide the file name (without extension) of the identity that is being used for the test. The identity name should match the file name.

`<ObjectsToLoad>` - list of the objects to be loaded before test case processing. The order of loading is the same as order in the test case. test identity is loaded after all object from `<ObjectsToLoad>` are imported.

`<TestCommands>` - place all you test commands in here ;-)

## ITF Commands

### Aggregate

Command to aggregate selected account(s) from target application. This command will perform actual aggregation from a target system in the same way IdentityIQ does.

This will only work with applications that support getObject. For example, it will work with LDAP or AD, but will not work for file base applications. For such aggregations consider using [mockedAggregate <MockedAggregate>](/wiki/spaces/AR/pages/45088773) command.

Some aggregation attributes are set to the following defaults promoteAttributes: true, correlateEntitlements: false, noOptimizeReaggregation: true. You can change them by adding attributes property.

```xml
<Aggregate logDisplayName="Real aggregation">
  <AccountsList>
    <Account applicationName="SAP" nativeIdentity="John.Doe"/>
  </AccountsList>
  <Attributes>
    <entry key="correlateEntitlements" value="true"/>
	<entry key="promoteManagedAttributes" value="true"/>
  </Attributes>
</Aggregate>
```

**Properties:**

`attributes` (optional) - List of attributes the will be passed to Aggregator class during execution of this command (refer to IdentityIQ documentation on aggregation for these attribute details). By default, these are set as follows:

* promoteAttributes: true,
* correlateEntitlements: false,
* noOptimizeReaggregation: true.

You can overwrite them if needed.

`accounts` - list of pairs (you can aggregate multiple accounts in one command). Each pair requires the name of the application and native identity of the account you want aggregated.

The success of this command relies on the proper data being present in the target system. Therefore, unless you are testing the aggregation itself, it may be better to use mockedAggregate command.

### CheckApprovals

Command to check and/or execute approvals in the workflow.

An important part of testing is validating workflows and approval steps. Approvals could be complex because there could be multiple of them in parallel and, at the same time, there could be different levels of approvals in serial mode. On top of that, approval worItems may be based on ootb renderer using approvalSet or a custom form. To make things even more complex, sometimes workItems may be transient, meaning that they cannot be found in database as they only live in memory.

If you need more information on how approvals, workItems, forms and approvalSets work in IdentityIQ, please review some of the IdentityIQ training materials. This will help you better understand the concepts of `approvals` command.

To better explain approvals command, I will use a simple example of approval workflow where we’ll have manager approval on the first level and then owner approval as a second level. Let’s imagine that 2 roles are being requested for an identity in serial mode. In this case, during the first level of approval, we will have one approval(workItem) with the person's manager being the owner. Once this is approved, the second level of approvals is generated. This time there will be two workItems, one for each role owner to approve.

Approvals command is constructed in a way that will allow to model this behavior. It is represented as a list of lists. Outer list consists of multiple `<ApprovalLevel>` elements. Inside each one of these there can by multiple `<Approval>` elements. Each `<Approval>` represents single `WorkItem`.

```xml
<CheckApprovals>
  <ApprovalLevel>
    <Approval> * </Approval>
  </ApprovalLevel>
  <ApprovalLevel>
    <Approval> * </Approval>
    <Approval> * </Approval>
  </ApprovalLevel>
</CheckApprovals>
```

**Evaluation order**

The order the approvals are being evaluated and acted on (if the decision is configured), as follows:

1. Command takes first `<ApprovalLevel>` element.
2. All approvals (`<Approval>`) from this lists are checked for existence (in order they appear on the list).
3. Each approval(`<Approval>`) is acted on when the decision is specified. Nothing happens when there is no decision specified. Watch out for parallel approvals, when only decision on one `WorkItem` cancels the other ones. In this case you may want to specify decision only for one approval.
4. The next list from `<ApprovalLevel>` element ) and back to #2.

**WorkItem types**

There are two, most common, types of `WorkItems` supported by ITF (for now): approvalSet based workItems and custom forms (please refer to IdentityIQ documentation or training for details).

##### ApprovalSet approvals

```xml
<CheckApprovals>
  <ApprovalLevel>
    <Approval>
      <ApprovalItems>
        <ApprovalItem applicationName="IdentityIQ"
                      attributeName="assignedRoles"
                      item="BusinessAnalyst"
                      operation="Add"
                      decision="deny" />
          <ApprovalItem applicationName="IdentityIQ"
                      attributeName="assignedRoles"
                      item="Designer"
                      operation="Add"
                      decision="approve" />
        </ApprovalItems>
        <Approver>Michael.Miller</Approver>
      </Approval>
  </ApprovalLevel>
</CheckApprovals>
```

This example shows expected approval based on approvalSet. `<Approver>` is the expected owner of the workItem and `<ApprovalItems>` is the list of approvalItems. In this case this is an approval of two roles being assigned.

`applicationName` - name of the application for which the approval item belongs. In case of roles they are part of IdentityIQ, and that literal should be used.

`operation` - operation on the approval item.

`attributeName` - name of the attribute for the item. For roles use `assignedRoles` literal.

`item` - value of the approval item being approved. In most cases this will be the name of the role or entitlement being approved.

`decision` - the approval decision you want the test case to perform. This can be `approve` or `deny`.

For approvals with approvalSet, the workItem will not be approved until there is a decision specified for each item. In the example above, Michael Miller approved role Designer and denies role Business Analyst.

Finally, to summarize the above information, the final approval command for the example scenario looks similar to this:

```xml
<CheckApprovals>
  <ApprovalLevel>
    <Approval>
      <Approver>Michael.Miller</Approver>
      <ApprovalItems>
        <ApprovalItem applicationName="IdentityIQ"
                      operation="Add"
                      attributeName="assignedRoles"
                      item="BusinessAnalyst"
                      decision="approve" />
        <ApprovalItem applicationName="IdentityIQ"
                      operation="Add"
                      attributeName="assignedRoles"
                      item="Designer"
                      decision="approve" />
      </ApprovalItems>
    </Approval>
  </ApprovalLevel>
  <ApprovalLevel>
    <Approval>
      <Approver>George.Wright</Approver>
      <ApprovalItems>
        <ApprovalItem applicationName="IdentityIQ"
                      operation="Add"
                      attributeName="assignedRoles"
                      item="BusinessAnalyst"
                      decision="approve" />
      </ApprovalItems>
    </Approval>
    <Approval>
      <Approver>Christopher.Clark</Approver>
      <ApprovalItems>
        <ApprovalItem applicationName="IdentityIQ"
                      operation="Add"
                      attributeName="assignedRoles"
                      item="Designer"
                      decision="approve" />
      </ApprovalItems>
    </Approval>
  </ApprovalLevel>
</CheckApprovals>
```

In case of simple one level one person approval, the list of lists will actually be reduced to one element lists like this:

##### Form approvals

These are the type of approval WorkItems that contain a custom form that needs to be filled out and submitted. The goals of ITF in this case is to detect that such workItem is generated for an owner, fill out the form with specified values, validate selected fields and submit the form to progress the workflow.

The `<ApprovalLevel>` list of lists structure stays the same. The difference is replacement of `<ApprovalItems>` with a`<Form>`.

```xml
<CheckApprovals>
  <ApprovalLevel>
    <Approval>
      <Approver>Betty.Young</Approver>
        <Form>
          <Field fieldName="Location">
            <FieldValue>Timbuktu</FieldValue>
          </Field>
          <Field fieldName="multiAttribute1">
            <FieldValue>value1,value2</FieldValue>
          </Field>
          <Field fieldName="CalculatedField" validateOnly="true">
            <FieldValue>London added postfix</FieldValue>
          </Field>
        </Form>
        <Decision>approve</Decision>
    </Approval>
  </ApprovalLevel>
</CheckApprovals>
```

There are no more `<ApprovalItems>` because the represent ApprovalSet, and there isn’t one in this case. The `<Form>` attribute represents the Form in the WorkItem. It’s an object whose properties are the expected names of the form fields and values are to be filled out in the Form.

There is one decision for the whole approval which represents pressing next or back button.

`<FieldValue>` element content is the value that will be used to fill out the field in the form(when `validateOnly` is false(the default)). When `validateOnly` is true value will be compared to field value after the form submission. This is designed to validate content of automatically generated fields. For multi valued fields use csv(coma separated values) format. When entered "null" literal expected value will be set to null. When it's left empty value will be and empty string. For checkbox use true, false values.

`validateOnly` - Boolean attribute of `<Field>` . When set to true ITF will not use this value to fill out the field but rather validate if the field contains this value after form submission. This is intended to be used to validate correctness of calculated fields. When set to false (default value), standard above described behavior is used.

`<Decision>` element represents which button on the form is clicked. Possible values are approve and deny. Value approve is equivalent to pressing next button and deny, the back button. There is no option for cancel button.

ITF supports custom forms that use models to propagate values between forms and workflow.

**Policy violations**

In ITF you can validate if there was policy violation triggered during the LCM request. Configuration is part of the `<Approval>`.

```xml
<Approval>
  <ApprovalItems>
    <ApprovalItem applicationName="IdentityIQ" attributeName="assignedRoles" item="Bank transfer approver" operation="Add"/>
  </ApprovalItems>
  <Approver>John.Williams</Approver>
  <Target>David.Anderson</Target>
  <PolicyViolation validateUnlistedPolicies="true">
    <PolicyName>Bank transfer SOD</PolicyName>
  </PolicyViolation>
</Approval>
```

`<PolicyViolation>` one or more `<PolicyName>` elements which represent expected policy violations generated when IIQ created the workfItem.

`validateUnlistedPolicies` - Boolean attribute(default false). When false ITF will only check if listed `<PolicyName>`'s policies were present on the approval. When set to true framework will also check if there are any policy violations on the approval which are not listed in the `<PolicyViolation>` element and if there are exceptions will be thrown. Using `<PolicyViolation`\> with `validateUnlistedPolicies="true"` and no `<PolicyName>` you can validate that there were no policies generated during the creation of the approval.(`<PolicyViolation validateUnlistedPolicies="true"/>`)

### Assign

Assign command allows assignment of links, entitlements, roles and workgroups. Command will assign specified object to the identity specified by TestIdentity. Depending on the type of object, direct api call(workgroup) or provisioning plan and `Provisioner`(link, entitlement, bundle) will be used.

```xml
<Assign logDisplayName="Assigning object to identity">>
  <IdentityName>John.Doe</IdentityName>
  <Bundle>BusinessAnalyst</Bundle>
  <Entitlement applicationName="LDAP" attributeName="groups" value="cn=BusinessAnalystGroup,ou=groups,dc=acme,dc=com"/>
  <Entitlement attributeName="memberOf" value="Programmers" applicationName="LDAP"/>
  <Link applicationName="LDAP" nativeIdentity="cn=AlexHayes,ou=Users,dc=acme,dc=com"/>
  <Link applicationName="LDAP" nativeIdentity="cn=AlexHayes Admin,ou=Users,dc=acme,dc=com"/>
  <Workgroup>Workers</Workgroup>
</Assign>
```
**Tags:**

`<IdentityName>` - Name of the identity to assign all the objects to. When [TestIdentity] is present in the test case and you want to assign objects to that identity, you can omit `<IdentityName>`.

`<Link>` - Link to be assigned to the identity. Can be used multiple times in `<Assign>` command to create multiple accounts.

`<Link>` attributes:

`applicationName` - Name of the application to assign. Mandatory.

`nativeIdentity` - Native Identity of the account that is being assigned.
When `nativeIdentity` is not present, ITF assumes that account creation(including account name creation) 
will be handled by applications create policy. Therefore, ensure that your application create policy is in place.

`<Bundle>` - role name to be assigned to identity. Can be used multiple times in `<Assign>` command.

`<Workgroup>` - workgroup to which identity should be added. Can be used multiple times in `<Assign>` command.

`<Entitlement>` - Entitlement to add to the identity. Attributes: `applicationName`, `attributeName`, `value`. Can be used multiple times in `<Assign>` command. If identity doesn’t have the account on the application it will be created. If there are multiple accounts in the application a random one will be used to assign the entitlement.

### CheckAudit

`checkAudit` command lets you validate audit events generated during execution of your test case. You can check content of each audit including skipping some of the attribute. Experience shows that this command is mostly useful when validating content of custom audit events.

`checkAudit` command works by changing each expected audit object in to query and searches for it with addition of checking that it was generated after the start of the test case. Because of that, even if `action` is the only required property, make sure that you provide enough other properties values so that the search makes sens and provides validations to you test case. For example, if you were to create an expected audit object with value only for action property, ITF would check if any audit event with such action was created since the beginning of the test case. If this is good enough for what you are validating, it's OK. But most of the time you would probably want to check the content of such event too.

```xml
<CheckAudit logDisplayName="Check audit">
	<AuditEvents>
		<Event>
			<Action>create_account</Action>
			<Source>Refresh</Source>
			<Target>John.Doe</Target>
			<Application>LDAP</Application>
			<AccountName>John</AccountName>
			<AttributeName>division</AttributeName>
			<AttributeValue>North-east</AttributeValue>
		</Event>
	</AuditEvents>
</CheckAudit>
```

**Tags:**

`AuditEvents` - Contains list of expected `Events`.

`Event` - single expected event.

`Event` tags:

`action` (mandatory), `source`, `target`, `application`, `accountName`, `attributeName`, `attributeValue` tags representing attributes of expected audit event.

### CheckEmail

Command to validate if email notification are being sent and to check their content.

When ITF plugin is installed, system configuration item `emailNotifierClass` is set to `com.itf.server.ITFEmailNotifier` which is a custom ITF notifier (for details on custom email notifiers please check [this](https://community.sailpoint.com/t5/Technical-White-Papers/Best-Practices-Email-Configuration/ta-p/75930#toc-hId-182469073) Compass article). When any email is being sent by IdentityIQ this class will store a copy of it and grant `<CheckEmail>` command access to those saved emails. To validate if desired email is sent, attributes to, subject and EmailBody are being check against the emails stored by custom `ITFEmailNotifier`.

For this command to work, your build must include IIQTester-simulator-x.x.jar provided in distribution zip file.

```xml
<CheckEmail subject="Changes requested to Albert.Woods need approval" 
            to="Patrick.Jenkins@demoexample.com" 
            timeOutSec="25" 
            compareMethod="contains">
  <EmailBody isRegex="false">Value(s): Programmer</EmailBody>
</CheckEmail>
```

`subject` (optional) - Value of the expected email’s subject of the notification sent by IdentityIQ.

`to` (optional) - To whom the expected notification is sent.

`compareMethod` - Possible values are contained(default) or equals. When a set to “contains” command will check if any of the subject, to and body attributes are contained in the expected email sent by IdentityIQ. When set to “equals”, command will check strict equality of the values. For `EmailBody` this setting will only be evaluated when `isRegex` attribute is set to false.

`timeOutSec` - (optional, default value 10 sec) IdentityIQ is sending emails asynchronously. Due to that, they may not be sent immediately. <CheckEmail> command will keep checking for `timeOutSec` number of seconds before failing. Of course if it finds the matching email before the timeout, it will result insuccess.

`EmailBody` (optional)- nested element that contains the text of the expected email body to validate.

`isRegex` Boolean (default false) When set to true `EmailBody` text is being evaluated against sent emails as regular expression.

### CheckExpiredItems

CheckExpiredItems command is equivalent to running “Check Expired Work Items” task. The difference is that ITF command will not create a task result.

There are no options.

```xml
<CheckExpiredItems/>
```

### CheckRequest

Command will search for a Request object matching configured criteria. If the request is not found command with throw `ITFTestingException`. If the request is found command will start checking if request is deleted until it’s gone or the timeout value `finishTimeoutSec` is reached.

```xml
<CheckRequest requestType="Workflow Request"
              regex="true"
              requestNamePrefix="[a-z]*case[a-z]*"
              finishTimeoutSec="0"
              throwWhenNotFound="true"
              throwWhenExistsAfterTimeout="false"/>
```

`requestType` Mandatory attribute that specifies the type of the request to search for. This value must match one of the values from name column of the spt\_request\_definition table from IdentityIQ DB schema. Of course you can also use custom request types.

`regex` boolean value that specifies if the request name search should be used using regular expression or simple prefix. Default value is false.

`requestNamePrefix` when `regex` is set to false this value is used to match request name as prefix. Otherwise request names are checked against this value as regular expression. Mandatory attribute.

`finishTimeoutSec` number of seconds (from the moment the request is found) ITF will keep checking if the request is gone. Default value is 30.

`throwWhenNotFound` when set to true ITF will throw exception if the request is not found. Otherwise ITF will just finish the command.

`throwWhenExistsAfterTimeout` when set to true and after the request is found and ITF already waited configured amount of time and the request is still present, ITF will thro exception. Otherwise ITF will just finish the command.

### Clean

Clean command. This command deletes various IdentityIQ objects depending on settings. This command is usually used at the very beginning or at the end of the test case. Its purpose is to clean up data before running a test, or clean up the system after the test has been completed. This command uses Terminator to delete objects.

It is important to have a clean environment before running the test so that the outcome is not dependent on residual data.

```xml
<Clean logDisplayName="Cleaning everything" 
      deleteAccessRequest="true" 
      workflowCase="true" 
      auditEvent="true" 
      identityItself="true" 
      provisioningRequest="true"
      emails="true">
	<IdentityName>John.Doe</IdentityName>
</Clean>
```

`Clean` attributes:

`deleteAccessRequest` - Boolean. When set to true, the command will delete all IdentityRequest objects for identity specified by `identityName`.

`workflowCase` - Boolean. When set to true, the command will delete all workflow cases which have identityName variable set to `identityName`.

`auditEvent` - Boolean. When set to true, the command will delete all audit events which target attribute equals to `identityName`.

`provisioningRequest` - Boolean. When set to true, the command will delete all ProvisioningRequest objects with identity set to `identityName`.

`identityItself` - Boolean. When set to true, the command will terminate identity specified by `identityName` attribute. When this is used, all objects related to identity will also be deleted by IdentityIQ (except audit events).

Warning. When testing aggregation (for example) you may not specify `testIdentity` in the test case, but you may still want to clean up before the test. In such case, make sure you do specify `identityName` for clean command.

`emails` - Boolean (default false). When set to true, clean command will remove all the emails which are stored for email validation. Review <CheckEmail> command for further details.

`Clean` tags

`IdentityName` - name of the identity you want to perform cleaning for. If test case has [TestIdentity] specified and you want to perform cleaning for that identity you can omit `IdentityName` tag.

### CleanObject

Command will delete any SailPointObject based on configured name matching.

Command will only work with objects that have name attribute

```xml
<CleanObject objectClass="Request">
  <NamePrefix isRegex="false">My case</NamePrefix>
</CleanObject>
```

`objectClass` - name of the SailPointObject class to delete. Mandatory.

`NamePrefix` - configuration used to find which objects to delete. Mandatory.

`isRegex` - when set to true, `NamePrefix` will be treated as regular expression to match object names for deletion. When set to false `NamePrefix` will be used as simple prefix to match object names to delete.

Use wisely. Wrong usage and configuration may result in deletion of large amounts of data

### LoadObjects

The process of loading objects needed during the test case is primarily done using `objectsToLoad` list root test case attribute. Even so there may be situations when you need to load some test related IdentityIQ objects during the processing of the ITF test case. For example, when testing custom audit, prior to the test you want all audit for a test target deleted, but after the deletion you want to load one audit event which simulates something that have already happened before the test.

`loadObjects` commands lets you load any Sailpoint object in the same manner as `objectsToLoad` does. It even uses the same setting for removing ids while loading.

Objects are loaded in the same order as they appear in the command. So for example if you are loading a role and it’s owner, owner object should be specified first followed by the role object.

```xml
<LoadObjects logDisplayName="Load something">
	<ObjectsList>
		<Object>John</Object>
		<Object>Jane</Object>
	</ObjectsList>
</LoadObjects>
```

**Tags:**

`<ObjectsList>` - contains list of `<Object>` tags that are loaded be the command.

`<Object>` - name of the file (without xml extension) containing IIQ object to be imported by the command.

### LogLevels

Command to set log levels on loggers on the server during the test execution.

This command sets the log level in memory. It will not survive server restart. If you want permanent setting, change log4j.properties.

```xml
<LogLevels logDisplayName="Setting log levels">
	<loggersList>
		<logger logLevel="TRACE" loggerName="com.sailpoint.Provisioner"/>
		<logger logLevel="DEBUG" loggerName="com.sailpoint.Identitizer"/>
	</loggersList>
</LogLevels>
```

`<loggersList>` - list of `<logger>` tags to be set

`<logger>` - sets one logger on the server to desired level

`<logger>` attributes:

`logLevel` - Desired log level. Possible values are the same as defined in log4j: TRACE, DEBUG, ERROR, INFO, WARN

`loggerName` - Name of the logger to set.

### MockedAggregate

Command to aggregate an account from json or xml file as resource object instead of invoking real application connector.

```xml
<MockedAggregate logDisplayName="Fake aggregation" objectType="account">
	<ApplicationName>HR_Employees</ApplicationName>
	<FileName>Gloria.Reynolds_hr</FileName>
	<Attributes>
		<entry key="promoteManagedAttributes" value="true"/>
	</Attributes>
</MockedAggregate>
```

`<MockedAggregate`\> attributes:

`objectType` - (optional) - Type of the object being aggregated. Default value if not provided is 'account'. This value must match 'objectType' value from application schema.

**Tags:**

`<ApplicationName>`\- Application to aggregate from.

`<FileName>` - Name of the file (with or without extension) that contains aggregation data. Aggregation file contains data in json or xml format. Attribute names must match values from application schema. Make sure that one of the attributes is identity attribute (also matching the one configured in application schema).

`<Attributes>` - list of aggregation attributes.

Some aggregation attributes are set to the following defaults promoteAttributes: true, correlateEntitlements: false, noOptimizeReaggregation: true. You can change them by adding attributes property. Attributes you can use are:

* noAttributePromotion
* correlateEntitlements
* noOptimizeReaggregation
* checkDeleted
* checkHistory
* correlateOnly
* promoteManagedAttributes

### ProcessEvents

A significant part of the processing in IdentityIQ is based on life cycle events. `processEvents` command is the way to help you test the correctness of your events configuration. It is basically similar to running Identity refresh task with `processTriggers` option checked with filter set for the [testIdentity]. After running refresh command will check if there is a task result present with the name starting with `triggerName` attribute + “:” + name of the test identity. If such task result is found, command execution is considered successful. It throws otherwise. Command will timeout waiting for the task result after 20 seconds.

Internally, command uses `Identitizer` with “processEvents” enabled. Command launches lifecycle’s configured workflow synchronously.

You must have `testIdentity/<TestIdentity>` set in your test case to use this command.

```xml
<ProcessEvents logDisplayName="Trigger leaver" noopExpected="false">
	<identityTrigger>Leaver processing</identityTrigger>
</ProcessEvents>
```

`<ProcessEvents>` attributes:

`noopExpected` - Boolean, optional, default is false. When set to true reverses behavior of the command, no event being triggered is considered success. This may be very useful to test triggering logic, when it should trigger and when it should not.

**Tags:**

`<identityTrigger>` - Name of the life cycle event that is expected to be triggered by the command.

### Provision

Command to execute provisioning plan. Provided plan will be compiled and executed using `sailpoint.api.Provisioner` class. Please see [<Plan>](#plan) section of Common objects schemas chapter for detailed example of plan xml representation.

```xml
<Provision>
  <Plan identityName="Brenda.Cooper">
    <AccountRequest application="IIQ" op="Modify" nativeIdentity="Brenda.Cooper">
      <AttributeRequest name="region" op="Set" value="Australia"/>
    </AccountRequest>
  </Plan>
</Provision>
```

### RunRule

This command let’s you run any beanshell rule with parameters.

```xml
<RunRule ruleName="test">
  <Attributes>
    <entry key="inputValue" value="orange"/>
  </Attributes>
</RunRule>
```

`ruleName` mandatory attribute specifying the name of the rule you wan to run.

`Attributes` optional list of attribute you wan to pass as input argument in to the rule.

### RunScript

This command lets you run beanshell script you specify in the command.

```xml
<RunScript>
  <Source><![CDATA[
      import sailpoint.object.Identity;

      System.out.println("Running custom script");
      System.out.println("First name of spadmin: "+
          context.getObjectByName(Identity.class, "spadmin").getFirstname());
    ]]>
  </Source>
</RunScript>
```

`<Source>` mandatory XML element inside which you should put the code of the script you wan to run. To avoid problems with XML it is recommended that you put all you source co between `<![CDATA[` and `]]>`.

### RunWfl

Run workflow command gives you the ability to run any workflow with a predefined set of parameters. Workflow can be run synchronously or asynchronously. Synchronous execution uses `Workflower.launch()` method to start the workflow. Asynchronous execution uses `Workflow Request` to schedule immediate workflow start.

```xml
<RunWfl workflowName="LCM Provisioning" 
        compilePlan="false" 
        startAsynchronously="false" 
        updateRoleIdsInAccountRequests="true"
        logDisplayName="Running test workflow">
  <Attributes>
    <entry key="identityName" value="Betty.Young"/>
	<entry key="launcher" value="spadmin"/>
	<entry key="trace" value="false"/>
	<entry key="notificationScheme" value="none"/>
	<entry key="approvalScheme" value="none"/>
	<entry key="flow" value="AccessRequest"/>
  </Attributes>
  <Plan identityName="Betty.Young">
    <AccountRequests>
		<AccountRequest applicationName="IIQ" operation="Modify" nativeIdentity="Betty.Young">
			<AttributeRequests>
				<AttributeRequest attributeName="location" operation="Set" value="London"/>
			</AttributeRequests>
		</AccountRequest>
		</AccountRequests>
	</Plan>
</RunWfl>
```

`<RunWfl>` attributes:

`workflowName` - mandatory, name of the workflow to run.

`compilePlan` - If plan is included and this value is set to true, plan will be compiled and included in the workflow as project input attribute. Default is false.

`startAsynchronously` - When set to true workflow request (with current timestamp for start time) will be created instead of starting the workflow using `Workflower.launch()` method. Default value is false.

`updateRoleIdsInAccountRequests` - Boolean, default false. When you create identity request for role in IIQ, system creates a provisioning plan in which there is single `AccountRequest` for each role you order. In each of these `AccountRequests` there is an attribute named id that contains the id of requested role. For obvious reasons you can’t include that id in your test case yourself because it is different in each environment. Setting `RunWfl` command attribute `updateRoleIdsInAccountRequests` to true will make ITF set above described attribute id with correct role id in the `AccountRequest`. It works the same for entitlement request.

`<Attributes>` - list of attributes that will be set as input variables for the workflow.

`<Plan>` - Provisioning plan is a special parameter that deserves special treatment. You specify the plan on a dedicated attribute. XML schema will guide you with plan configuring. It is very similar to IIQ’s provisioning plan xml representation.

### SimulateProvisioning

When testing internal IdentityIQ configurations, the actual external provisioning is not important if your applications configuration(connector) is already properly tested (you will still have to perform end-to-end testing, but that is a separate topic). In order to be able to focus on IdentityIQ side, in ITF, all external provisioning can be simulated. This mechanism allows you to mark any application to be in simulation mode during the execution of your test case. After the test case is over, the simulation must be turned off.

Simulation means that any provisioning performed by Provisioner will always return with selected status. The possible statuses are defined by IdentitiIQ and are limited to: `queued`, `committed` and `failed`. The default setting is `committed`, but you can configure it any way you need. For example, you may have a test case which checks how your workflow reacts to failed provisioning.

Simulation is, technically, based on IntegrationConfig configuration so, if you already using it, you will have to overwrite your customization.

In this example application LDAP is configured to return committed result to any provisioning request.

```xml
<SimulateProvisioning>
  <Application>LDAP</Application>
</SimulateProvisioning>
```

Following example shows how to simulate failed provisioning from AD application

```xml
<SimulateProvisioning simulateStatus="failed">
  <Application>LDAP</Application>
</SimulateProvisioning>
```

**Properties:**

`applications` - list of application names to be set to simulation mode
`simulateStatus` - provisioningResult to be returned from simulated application. Available values are: `queued`, `committed`, `failed`. When not set, the default value is `commited`.  
`turnOffSimulation` - Boolean setting. When set to ‘true’, any configured simulation will be turned off. `simulateStatus` and `applications`settings will be ignored. This takes precedence.

Example usage:

```xml
<SimulateProvisioning turnOffSimulation="true"/>
```

Ensure that you turn off simulation at the end of your test case unless you need the simulation mode to stay configured in your IdentityIQ instance.

### TimeMachine

During manual testing of workItems lifecycle actions (sending notification, reminders, escalations, expirations) time machine capability of IdentityIQ is used. `timeMachine` command works in exact same manner provided ability to change system time and date to match desired state. This command has exactly same setting as IdentityIQ ’s time machine. You can advance current time by minutes, hours or days. You can also reset the time back to current system time and date.

Always remember to reset time machine to system time after the test. Otherwise all other process will use modified time.

```xml
<TimeMachine minutes="2" hours="3" days="1" reset="true"/>
```

**Properties:**

`minutes` (optional) number. Number of minutest to advance the time used by IdentityIQ to handle work item lifecycle.

`hours` (optional) number. Number of hours to advance the time used by IdentityIQ to handle work item lifecycle.

`hours` (optional) number. Number of hours to advance the time used by IdentityIQ to handle work item lifecycle.

Individually all three attributes above are optional but together one of them must be provided.

`reset`(optional) Boolean. When set to true ITF will reset time machine to system time and data. In such case all other time changing attributes are ignored.

### ValidateResult

Command to validate attribute values of various objects.

Very often the process of validation of the test case involves comparing the attribute values of different IdentityIQ objects with the expected ones. This process may be time consuming as well as error prone. The validateResult command eliminates that problem by automating the task.

Objects for which attributes can be validated are:

1. Identity
2. Link

```xml
<ValidateResult>
  <ExpectedIdentity identityName="Amanda.Ross" 
                    validateUnlistedLinks="false" 
                    validateUnlistedRoles="false">
    <ExpectedLinks>
      <ExpectedLink applicationName="HR_Contractors" nativeIdentity="2ff5">
        <Attributes>
          <entry key="inactiveIdentity" value="FALSE"/>
          <entry key="firstName" value="Amanda"/>
          <entry key="lastName" value="Ross"/>
          <entry key="fullName" value="Amanda.Ross"/>
          <entry key="employeeId" value="2ff5"/>
          <entry key="location" value="London"/>
          <entry key="managerId" value="1b"/>
          <entry key="department" value="Regional Operations"/>
          <entry key="region" value="Europe"/>
          <entry key="costcenter" value="R01,L03"/>
          <entry key="email" value="Amanda.Ross@demoexample.com"/>
        </Attributes>
      </ExpectedLink>
    </ExpectedLinks>
    <ExpectedManager>Jerry.Bennett</ExpectedManager>
    <Attributes>
        <entry key="empId" value="1b2c"/>
        <entry key="firstname" value="Amanda"/>
        <entry key="employeeType" value="employee"/>
        <entry key="displayName" value="Amanda.Ross"/>
        <entry key="location" value="London"/>
        <entry key="department" value="Regional Operations"/>
        <entry key="region" value="Europe"/>
        <entry key="costcenter">
          <value>
            <List>
              <String>R01</String>
              <String>L03</String>
            </List>
          </value>
        </entry>
        <entry key="email" value="Amanda.Ross@demoexample.com"/>
        <entry key="lastname" value="Ross"/>
    </Attributes>
    <ExpectedRoles>
      <Role>Programmer</Role>
    </ExpectedRoles>
    <RoleAssignments>
      <RoleAssignment endDate="22.05.2024" 
                      roleId="3452345234" 
                      startDate="21.07.2000"
                      date="today" 
                      roleName="BusinessAnalyst" 
                      source="Unknown">
        <RoleTarget applicationName="LDAP" 
                    nativeIdentity="cn=Betty Young,ou=users,dc=acme,dc=com"/>
      </RoleAssignment>
    </RoleAssignments>
    <RoleDetections>
      <RoleDetection date="today" 
                     roleName="Business analyst IT"
                     roleId="32465234">
        <RoleTarget applicationName="LDAP" 
                    nativeIdentity="cn=Betty Young,ou=users,dc=acme,dc=com">
          <AccountItem name="groups" 
                       value="cn=BusinessAnalystGroup,ou=groups,dc=acme,dc=com"/>
        </RoleTarget>
      </RoleDetection>
    </RoleDetections>
  </ExpectedIdentity>
</ValidateResult>
```

`<ExpectedIdentity>` - Represents identity data that will be validated.

**ExpectedIdentity Attributes:**

`identityName` - name of the validated identity.

`validateUnlistedLinks` - when set to true, ITF will throw in case there are links on validated identity which are not present inside `<ExpectedLinks>` tag. Default value false.

`validateUnlistedRoles`\- when set to true, ITF will throw in case there are roles on validated identity which are not present in the `<ExpectedRoles>` tag.

`<ExpectedLinks>` - contains list of `<ExpectedLink>` tag to be validated.

`<ExpectedLink>` - contains link data to be validated against validated identity.

**ExpectedLink Attributes:**

`applicationName` - Name of the application of the link being validated. Mandatory.

`nativeIdentity` - When specified ITF will check if link with such `nativeIdentity` exists on the Identity. In not present, ITF will only validate existence, in the identity, of any link from the application specified by `applicationName`.

When `nativeIdentity` is not specified and there are other attributes present for validation. ITF will pick the first link from the application `applicationName` on the identity and perform validation of the attributes against that link. There are no guaranties as to which is the “first” link. It depends on the order returned by IdentityIQ (and it is not guarantied in any way). Because of that, when validating link attributes (except just the link existence), it is recommended to specify `nativeIdentity` attribute in the test case.

`<ExpectedManager>` - name of the manager identity to validate.

`<Attributes>` - contains list of identity attribute entries to be validated.

`<ExpectedRoles>` - Contains list of `<Role>` to be validated against the identity.

`RoleAssignments` - List of role assignment to validate against the identity

`RoleAssignment` - Single assignment that ITF will try to find in the identity. Format is a direct mapping from IdentityIQs `RoleAssignment` object. All attributes of `RoleAssignment` and nested content will be searched for in identity.

**RoleAssignment attributes:**

`roleName` - Name of the role of the role assignment. Mandatory.

`date` - creation date in DD.MM.YYYY format. You can use `today+/-n` pseudo value in this attribute.

`startDate` - start date of the role assignment in DD.MM.YYYY format. You can use `today+/-n` pseudo value in this attribute.

`endDate` - end date of the role assignment in DD.MM.YYYY format. You can use `today+/-n` pseudo value in this attribute.

`roleId` - role id of the role in the role assignment. Because role ids, very often, differ between IIQ environments, it is not recommended to use this attribute if you want your test cases to be transferable between IIQ instances.

`source` - source of the role assignment.

`RoleTarget` - role target for the `RoleAssignment`. You can provide multiple `RoleTarget` s.

**RoleTarget attributes:**

`applicationName` - name of the application that is expected to be used for the `RoleAssignment`.

`nativeIdentity` - native identity of the link that is expected to be used for the `RoleAssignment`.

`AccountItem` - account item for the `RoleTarget`. Can provide multiple `AccountItem`s for single target. `AccountItem` sould be only used for targets belonging to role detections (same as in IdentityIQ).

**AccountItem attributes:**

`name` - name of the entitlement (managed attribute) type.

`value` - value of the entitlement (managed attribute).

`RoleDetections` - List of role detections to validate against the identity.

`RoleDetection` - Single detection that ITF will try to find in the identity. Format is a direct mapping from IdentityIQs `RoleDetection` object. All attributes of `RoleDetection` and nested content will be searched for in identity.

**RoleDetection attributes:**

`roleName` - Name of the role of the role detection. Mandatory.

`date` - creation date in DD.MM.YYYY format. You can use `today+/-n` pseudo value in this attribute.

`roleId` - role id of the role in the role detection. Because role ids, very often, differ between IIQ environments, it is not recommended to use this attribute if you want your test cases to be transferable between IIQ instances.

### WaitForWflFinish

Very often in IdentityIQ, any kind of data validation can only be done once the workflow processing is done. Also, even though most of the workflow related commands are synchronous (except for runWfl command with asynchronous option #LINK HERE), there may be situations where workflow is started asynchronously by the IdentityIQ configuration itself. `waitForWflFinish` command was designed for just such occasions. This command will perform busy waiting until workflow is finished. Finished status is determined by checking `isComplete()` method on `taskResult` of the workflow started previously by ATF test case. Successful finish is reported when workflow is finished and completion status was either `Success` or `Warning`. If there is no workflow being processed by the ATF is found, exception will be thrown. If the workflow doesn’t end within configured time out, exception will be thrown.

```xml
<WaitForWflFinish timeOutSec="60" logDisplayName="Waiting for workflow to finish"/>
```

**Properties:**

`timeOutSec` - optional, number of seconds to wait for workflow to finish before throwing exception. Default value is 40 seconds.

`logDisplayName` - see link.

see also: [runWfl command](/wiki/spaces/AR/pages/84475905)

### WaitForWflStep

Command to wait until workflow reaches certain step. Once command detects that workflow reached that step it finishes successfully. If the step is not reached before configurable timeout `ITFTestingException` is thrown.

Command works in two phases.

In the first phase it is trying to find the workflow case. If `NamePrefix` is preset, it will be used to find the workflow case otherwise command will expect the workflow case to be present in the testing context, being previously explicitly started by the test case (using [runWfl](/wiki/spaces/AR/pages/84475905) command).

If the workflow case is found by the first phase, command moves on to the second phase in which it waits for the workflow to reach step named `stepName` before configurable timeout.

This command will only work for approval, wait or background steps.

```xml
<WaitForWflStep stepName="My step name"
                stepWaitTimeout="60"
                wflStartTimeout="20" 
                logDisplayName="Waiting for backgrounded step">
    <NamePrefix isRegex="true" waitTimeout="40">^P.*wfl case.*</NamePrefix>
</WaitForWflStep>
```

**Properties:**

`stepName` - mandatory. Name of the workflow step ITF is waiting for.

`stepWaitTimeout` - Number of seconds ITF will wait for the workflow to reach designated step. Default value 60 seconds. Time start from the moment workflow case is either found by `NamePrefix` or taken from testing context.

`wflStartTimeout` - Number of seconds ITF will keep searching for the workflow to start. This is used only in case there is no `NamePrefix` and workflow was previously started asynchronously. Default value is 120.

`NamePrefix` - element used find workflow case to be used in second phase of the command. Value is mandatory.

`isRegex` - Boolean value (default is false) that specifies how to use `NamePrefix` value when searching for workflow case. When set to true, value is treated as regular expression and ITF will search for workflow case which name matches the expression. When set to false `NamePrefix` value is treated as simple name prefix and ITF will search for workflow case which name starts with the value.

`waitTimeout` - Number of seconds ITF will keep searching for the workflow case. When not provided ITF will only check for the existence of expected workflow case once.

### Workflower

Command to pick up backgrounded or waiting workflow case, and begin processing it. This is similar to running `"Perform Maintenance"` task with `Process background workflow events` selected.

Without specifying `<NamePrefix>` this command will only process event for `workflowCase` previously started by the ITF test case. If there was no workflow started previously by the ITF test case, the exception will be thrown and the test case will fail (unless `failOnNotExist` is set to false).

With `<NamePrefix>` command can “wake up” any arbitrary workflow case. Command searches all the workflow cases and trying to match the one with name starting with `<NamePrefix>` value. If no such case is found exception is thrown (unless `failOnNotExist` is set to false). If more than one workflow case is found, exception is also thrown. If the workflow case is not immediately found you can specify `waitTimeout` (in seconds). Command will keep trying to search(roughly every second) for the workflow case for specified amount of seconds before giving up. When `isRegex` is set to true, command with try to match the workflow’s case name using regular expression rather that simple string “starts with” search. In such case `<NamePrefix>` value is used as regex pattern.

When `waiting` is used in the workflow, there is an expiration time set on the event work item, to timestamp when the workflow case should be restarted. This command ignores that setting and will reactivate the workflow case immediately.

Once found, the `workflowCase` is restarted synchronously, meaning the `wakeUpWorkflow` command will not return until the workflow finishes, stops at approval, or is backgrounded or waited again.

```xml
<Workflower failOnNotExist="true">
  <NamePrefix waitTimeout="60" isRegex="true">My asynchronous workflow for John</NamePrefix>
</Workflower>
```

**Properties:**

`failOnNotExist` - Boolean, optional property. When set to true, the command will throw exception in case there is no workflow previously started by the ITF case. When false and there is no `workflowCase` to wake up, the command will do nothing and processing will move on.  
`<NamePrefix>` - When present command searches for arbitrary backgrounded workflow case matching element's value.

`waitTimeout` - number of seconds to search for workflow case specified by value

`isRegex` - when true, use regular expression to search for workflow case.

### ValidateXpath

This command lets you validate any attribute present in XML object representation of any SaliPointObject.

Command contains two elements: one specifies they way to find an objects for validation and second one the validations itself.

There are 3 ways to locate an object in this command: using name or id, using filter string or using HQL query.

No matter which method you use, make sure that it results with finding exactly one object.

```xml
<ValidateXpath>
  <ObjectFinder objectClass="Identity">
    <NameOrId>Pamela.Kelly</NameOrId>
  </ObjectFinder>
  <Validation>
    <XPath>/Identity/Attributes/Map/entry[@key='department']/@value</XPath>
	<ExpectedValue>Finance</ExpectedValue>
  </Validation>
</ValidateXpath>
```

```xml
<ValidateXpath>
  <ObjectFinder objectClass="Bundle">
    <FilterString>name == "Contractor_BR"</FilterString>
  </ObjectFinder>
  <Validation>
    <XPath>/Bundle/Attributes/Map/entry[@key='allowMultipleAssignments']/@value</XPath>
	<ExpectedValue>false</ExpectedValue>
  </Validation>
</ValidateXpath>
```

```xml
<ValidateXpath>
  <ObjectFinder objectClass="Identity">
    <HqlQuery>from Identity where name='Pamela.Kelly'</HqlQuery>
  </ObjectFinder>
  <Validation>
    <XPath>/Identity/@name</XPath>
    <ExpectedValue>Pamela.Kelly</ExpectedValue>
  </Validation>
</ValidateXpath>
```

`<ObjectFinder>` - Node with instructions how to find the object to validate

`objectClass` - mandatory attribute to specify what type of SailPointObject to search for

`<NameOrId>` - Node to provide the name of id of the object to validate

`<FilterString>` - Node to provide a filter string to find the object for validation.

`<HqlQuery>` - Node to provide a HQL query to find the object for validation.

Only one of `<NameOrId>`, `<FilterString>`, `<HqlQuery>`can be used at the same time.

`<Validation>` - Node with the validation information for command.

`<XPath>` - Node to provide an xpath expression. This expression should return the string value.

`<ExpectedValue>` - Node that contains the value to be validated against what resides in the pace specified by xpath expression.

### CheckApprovalExpirationResult

Sometimes it is needed to test the behavior of the approval which is a part of Identity Request. Checking exitance of such approvals and acting on them can be done with <CheckApprovals> command, but to validate what happens on the expiration requires this dedicated command.

This command will search for the latest access request made by the test identity and will validate if it contains specified approvals in given status. Commands structure reflects how IdentityIQ stores it’s data.

```xml
<CheckApprovalExpirationResult>
  <IdentityName>TestIdentity</IdentityName>
  <ApprovalSummary approvalSummaryState="Finished">
    <ApprovalSet>
      <RequestApprovalItem application="LDAP" name="groups" nativeIdentity="cn=Anna Ward,ou=users,dc=acme,dc=com" operation="Add" value="cn=AccountingAccess,ou=groups,dc=acme,dc=com"/>
      <RequestApprovalItem application="LDAP" name="groups" nativeIdentity="cn=Anna Ward,ou=users,dc=acme,dc=com" operation="Add" value="cn=Bank Account Access,ou=groups,dc=acme,dc=com"/>
    </ApprovalSet>
  </ApprovalSummary>
  <ApprovalSummary approvalSummaryState="Expired">
    <ApprovalSet>
      <RequestApprovalItem application="LDAP" name="groups" nativeIdentity="cn=Anna Ward,ou=users,dc=acme,dc=com" operation="Add" value="cn=AccountingAccess,ou=groups,dc=acme,dc=com"/>
    </ApprovalSet>
  </ApprovalSummary>
  <ApprovalSummary approvalSummaryState="Finished">
    <ApprovalSet>
      <RequestApprovalItem application="LDAP" name="groups" nativeIdentity="cn=Anna Ward,ou=users,dc=acme,dc=com" operation="Add" value="cn=Bank Account Access,ou=groups,dc=acme,dc=com"/>
    </ApprovalSet>
  </ApprovalSummary>
</CheckApprovalExpirationResult>
```

`<ApprovalSummary>`\- Container for approvals to validate. Command can validate multiple ApprovalSummaries.

`approvalSummaryState` - attribute of the ApprovalSummary. Represents the state of the summary.

`<ApprovalSet>` Single approval set for ApprovalSummary. There can be only one ApprovalSet per ApprovalSummary.

`<RequestApprovalItem>` - Single approvalItem to be validated. The attribute is the same as the attribute of Sailpoint’s ApprovalItem.

`<IdentityName>` - Optional element. Command will search for last identity request for test identity (specified in the test case). If this element is present command will search for last identity request belonging to this identity instead.

### Common objects schemas

#### Plan

Schema representing provisioning plan. XML elements naming is the same as original XML `ProvisioningPlan` representation in IdentityIQ.

```xml
<Plan identityName="Brenda.Cooper">
	<Attributes>
		<entry key="sample attribute" value="house"/>
	</Attributes>
	<AccountRequest application="LDAP" nativeIdentity="Brenda" op="Modify">
		<Attributes>
			<entry key="account level attribute" value="tree"/>
		</Attributes>
		<AttributeRequest assignmentId="..." name="groups" op="Add" value="Sales">
			<Attributes>
				<entry key="attribute level attributes" value="apple"/>
			</Attributes>
		</AttributeRequest>
	</AccountRequest>
	<AccountRequest application="LDAP" nativeIdentity="Brenda" op="Modify">
		<AttributeRequest assignmentId="..." name="groups" op="Add" value="BankAccount"/>
	</AccountRequest>
	<ProvisioningTargets>
		<ProvisioningTarget application="LDAP" 
							assignmentId="..." 
							attribute="groups" 
							value="Sales">
			<AccountSelection applicationName="LDAP" 
							  doCreate="false" 
							  implicitCreate="false" 
							  selection="Brenda">
				<AccountInfo displayName="Brenda Cooper" nativeIdentity="Brenda"/>
			</AccountSelection>
		</ProvisioningTarget>
		<ProvisioningTarget application="LDAP" 
							assignmentId="..." 
							attribute="groups" 
							value="BankAccount">
			<AccountSelection applicationName="LDAP" 
							doCreate="false" 
							implicitCreate="false" 
							selection="Brenda">
				<AccountInfo displayName="Brenda Cooper" nativeIdentity="Brenda"/>
			</AccountSelection>
		</ProvisioningTarget>
	</ProvisioningTargets>
	<Requesters>
		<Reference class="sailpoint.object.Identity" name="spadmin"/>
	</Requesters>
</Plan>
```