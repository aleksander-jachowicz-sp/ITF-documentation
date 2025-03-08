
# Configuring ITF Client side logging in IntelliJ IDEA

* * *

ITF Client side is where you invoke the test cases. You can monitor the progress of the test cases execution on the client side by enabling logging.
Three are 2 options for logging on the client side:
Log4j based logging and System.out based logging.

## Log4j based logging

To enable Log4j based logging, you need to configure the `log4j2.properties` file located in `itf/resources` directory to include:
```properties
logger.AutoTestingRestClient.name=com.itf.remote.client.AutoTestingRestClient
logger.AutoTestingRestClient.level=debug

logger.TestCaseResultHandler.name=com.itf.remote.client.TestCaseResultHandler
logger.TestCaseResultHandler.level=debug
```
Once the above configuration is done, you have to make sure to rebuild (Ctrl+F9) the project in IntelliJ IDEA to apply the changes.

## System.out based logging

To enable System.out based logging, you need to configure the `itf.properties` file located in `itf/resources` directory to include:
```properties
sys_out_logging=true
```
Once the above configuration is done, you have to make sure to rebuild (Ctrl+F9) the project in IntelliJ IDEA to apply the changes.

<div class="my_note">
For readability and ease of use, we recommend not to enable both types of logging at the same time.
</div>