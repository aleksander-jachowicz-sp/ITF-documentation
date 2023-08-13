# ITF IntelliJ plugin functionalities

* * *

## DA Equivalent

As a SailPoint developer, you should be familiar with Eclipse DA(DeploymentAccelerator) plugin. It is a "must have" during IIQ development.
Because of that reason, our plugin contains the most essential functions of DA, just for IntelliJ platform.

The Plugin provides you with the ability to:

1. Upload IIQ Objects 
2. Download IIQ Objects
3. Refresh IIQ Objects
4. Compare IIQ Objects with a remote version

### Environments usage

All IIQ object operations can communicate with all your environments defined in `target.properties` files. 
Plugin will allow you to select any of the defined environments to interact with as long as you define the following 
properties inside each `target.properties` files:

| **Property name**  | **Description**          | Sample value                       |
|:-------------------|--------------------------|------------------------------------|
| `%%ECLIPSE_URL%%`  | Url of IdentityIQ server | `http://localhost:8080/identityiq` |
| `%%ECLIPSE_USER%%` | Name of the user         | spadmin                            |
| `%%ECLIPSE_PASS%%` | Plain text password      | admin                              |

<div class="my_note">
    Since password is stored in plain text and possibly being committed to git repository, do not use this for important environments, where access is restricted. 
</div> 

As long as these three values are provided, ITF plugin will read them and give you an ability to manipulate IIQ objects on that server.

### Upload IIQ Objects

To upload an XML object(s) from your build to IIQ server, right-click on the xml file (containing the object), select ITF (1), 
Deploy Artifact (2) and select environment (3).
![deploy object 1.png](assets%2Fimages%2Fdeploy%20object%201.png)
This option will only be available for files which are [recognized](#iiq-xml-object-files-recognition) as IdentityIQ XML object files.

Once the object is correctly uploaded, you will get a balloon confirmation.
![deploy object confirm.png](assets%2Fimages%2Fdeploy%20object%20confirm.png)

In a case or error during uploading, you will get a balloon error message with problem description. 
![deploy object error.png](assets%2Fimages%2Fdeploy%20object%20error.png)

You can select multiple objects at a time to upload.  

### Download IIQ Objects

To download an XML Object(s) from IIQ server and save it in XML file, 
right-click on directory where you want the file stored, select ITF (1), "Import Object from IIQ" (2) and select environment (3).
![download object 1.png](assets%2Fimages%2Fdownload%20object%201.png)

The Selection window will pop up, in which you need to select object type (1) and an object (2).
You can select multiple objects of the same type. You can use the "Object filter" field to search for an object by name.  
![download object select.png](assets%2Fimages%2Fdownload%20object%20select.png)

<div class="my_info">
Keep in mind that in an environment with a large number of objects, reading object list from IIQ server may take a few seconds.
It is possible to preload some of the object lists, but that requires customization (please contact us if interested). 
</div>

Once the object(s) is downloaded, a new XML file in the selected directory will be created with object content, and 
you will see a balloon confirmation with the name of the object.

![download object confirmation.png](assets%2Fimages%2Fdownload%20object%20confirmation.png)

<div class="my_info">
"Download objects" strips all ids, create and modify timestamps from downloaded objects.
</div>

Download option will only be available when directory is selected in the Project pane.

### Refresh IIQ Objects

Lets you refresh an IdentityIQ object(s) with the version from IIQ server.
This option is available for files which are [recognized](#iiq-xml-object-files-recognition) as IdentityIQ XML object files.

To refresh an object(s) from IIQ server, right-click on the xml file (containing the object) in Project pane and 
select ITF (1), Refresh Object (2) and select environment (3).

![refresh object.png](assets%2Fimages%2Frefresh%20object.png)

The Plugin will download the current version of the object from IIQ server and replace the content of the file with it.

### Compare IIQ Objects

Lets you compare an XML IdentityIQ object(s) with the version on IIQ server and apply changes to the local file one by one.
This option is available for files which are [recognized](#iiq-xml-object-files-recognition) as IdentityIQ XML object files.

To compare an object(s) with the version on IIQ server, right-click on the xml file (containing the object) in Project pane and
select ITF (1), Compare Object (2) and select environment (3).

![compare object.png](assets%2Fimages%2Fcompare%20object.png)

The Plugin will download the current version of the object from IIQ server 
and compare it with the local file in IntelliJ compare tool. 
Please follow the [IntelliJ instructions](https://www.jetbrains.com/help/idea/differences-viewer.html) for the compare tool 
to apply changes. 

![compare object intellij.png](assets%2Fimages%2Fcompare%20object%20intellij.png)

### IIQ XML Object files recognition

File must meet the following requirements to be recognized as IdentityIQ XML object file:

1. Must have `.xml` extension.
2. Must be a proper XML file.
3. Must contain `saipoint.dtd` in the signature.

## ITF helper functionalities

Soon...