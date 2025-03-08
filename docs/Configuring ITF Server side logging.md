
# Configuring ITF Sever side logging

* * *

As you may have seen in demos or in the documentation, ITF has a feature to log messages on the server side. 
This feature is useful when you want to debug your test cases or see what is happening on the server side.

ITF uses log4j2 for logging. If you want to see the progress of the test cases on the server side, 
you need to configure log4j2 in your IIQ instance to log messages.

Insert the following lines into the `*.log4j2.properties` file in your IIQ build:

```properties
logger.AutomatedTester.name=com.itf.AutomatedTester
logger.AutomatedTester.level=debug

logger.AutomatedTestCase.name=com.itf.AutomatedTestCase
logger.AutomatedTestCase.level=debug

logger.TestCaseExecutionEnvironment.name=com.itf.engine.TestCaseExecutionEnvironment
logger.TestCaseExecutionEnvironment.level=debug
```

You will have to redeploy your IIQ instance for the changes to take effect.