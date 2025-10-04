# Release notes

* * *
### <a id="5.2.0"></a> [5.2.0](#5.2.0) - 2025-10-04

###### IntelliJ Plugin - Major Update (Version 2.0.0)
1. **New:** Modern IIQ server configuration system with centralized management
2. **Enhanced:** Settings dialog with table-based server management and connection testing
3. **Improved:** Execution target designation
4. **Backward Compatible:** Existing target.properties-based configurations continue to work

###### Fixed
1. Handling forms with no fields in approval workflows

###### Changed
1. [SetAttribute command](ITF%20XML%20Reference.md#setattribute) - value attribute is now optional
2. Server configuration moved to a centralized management system

* * *
### <a id="5.1.1"></a> [5.1.1](#5.1.1) - 2025-09-03
###### Added
1. Identity type fields in Forms handling. 

* * *
### <a id="5.1.0"></a> [5.1.0](#5.1.0) - 2025-08-24
###### Fixed
1. Result validation fixed.
###### Added
1. Partial mocked aggregate command.
2. Quicklink command.
3. SetAttribute command.
4. WaitForTaskResult command.
5. CreateFile command.


### <a id="5.0.0"></a> [5.0.0](#5.0.0) - 2025-05-20
###### Fixed
1. Reading workItems from IIQ server now works properly.
2. Attributes value can now be nested.
###### Added
1. Skip option for all commands
2. New command CheckIdentityNotExists
3. New "ldap" format for pseudo value "today"
4. Simulation results can now be modified on the fly.
5. "null" value for validation.
6. ObjectRequest for provisioning plan testing.
7. Tokenization now possible for all test case files.
8. New SqlScript command.
9. "today" pseudo value can now be used in task attributes.


### <a id="4.19.0"></a> [4.19.0](#4.19.0) - 2025-03-15
###### Fixed
1. Clean workflow command checks for workflow existance while iterating.
###### Added
1. Attributes for run workflow command. [More info...](ITF%20XML%20Reference.md/#runtask)

### <a id="4.18.0"></a> [4.18.0](#4.18.0) - 2025-03-08
###### Fixed
1. Documentation updates.
2. Form handler default String type for fields.
###### Changed
1. Connector command now based on connector class instead of connector proxy class. [More info...](ITF%20XML%20Reference.md/#connector)
###### Added
1. System.out logging for ITF client. [More info...](Configuring%20ITF%20Client%20side%20logging.md)
2. createIdentity for provisioning plan. [More info...](ITF%20XML%20Reference.md/#plan)

### <a id="4.17.2"></a> [4.17.2](#4.17.2) - 2025-02-16
###### Added
1. New command [Sleep command](../ITF%20XML%20Reference/#sleep).
2. New ITF Client logging with System.out.

###### Fixed
1. Proper handling of referenced forms in workflow (including transient and multiple ones).
2. Updated Intellij plugin with latest ITF library version.

### <a id="4.17.1"></a> [4.17.1](#4.17.1) - 2025-01-05

###### Fixed
1. Transient for validation whn for is referenced in workflow(as opposed to being embedded)

### <a id="4.17.0"></a> [4.17.0](#4.17.0) - 2024-12-25
###### Added
1. isTransient setting on [runWorkflow command](../ITF%20XML%20Reference/#runwfl).
2. ITF client timeout setting. Test case level: [here](../ITF%20XML%20Reference/#itf-test-case) JVM setting: [here](../IIQ%20connectivity/#client-timeout-configuration).

###### Fixed
1. Transient form validation.

### <a id="4.16.0"></a> [4.16.0](#4.16.0) - 2024-10-20
###### Added
1. New approval type ViolationReview support for Approval command.
2. Completer for approvalSet based workItems.

###### Fixed
1. Approval now registers owner of the approval as the completer.

### <a id="4.14.0"></a> [4.14.0](#4.14.0) - 2024-08-14

###### Added
1. Date format for [pseudo attribute today](/Pseudo%20date%20handling) value for [MockedAggregate](/ITF%20XML%20Reference/#mockedaggregate) command.

###### Changed
1. Release notes format changed.

### <a id="4.13.3"></a> [4.13.3](#4.13.3) - 2024-08-05

###### Added
1. throwIfExists option for ExpectedLink of [ValidateResult](/ITF%20XML%20Reference/#validateresult) command.
2. You can now use [pseudo attribute today](/Pseudo%20date%20handling) in [ValidateResult](/ITF%20XML%20Reference/#validateresult) command for validating identity and link attributes.
3. Running test cases stored in IIQ from command line [here](/IIQ%20connectivity/#running-test-case-stored-on-the-server-from-command-line).

###### Fixed
1. Bugfix: accepting null ResourceObject on MockedAggregation command.
2. BugFix: running schema customization rules on MockedAggregation command.

### <a id="4.13.2"></a> [4.13.2](#4.13.2) - 2024-06-23

1. Link disable validation with pseudo attribute IIQDisabled.
2. Multithreaded ITF execution bugfix.

### <a id="4.13.1"></a> [4.13.1](#4.13.1) - 2024-05-20

1. ITF task results now end with guid.

### <a id="4.13"></a> [4.13](#4.13) - 2024-05-08

1. New command: [InvokeTestCase](/ITF%20XML%20Reference/#invoketestcase)

### <a id="4.12"></a> [4.12](#4.12) - 2024-04-19

1. New improved CheckEmail command and EmailNotifierControl command to manage email notifications logging.

### <a id="4.11"></a> [4.11](#4.11) - 2024-03-31

1. Matching workItems for approval by Description.
2. Throwing explicit exception when more than one approval matches the approval command.

### <a id="4.10.2"></a> [4.10.2](#4.10.2) - 2024-03-18

1. ITF configuration checker in Intellij plugin
2. Bugfix: Proper ITF client authentication to IIQ server

### <a id="4.10.1"></a> [4.10.1](#4.10.1) - 2024-02-03

1. bugfix: Back button in custom form approval now works properly.

### <a id="4.10"></a> [4.10](#4.10) - 2024-02-03

1. Single approval command
2. SetTestIdentity command
3. WaitForWflFinish command enhancement
4. Email notifier now supports ootb and custom notifiers.
5. Validating account attributes directly in target system.
4. Bugfixes

### <a id="4.9"></a> [4.9](#4.9) - 2023-10-30

1. CancelAccessRequest command
2. failOnExists for approvals
3. Wait for task to finish command
4. Internal: Simulation now works based on single integration config and configuration is placed in custom object.

### <a id="4.8.5"></a> [4.8.5](#4.8.5) - 2023-09-15

1. Bugfix: Error while importing test identity when placed in <ObjectsToLoad> tag with a different file name.
2. Bugfix: Correctly validating Link multivalued attribute when it has only single value.

### <a id="4.8.4"></a> [4.8.4](#4.8.4) - 2023-08-10

1. Bugfix: Retry on hHttpClient
   
IntelliJ plugin version 1.9.2

1. Java - ITF test case reference contributor.
2. Find JUnit class with current test case.

### <a id="4.8.3"></a> [4.8.3](#4.8.3) - 2023-07-05

1. Small bugfixes
2. class case exception on getting test case result mitigation

### <a id="4.8.2"></a> [4.8.2](#4.8.2) - 2023-06-23

1. FailOnException option for Provisioning and Aggregate command.
2. jar dependencies included in release
3. Connector command
4. Bug fixes

### <a id="4.8.1"></a> [4.8.1](#4.8.1) - 2023-05-20

Bugfix: MockedAggregation now properly can handle Date and Long attribute types. Mocked aggregation file generator (importing mocked aggregation file in IntelliJ) now properly imports Long attributes.

### <a id="4.8"></a> [4.8](#4.8) - 2023-04-19

Pseudo today now can contain date format.

IntelliJ plugin version 1.8.3

* Insert refresh command
* New Insert mocked aggregation command

### <a id="4.7"></a> [4.7](#4.7) - 2023-03-31

[RunRule] and [RunScript] commands added.

Role assignment and role detections validation added to ValidateResult command.

New IntelliJ plugin version 1.8.2

* Refactored insertion of ITF elements
* Ability to insert test identity (including selection of elements and downloading the file to local dir)
* Validation insertion now include role assignments and role detections

### <a id="4.6"></a> [4.6](#4.6) - 2023-02-03

Session based ITF client

Injecting id for role or entitlement in access request workflows

CheckApprovalExpirationResult command

Identity name no longer needs to match file name for testIdentity

IntellijPlugin now supports injecting CheckApprovalExpirationResult command from IIQ server data

Intellij plugin bugfixes.

### <a id="4.5"></a> [4.5](#4.5) - 2023-01-20

Approval command now supports custom forms with hidden fields

New version 1.8 of the IntelliJ plugin. Bug fixed.

### <a id="4.4"></a> [4.4](#4.4) - 2022-12-15

Attributes available on all levels in provisioning plan

Run task command

Provision command

Bug fix: NPE on link validation

### <a id="4.3"></a> [4.3](#4.3) - 2022-11-10

Policy handling in approval forms

Dynamic forms support

Check expired workItems command