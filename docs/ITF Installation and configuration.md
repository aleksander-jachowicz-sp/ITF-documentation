
# ITF Installation and configuration

* * *

## Distribution zip content

ITF is distributed as a zipped file. You should have received a zip file called `IIQTester_Release-x.y.zip` , where x is major version and y is minor version.

Once you unzip the distribution file, you will find the following directories:

1. `lib` folder contains jars that are needed by client(IDE) to invoke test cases on IIQ server using Junit approach.
2. `doc` folder that contains documentation pdf.
3. `iiq_plugin` folder that contains IdentityIQ plugin zip file.
4. `intellij_plugin` folder that contains IDEA IntelliJ IDE plugin zip file.
5. `itf` folder that contains directory structure and ift configuration files that need to be copied to SSB directory. ITF dtd schema files are also stored in that folder.
6. `simulator` folder contains jar used to simulate provisioning in ITF.

## Configuring ITF

Disclaimer: The following documentation provides step-by-step instruction on how to configure ITF in SSB based build. If you are using different build, some steps may be different. In such case, please contact us for assistance.

* [Deploy ITF IdentityIQ plugin.md](Deploy%20ITF%20IdentityIQ%20plugin.md)
* [Configuring ITF in the SSB build](/wiki/spaces/ITF/pages/266436611/Configuring+ITF+in+the+SSB+build)
* [Install IntelliJ ITF plugin](/wiki/spaces/ITF/pages/266076182/Install+IntelliJ+ITF+plugin)