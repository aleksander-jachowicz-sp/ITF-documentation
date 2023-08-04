# Configuring ITF DTD for IntelliJ

<https://itestf.atlassian.net/wiki/spaces/ITF/pages/266436624/Configuring+ITF+DTD+for+IntelliJ>

* * *

ITF test cases can be defined using XML. To provide ability to easily create those test cases ITF comes with dtd file that defines the XML content and provide huge help in writing the XML. In this chapter you will learn how to configure the dtd.

You will find the dtd file (AutomatedTestingXMLSchemaXX.dtd) in the distribution zip under itf\\schema\\ directory. Place this file somewhere available to the IDE of your choice. The common place is \\itf\\schema directory (create if not exists) in you IIQ build.

##### Intellij configuration for TestCase dtd

Open Settings window (File → Settings) and select Langauages & Frameworks → Schemas and DTDs.

Select a plus sign on the right top to add new DTD configuration.

![](https://itestf.atlassian.net/wiki/download/attachments/266436624/Screenshot_20221211_122945.png?version=1&modificationDate=1690355982684&cacheVersion=1&api=v2)

In the URI filed insert `http://www.amidentity.com/dtd/AutomatedTestingXMLSchema20.dtd`

File should point to to where you placed the dtd file (itf\\schema\\AutomatedTestingXMLSchema20.dtd).

![](https://itestf.atlassian.net/wiki/download/attachments/266436624/dtd%20config%20intellij.png?version=2&modificationDate=1690356966733&cacheVersion=1&api=v2)

Once configured press OK twice. DTD configuration is now finished and will be referenced when you place following heading in you test case file:

```java
<?xml version='1.0' encoding='UTF-8'?>
<!DOCTYPE TestCase PUBLIC "-//amidentity//DTD TestCase 2.0//EN" 
"http://www.amidentity.com/dtd/AutomatedTestingXMLSchema20.dtd">
```

Perform similar procedure to configure dtd for resource object xml files.

Use this URI: `http://www.amidentity.com/dtd/MockedRecourceObjectXMLSchema20.dtd`

Use following header for mocked resource objects xml files:

```java
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE MockedAccount PUBLIC "-//amidentity//DTD Mocked ResourceObject 2.0//EN" 
"http://www.amidentity.com/dtd/MockedRecourceObjectXMLSchema20.dtd">
```