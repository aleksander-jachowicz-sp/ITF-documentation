
# ITF Installation and configuration

* * *

## Distribution zip content

ITF is distributed as a zipped file. You should have received a zip file called `IIQTester_Release-x.y.zip` , where x is major version and y is minor version.

Once you unzip the distribution file, you will find the following directories:

1. `lib` folder contains jars that are needed by client(IDE) to invoke test cases on IIQ server using Junit approach.
2. `iiq_plugin` folder that contains IdentityIQ plugin zip file.
3. `intellij_plugin` folder that contains IDEA IntelliJ IDE plugin zip file.
4. `itf` folder that contains directory structure and ift configuration files that need to be copied to SSB directory. ITF dtd schema files are also stored in that folder.
5. `simulator` folder contains jar used to simulate provisioning in ITF.

## Configuring ITF

Disclaimer: The following documentation provides step-by-step instruction on how to configure ITF in an SSB-based build. 
If you are using different build, some steps may be different. In such a case, please contact us for assistance.

### Configuration with IntelliJ IDEA plugin version 2.0 and above

#### Simple configuration to run test cases

This is a new and simple 2 step configuration to run ITF test cases in IntelliJ IDEA.

1. [Deploy ITF IdentityIQ plugin.md](Deploy%20ITF%20IdentityIQ%20plugin.md)
2. [Install IntelliJ ITF plugin](Install%20IntelliJ%20ITF%20plugin.md)
3. [Configure IIQ Servers](Configure_IIQ_Servers.md)

<div class="my_note">
If you are upgrading from a previous version, remove ITF jar libraries from the dependencies directory and 
lib directory. You can also remove itf.properties, log4j.properties, and the resources directory as they are 
no longer needed.
</div>

In case you want to also run test cases using Junit you will still need to do the old additional steps:

4. [Configuring ITF in the SSB build](Configuring%20ITF%20in%20the%20SSB%20build.md)

### Legacy configuration

* [Deploy ITF IdentityIQ plugin.md](Deploy%20ITF%20IdentityIQ%20plugin.md)
* [Configuring ITF in the SSB build](Configuring%20ITF%20in%20the%20SSB%20build.md)
* [Install IntelliJ ITF plugin](Install%20IntelliJ%20ITF%20plugin.md)
* [Configuring ITF Sever side logging](Configuring%20ITF%20Server%20side%20logging.md)
