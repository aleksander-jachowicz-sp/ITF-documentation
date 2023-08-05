
# Configuring ITF libraries for IntelliJ

* * *

## Add ITF client and supporting libraries

1. Open project structure dialog.
2. Select Modules (1), select your IdentityIQ Module and Sources(2) tab. Mark `itf/resources` (3) folder as Resources (icons above the list) and `itf/test` (4) as Tests.
    ![intellij libraries1.PNG](assets%2Fimages%2Fintellij%20libraries1.PNG)
3. Select Libraries (1) settings on the left and Select plus ![(plus)](/wiki/s/2048552623/6452/f35647b150fb78f879d5fc2efe77adc68a52833d/_/images/icons/emoticons/add.png) sign above the Libraries list. This will pop up file selection window.
    ![intellij libraries2.PNG](assets%2Fimages%2Fintellij%20libraries2.PNG)
4. And navigate to lib directory and select all ITF jars and click OK.
    ![intellij libraries3.PNG](assets%2Fimages%2Fintellij%20libraries3.PNG)
5. (Optional) Rename the library to ITF.
6. Make sure that this newly created library is assigned as dependency to you main Module. If not add manually.

![intellij libraries4.PNG](assets%2Fimages%2Fintellij%20libraries4.PNG)

## Add ITF dependency libraries

1. Open project structure dialog.
2. Select Libraries (1) settings on the left and Select plus ![(plus)](/wiki/s/2048552623/6452/f35647b150fb78f879d5fc2efe77adc68a52833d/_/images/icons/emoticons/add.png) sign above the Libraries list. This will pop up file selection window.
    ![intellij libraries5.PNG](assets%2Fimages%2Fintellij%20libraries5.PNG)
3. Select `iftDependencies` directory in `itf` folder from home of your SSB directory.
    ![intellij libraries6.PNG](assets%2Fimages%2Fintellij%20libraries6.PNG)
4. Select OK when asked about adding library to your main module.
    ![intellij libraries7.PNG](assets%2Fimages%2Fintellij%20libraries7.PNG)
5. Check that your newly added ITF dependencies are below your IIQ libraries in Module Dependencies.
    ![intellij libraries8.PNG](assets%2Fimages%2Fintellij%20libraries8.PNG)