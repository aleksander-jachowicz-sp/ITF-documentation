# Release notes

* * *
## <a id="4.16.0"></a> [4.17.0](#4.17.0) - 2024-12-25
###### Added
1. isTransient setting on [runWorkflow command](/ITF%20XML%20Reference/#runwfl).
2. ITF client timeout setting. Test case level: [here](/ITF%20XML%20Reference/#itf-test-case) JVM setting: [here](/IIQ%20connectivity/#client-timeout-configuration). 

###### Fixed
1. Transient form validation.

## <a id="4.16.0"></a> [4.16.0](#4.16.0) - 2024-10-20
###### Added
1. New approval type ViolationReview support for Approval command.
2. Completer for approvalSet based workItems.

###### Fixed
1. Approval now registers owner of the approval as the completer.

## <a id="4.14.0"></a> [4.14.0](#4.14.0) - 2024-08-14

###### Added
1. Date format for [pseudo attribute today](/Pseudo%20date%20handling) value for [MockedAggregate](/ITF%20XML%20Reference/#mockedaggregate) command. 

###### Changed
1. Release notes format changed.


## <a id="4.13.3"></a> [4.13.3](#4.13.3) - 2024-08-05

###### Added
1. throwIfExists option for ExpectedLink of [ValidateResult](/ITF%20XML%20Reference/#validateresult) command.
2. You can now use [pseudo attribute today](/Pseudo%20date%20handling) in [ValidateResult](/ITF%20XML%20Reference/#validateresult) command for validating identity and link attributes.
3. Running test cases stored in IIQ from command line [here](/IIQ%20connectivity/#running-test-case-stored-on-the-server-from-command-line).

###### Fixed
1. Bugfix: accepting null ResourceObject on MockedAggregation command.
2. BugFix: running schema customization rules on MockedAggregation command.

## 4.13.2 - 2024-06-23

1. Link disable validation with pseudo attribute IIQDisabled.
2. Multithreaded ITF execution bugfix.

## 4.13.1 - 2024-05-20

1. ITF task results now end with guid.

## 4.13 - 2024-05-08

1. New command: [InvokeTestCase](/ITF%20XML%20Reference/#invoketestcase)

## 4.12 - 2024-04-19

1. New improved CheckEmail command and EmailNotifierControl command to manage email notifications logging.

## 4.11 - 2024-03-31

1. Matching workItems for approval by Description.
2. Throwing explicit exception when more than one approval matches the approval command.

## 4.10.2 - 2024-03-18

1. ITF configuration checker in Intellij plugin
2. Bugfix: Proper ITF client authentication to IIQ server

## 4.10.1

1. bugfix: Back button in custom form approval now works properly.

## 4.10 - 2024-02-03

1. Single approval command
2. SetTestIdentity command
3. WaitForWflFinish command enhancement
4. Email notifier now supports ootb and custom notifiers.
5. Validating account attributes directly in target system.
4. Bugfixes

## 4.9 2023-10-30

1. CancelAccessRequest command
2. failOnExists for approvals
3. Wait for task to finish command
4. Internal: Simulation now works based on single integration config and configuration is placed in custom object.

## 4.8.5

1. Bugfix: Error while importing test identity when placed in <ObjectsToLoad> tag with a different file name.
2. Bugfix: Correctly validating Link multivalued attribute when it has only single value.

## 4.8.4

1. Bugfix: Retry on hHttpClient

IntelliJ plugin version 1.9.2

1. Java - ITF test case reference contributor.
2. Find JUnit class with current test case.

## 4.8.3

1. Small bugfixes
2. class case exception on getting test case result mitigation

## 4.8.2

1. FailOnException option for Provisioning and Aggregate command.
2. jar dependencies included in release
3. Connector command
4. Bug fixes

## 4.8.1

Bugfix: MockedAggregation now properly can handle Date and Long attribute types. Mocked aggregation file generator (importing mocked aggregation file in IntelliJ) now properly imports Long attributes.

## 4.8

Pseudo today now can contain date format.

IntelliJ plugin version 1.8.3

* Insert refresh command
* New Insert mocked aggregation command

## 4.7

[RunRule] and [RunScript] commands added.

Role assignment and role detections validation added to ValidateResult command.

New IntelliJ plugin version 1.8.2

* Refactored insertion of ITF elements
* Ability to insert test identity (including selection of elements and downloading the file to local dir)
* Validation insertion now include role assignments and role detections

## 4.6

Session based ITF client

Injecting id for role or entitlement in access request workflows

CheckApprovalExpirationResult command

Identity name no longer needs to match file name for testIdentity

IntellijPlugin now supports injecting CheckApprovalExpirationResult command from IIQ server data

Intellij plugin bugfixes.

## 4.5

Approval command now supports custom forms with hidden fields

New version 1.8 of the IntelliJ plugin. Bug fixed.

## 4.4

Attributes available on all levels in provisioning plan

Run task command

Provision command

Bug fix: NPE on link validation

## 4.3

Policy handling in approval forms

Dynamic forms support

Check expired workItems command