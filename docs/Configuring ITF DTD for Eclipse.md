
# Configuring ITF DTD for Eclipse

* * *

ITF test cases can be defined using XML. To provide ability to easily create those test cases ITF comes with dtd file that defines the XML content and provide huge help in writing the XML. In this chapter you will learn how to configure the dtd.

You will find the dtd file (AutomatedTestingXMLSchemaXX.dtd) in the distribution zip under itf\\schema\\ directory. Place this file somewhere available to the IDE of your choice. The common place is \\itf\\schema directory (create if not exists) in you IIQ build.

## Eclipse configuration for TestCase dtd

Open preferences (Window->Preferences) and select XML -> XML Catalog setting.

![eclipse dtd 1.png](assets%2Fimages%2Feclipse%20dtd%201.png)

Select Add… button on the right.

![eclipse dtd 2.png](assets%2Fimages%2Feclipse%20dtd%202.png)

Location field should point to where you placed the dtd file.

Select Key type to Public ID.

Key should be `"-//amidentity//DTD TestCase 2.0//EN"`

Press OK and Apply and Close on the Preferences window. DTD configuration is now finished and will be referenced when you place following heading in you test case file:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<!DOCTYPE TestCase PUBLIC "-//amidentity//DTD TestCase 2.0//EN" 
"http://www.amidentity.com/dtd/AutomatedTestingXMLSchema20.dtd">
```

## Eclipse configuration for resource object dtd

Perform similar procedure to configure dtd for resource object xml files.

Use Key: `"-//amidentity//DTD Mocked ResourceObject 2.0//EN"`

Use following header for mocked resource objects xml files:

```java
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE MockedAccount PUBLIC "-//amidentity//DTD Mocked ResourceObject 2.0//EN" 
"http://www.amidentity.com/dtd/MockedRecourceObjectXMLSchema20.dtd">
```