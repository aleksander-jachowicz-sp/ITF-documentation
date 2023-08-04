
# Configuring ITF in the SSB build

* * *

The next step is to configure your IdentityIQ SSB build to use ITF.

## Copying library files

The source for all copy instructions is unzipped ITF distribution file.

1. Copy all jar files from `lib` directory to your SSB lib directory. If you already have JUnit configured in your build you can skip the JUnit jar.
2. Copy `ift` folder to the root directory of your SSB build.
3. Copy jar from `simulator` folder to web/WEB-INF/lib directory of SSB. Do this only if you wan to user simulator or check email commands.

| **NOTE**              |
|:----------------------|
| Before the simulator and email command will work you have to <br/>rebuild and redeploy you SSB and restart your server (Tomcat)|

## IIQ Server Settings

IFT needs to know the location and credentials to you IIQ servers to run test cases, download upload objects, download formatted commands etc. There are 2 places where it reads the connection data:

* To run test cases, ITF reads connection data from `itf.properties` file. Either from environment specific file (using SSB environment mechanism) or generic properties file in `ift/resources` directory.
* For all other operations (uploading, downloading, refreshing objects, generating test case commands) ITF uses mechanism known from DA (Deployment accelerator). IIQ connection data is read from environment target properties files from variables `%%ECLIPSE_URL%%`, `%%ECLIPSE_USER%%`, `%%ECLIPSE_PASS%%`.

**Test case running target configuration steps:**

Copy file `itf/resources/itf.properties` to the root folder of your SSB build and rename it to `[your environment name here].itf.properties`.
ITF uses SSB naming, so, if your environment in `servers.properties` is called something, 
use that name as prefix for copied `itf.properties` file.

Open your `sandbox.itf.properties`

```
itf_iiq_app_location=http://localhost:8080/identityiq
itf_iiq_username=spadmin
itf_iiq_password=admin
```

It contains settings used by ITF. Most likely, you will use the default values, but you may change them in case your environment is different.

`itf_iiq_app_location` is a url pointing to where your IdentityIQ server is installed. By default, it’s set to your local host. You can use ITF with any IIQ server as long as there is an http connection from your computer.

`itf_iiq_username` and `itf_iiq_password` are used by ITF to log in to IdentityIQ ITF web services.