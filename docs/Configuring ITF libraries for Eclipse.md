# Configuring ITF libraries for Eclipse

<https://itestf.atlassian.net/wiki/spaces/ITF/pages/266600450/Configuring+ITF+libraries+for+Eclipse>

* * *

1. Open your IIQ project properties, select JAVA Build Path(1) and then select Source(2) tab. Using Add Folder button (3), add ITF folders `ift/resources` and `itf/test` (4). You should have copied them previously from the unzipped distribution file.
    ![](https://itestf.atlassian.net/wiki/download/attachments/266600450/EclipseBuildPathSource.PNG?version=1&modificationDate=1690353710963&cacheVersion=1&api=v2)
2. Select Libraries tab (1) and click Add JARs... button (2).
    ![](https://itestf.atlassian.net/wiki/download/attachments/266600450/EclipseBuildPathLibraries.PNG?version=1&modificationDate=1690353710971&cacheVersion=1&api=v2)
3. Navigate to lib folder (1), and select all ITF jars (2). Then click OK (3) and Apply and Close (4). If you don’t see the jar file in the lib directory you must redo step “Copying files” above.
    ![](https://itestf.atlassian.net/wiki/download/attachments/266600450/EclipseBuildPathJarSelection.PNG?version=1&modificationDate=1690353710977&cacheVersion=1&api=v2)