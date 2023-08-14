# IdentityIQ Server Connectivity

* * *

ITF connects to IIQ server for two main purposes:

1. [Run ITF test](#running-itf-test-cases-on-iiq-server) cases on IIQ server.
2. [Import and export](#import-and-export-identityiq-objects-retrieve-commands) IdentityIQ objects, retrieve commands.

Each of these purposes uses a different mechanism to obtain IIQ server credentials.

## Running ITF test cases on IIQ server

<div class="my_info">
Because test cases may be run automatically, there may be no way for user to provide credentials. 
This is the reason why ITF uses a different mechanism to obtain IIQ server credentials for running test cases then for object manipulation.
</div>

### Running from IntelliJ runner

ITF IntelliJ plugin will search for IIQ server credentials in the following locations (in order of priority):

1. Current computer *.itf.properties file in the root directory of the [module](https://www.jetbrains.com/help/idea/creating-and-managing-modules.html) where the test case is located.
    1. ITF will try to locate `servers.properties` file (SSB) in the root directory of the module.
    2. When found, it will look inside for a property with the name that matches the name of your computer (run `hostname` command in Windows terminal to get computer name).
    3. When found, it will try to find properties file with the name: `[value of the property].itf.properties`. The Name of the computer is expected to be in the upper case in `servers.properties` file.
    4. When found, ITF expects credentials to be in this file and will use them.
2. Default itf.properties in directory `itf/resources/itf.properties` of the current [module](https://www.jetbrains.com/help/idea/creating-and-managing-modules.html). This is used when computer dedicated file is not found. 

### Running from IntelliJ Junit runner

When running a test case using Junit inside IntelliJ, ITF will obtain IIQ credentials from the following locations (in order of priority):

1. Current computer *.itf.properties file in the root of the current working directory.
    1. ITF will try to locate `servers.properties` file (SSB) in the root of the current working directory. By default, ehrn running Junit in IntelliJ this will be set to `$MODULE_WORKING_DIR$` which is the root of the current [module](https://www.jetbrains.com/help/idea/creating-and-managing-modules.html).
    2. When found, it will look inside for a property with the name that matches the name of your computer (run `hostname` command in Windows terminal to get computer name).
    3. When found, it will try to find properties file with the name: `[value of the property].itf.properties`. The Name of the computer is expected to be in the upper case in `servers.properties` file.
    4. When found, ITF expects credentials to be in this file and will use them.
2. If no computer-dedicated file is found, ITF will look for first available `itf.properties` file in classpath.
You should have [defined the Classpath](Configuring ITF libraries for IntelliJ.md#add-itf-client-and-supporting-libraries) (Step #3 during point #2) during installation.   

<div class="my_info">
Keep in mind that IntelliJ be default will build the project before running Junit test.
Built project will be placed in `out` directory of the project.
This means that if you depend on the default `itf.properties` file, IntelliJ will dynamically copy it to the `out` directory and the copy will be used.
</div>

#### IntelliJ Compiler output configuration

IntelliJ `out` configuration depends on two settings:
Project level setting:
![intellij out project configuration.png](assets%2Fimages%2Fintellij%20out%20project%20configuration.png)

Module level setting:
![intellij out module configuration.png](assets%2Fimages%2Fintellij%20out%20module%20configuration.png)

More details available [here](https://www.jetbrains.com/help/idea/configure-modules.html#module-compiler-output) in IntelliJ documentation. 

### Running from command line

In your CI / CD pipeline, you may want to run ITF test cases from the command line. Depending on your requirements, you have two options:

1. Keep your credentials in computer-dedicated `[computer_name].itf.properties` files. In this case IIQ url, login and password 
will be stored in clear text in the file. More over the settings in `servers.properties` file will have to mach computer name where the tests are running.
2. If you want your passwords to be more secure or computer names where you run the tests are unknown (or changing dynamically), 
you can use system properties. Please [contact us](mailto:contact@amidentity.com) for details.  

## Import and export IdentityIQ objects, retrieve commands