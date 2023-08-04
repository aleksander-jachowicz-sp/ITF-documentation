# Test identity generator

<https://itestf.atlassian.net/wiki/spaces/ITF/pages/247070758/Test+identity+generator>

* * *

Preparing test data can be time consuming process. ITF can help in preparing test cases data based on identities that already exist in the IdentityIQ instance.

Following data items can be generated:

1. Import ready identity xml - Test data should not contain object ids, created, modified timestamps in object XMLs being imported during execution of the test cases. There is also a problem with role in identity xml. Authoritative source of identity role assignment information are RoleAssignment objects and they reference Bundle objects by id not the name. If you used this in test case it would make it instance dependent (you have different ids for the same role between instances). You would have a test case you con only run on IIQ you used to create such case, you could not share it with other developers for example. Due to that, ITF uses a different approach described in #2 below.
2. Sample test case Json. Test identity generator will prepare a sample test case Json content that contains various commands with related data reflecting selected identity. Following elements are generated:
    1. Whole test case template with attributes: `testCaseName`, `testIdentity`, `removeIds` and `processOrder`.
    2. `simulateProvisioning` command with all applications which entitlements are included in the roles assigned to test identity.
    3. [assign <Assign>](/wiki/spaces/AR/pages/45154509) command with all the roles, that identity had, listed.
    4. `validateResult` command with validation of all identity attributes and all link attributes from test identity.
3. Mocked aggregation file content - you can select which link you want to use, and the generator will create Json content with all link attributes that can be copied and pasted into mocked aggregation files.

### Usage

To run test data generator, open the hamburger menu on the left and select ITF and “Create test identity” item.

![](https://itestf.atlassian.net/wiki/download/thumbnails/247070758/TestDataGenerator1.png?version=1&modificationDate=1670069911171&cacheVersion=1&api=v2&width=340&height=203)

List of identities will be displayed. Search and select the identity you wish to use for a test.

![](https://itestf.atlassian.net/wiki/download/thumbnails/247070758/TestDataGenerator2.png?version=1&modificationDate=1670069911176&cacheVersion=1&api=v2&width=340&height=205)

Once the identity is selected, generator will display generated test data. You can now copy the content to your test case.

![](https://itestf.atlassian.net/wiki/download/thumbnails/247070758/TestDataGenerator3.png?version=1&modificationDate=1670069911195&cacheVersion=1&api=v2&width=340&height=446)

### Customizing test data generation

You have the ability to customize the way the test data is being generated. ITF plugin contains a configurable rule that can manipulate the test identity once it’s selected. You can write your own customization rule matching your requirements for test data. ITF comes with a sample customization rule that adds \_test to identity name.

```java
<?xml version='1.0' encoding='UTF-8'?>
<!DOCTYPE Rule PUBLIC "sailpoint.dtd" "sailpoint.dtd">
<Rule language="beanshell" name="Test Identity Creation Sample">
    <Description>
        Modify input identity for your testing. any changes to roles are ignored.
        DO NOT save this identity, just return it after modification.
        You can modify identity attributes and link attributes.
    </Description>
    <Signature returnType="String">
        <Inputs>
            <Argument name="identity" type="Identity">
                <Description>
                    Test identity that should be returned. 
                    DO NOT save this identity as it only should exist in memory.
                </Description>
            </Argument>
            <Argument name="context" type="SailPointContext">
                <Description>
                    A sailpoint.api.SailPointContext object that can be used to 
                    query the database if necessary.
                </Description>
            </Argument>
        </Inputs>
        <Returns>
            <Argument name="identity" type="Identity"/>
        </Returns>
    </Signature>
    <Source><![CDATA[
        import sailpoint.object.*;

        identity.setDisplayName(identity.getDisplayName()+"_test");
        return identity;
    ]]>
    </Source>
</Rule>
```

Inside the rule, you get the identity object of the selected identity, and you can manipulate the identity in any way you need. Once ready, the identity object should be returned and it will be used to generate test data.

Do NOT save the identity, just return it!

Once your customization rule is ready, import it to IdentityIQ and configure in plugin. To do that, go to the plugin page and select Configuration for ITF plugin.

![](https://itestf.atlassian.net/wiki/download/thumbnails/247070758/TestDataGenerator4.png?version=1&modificationDate=1670069911196&cacheVersion=1&api=v2&width=340&height=196)

On the configuration page, select the name of your customization rule and press Save.

Once all the above steps are complete, test data generator will use your process to prepare the data for test.