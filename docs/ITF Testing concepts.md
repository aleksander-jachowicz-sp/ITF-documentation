# ITF Testing concepts
* * *

A basic ITF test case consists of 3 parts, as follows: a) loading test data, b) executing the actions (commands), and c) validating results. Obviously, more complex patterns are possible (load data, execute something, validate, load more data, execute, validate again), but in order to understand how ITF works, we will focus on the basic scenario.

## Loading test data

In ITF, each test case revolves around performing an action to a designated test identity (aggregating, requesting for, running though refresh, triggering lifecycle events, etc.). But, from the perspective of test identity, we can have two types of test cases:

* Cases when the identity should already exist before we start the test case
* Cases when the identity does not exist and is being aggregated as a part of the test case. Even in this situation, some other expected Identities and/or objects should exist (e.g. an expected manager of the identity being aggregated).

Let’s start with the first situation.

#### Main test identity

Test identity xml should be saved in a xml file. It is a best practice to name the file the same as the test identity name. For example, if the test identity name is JohnDoe, the file will be called JohnDoe.xml. The name of our test identity, or rather the name of the file that contains the test identity xml, should be specified in `testIdentity` test case attribute (without xml extension).

The second scenario is when you don’t want the identity to exist at the beginning of the test because the test is to aggregate the data and validate identity creation.

#### Initial aggregation

When you don’t want to have identity imported before the test case, simply don’t specify `testIdentity` attribute in the test case. Next step is to tell ITF to aggregate the data. To do that, use `mockedAggregate` command in `processOrder`. You will have to specify what application should be used for aggregation and the name of the json file containing aggregation data.

```java
{
    "mockedAggregate": {
        "applicationName": "Application HR",
        "fileName": "myAggregationFile"
    }
}
```

#### Other identities and objects

Besides the main test identity, you will need other additional objects required in your test cases. All of them (their XML representation without ids) should be saved in individual xml files (best practice ![(thumbs up)](/wiki/s/-69361965/6452/ebb6a38bd07dacac904a492e44a0530ef4a35e36/_/images/icons/emoticons/thumbs_up.png): use the same folder as for your test case and test identity files). Once you have all the test objects in xml files, you need to tell ITF to import them. You do this by listing file names in `objectsToLoad` attribute of your test case. This attribute is a list of strings. Each string corresponds to test object file name without extension.

#### Objects loading order

The order in which you import objects into IdentityIQ is important. If you load an identity before you load its manager, that identity will not have that manager information. So it is important to specify objects to load in a specific order. Overall the order in which ITF is importing the data is as follows:

1. `objectsToLoad` is evaluated first and all objects from this list are imported in the order they appear.
2. `testIdentity` is imported.

There could be a situation where you need to load an object during the execution of a test case (after the initial import). In such situation, you can use `loadObjects` command. (Check command reference for details)

#### Importing role assignments

In XML role assignments, store role information using role ids rather than role names. If you wanted to import an identity with assigned roles, you would have to leave role and assignment ids in the XML. That would make the test case work only in the environment that contained roles with those particular ids. Essentially, your test cases would not be portable (although it is possible to configure ITF test case to load objects with ids).

ITF work around is as follows. Remove any role information in your identity XML (this includes roleAssignments, roleDetections, assigned roles, detected roles). You should still keep all the entitlements (managed attributes) granted by the roles on the links. Use `assign` command at the beginning of `processOrder` with all the roles that you want to be assigned to test identity on the list.

```java
{
    "assign": {
        "bundle": ["role1", "role2"]
    }
}

```

The result will be the identity with all roles assigned.

## Execution of test actions

When testing manually, once you configure the test identity, you start running the main testing activities. These could be requesting a role for the the identity, running aggregation, invoking life cycle event for the test identity. ITF is very similar, except you configure what you want to happen using [ITF commands](https://itestf.atlassian.net/wiki/pages/resumedraft.action?draftId=18022739) and framework will do the job for you. The huge benefit is not only that ITF will execute the actions for you, saving a lot of manual labor time, but once you defined these actions all the test cases are automatically repeatable.

The actions that you can perform are represented by the commands provided by ITF. Please see [ITF commands reference](https://itestf.atlassian.net/wiki/pages/resumedraft.action?draftId=18022739) for details.

## Validation results

Once all the actions have been executed, the last element of usual test case is data validation. Technically, this phase is also done by using validation commands like [validateResult](/wiki/spaces/AR/pages/55377986) or [checkAudit](/wiki/spaces/AR/pages/124223535).

## Cleanup

Although not required, it is a good practice to clean (delete) all the test data used during the test case after all work is finished. To do that, you can use [clean command](/wiki/spaces/AR/pages/55312406) as a last command in your test case.

## ITF Directories

ITF binaries are located in the lib directory. It’s actually one jar file AutomatedTester.x.y.jar

##### itf/resources

This directory contains properties file with default configuration, which you copy during the installation and log4j2.properties, which controls logging of the ITF client in your IDE. If you have problems with your ITF execution, you may increase log levels of some of the ITF loggers in that file.

##### ift/schema

This directory hold json schema file for ITF test cases. This is a very helpful schema as it will be able to guide while creating test cases. It will show you available options and commands while you type in the editor.

##### ift/testData

This is the default directory where all test data is stored for the test cases (Note: this location is configurable, but it is advised to keep the default).

##### itf/test

Here, all the ITF JUnit java files are stored. ITF uses JUnit to invoke the test cases. We are hijacking JUnit infrastructure as it is very flexible and widely used, but we are only using it to trigger the test case. Once triggered, ITF takes care of the rest.