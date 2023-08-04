
# Configuring ITF libraries for IntelliJ

* * *

## Add ITF client and supporting libraries

1. Open project structure dialog.
2. Select Modules (1), select your IdentityIQ Module and Sources(2) tab. Mark `itf/resources` (3) folder as Resources (icons above the list) and `itf/test` (4) as Tests.
    ![](https://itestf.atlassian.net/wiki/download/thumbnails/266240094/IntellijModulesSources.PNG?version=1&modificationDate=1690353811216&cacheVersion=1&api=v2&width=646&height=555)
3. Select Libraries (1) settings on the left and Select plus ![(plus)](/wiki/s/2048552623/6452/f35647b150fb78f879d5fc2efe77adc68a52833d/_/images/icons/emoticons/add.png) sign above the Libraries list. This will pop up file selection window.
    ![](https://itestf.atlassian.net/wiki/download/attachments/266240094/Screenshot%202022-12-10%20171430.png?version=1&modificationDate=1690353811222&cacheVersion=1&api=v2)
4. And navigate to lib directory and select all ITF jars and click OK.
    ![](https://itestf.atlassian.net/wiki/download/attachments/266240094/Screenshot_20221210_052034.png?version=1&modificationDate=1690353811229&cacheVersion=1&api=v2)
5. (Optional) Rename the library to ITF.
6. Make sure that this newly created library is assigned as dependency to you main Module. If not add manually.

![](https://itestf.atlassian.net/wiki/download/attachments/266240094/Screenshot_20221211_123737.png?version=1&modificationDate=1690353811234&cacheVersion=1&api=v2)

## Add ITF dependency libraries

1. Open project structure dialog.
2. Select Libraries (1) settings on the left and Select plus ![(plus)](/wiki/s/2048552623/6452/f35647b150fb78f879d5fc2efe77adc68a52833d/_/images/icons/emoticons/add.png) sign above the Libraries list. This will pop up file selection window.
    ![](https://itestf.atlassian.net/wiki/download/thumbnails/266240094/Screenshot%202022-12-10%20171430.png?version=1&modificationDate=1690353811222&cacheVersion=1&api=v2&width=662&height=259)
3. Select `iftDependencies` directory in `itf` folder from home of your SSB directory.
    ![](https://itestf.atlassian.net/wiki/download/attachments/266240094/dependencies%20add%20intellij.png?version=1&modificationDate=1690357624016&cacheVersion=1&api=v2)
4. Select OK when asked about adding library to your main module.
    ![](https://itestf.atlassian.net/wiki/download/attachments/266240094/dependencies%20modul%20add%20ask.png?version=1&modificationDate=1690357722150&cacheVersion=1&api=v2)
5. Check that your newly added ITF dependencies are below your IIQ libraries in Module Dependencies.
    ![](https://itestf.atlassian.net/wiki/download/attachments/266240094/module%20dependencies%20intellij.png?version=1&modificationDate=1690357939633&cacheVersion=1&api=v2)