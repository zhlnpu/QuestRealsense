# QuestRealsense
 Use RS camera in Quest platform

1. Unity: 2019.3.1f1
2. Headset: rift S, quest, quest 2
3. Camera: SR300, D415/435
4. SDK, NDK, Gradle: https://mailnwpueducn-my.sharepoint.com/:f:/g/personal/zhl93_mail_nwpu_edu_cn/EldNAhoPy65Gse1CREszGpsBshYKm0pBhLtTH2mAngDKQQ?e=00O5Fc
5. JDK: jdk1.8.0_271, or so


## Key features:
1. The depth camera can be opened on the mobile VR platform.
2. A unity 3d project is provided, compatible with Oculus Quest/Quest 2, Rift/Rift S.
3. The depth camera includes SR300 and D400. Minor revision is needed for L500.
4. Add the AVPRO plugin
5. The default demo enables hand tacking and point cloud rendering.




## The whole project refers to:
https://github.com/GeorgeAdamon/quest-realsense

some other resources:
https://dev.intelrealsense.com/docs/android-build-of-the-intel-realsense-sdk-20
https://github.com/IntelRealSense/librealsense/tree/master/wrappers/android


## solutions for konwn issues within the tutorial

### realse aar file generation
The default android platform is 26, make the following changes to adapt to android 25 of quest:
1. change the gradle to version 5.1.1 in project settings (the minimun gradle version supported by unity is 5.1.1)
2. change gradle path to local and target to gradle 5.5.1 path
3. change project "librealsense" android version to 25 in two places of the project seetings
4. make sure the Android SDK, NDK, Gradle path are the same



### allowBackup=true error
Open generated aar lib with window zip program, open AndroidManifest.xml, change the value of android:allowBack to false, save and update the xml file in the zipped file.


### No poitn cloud renered in Quest app
First, make sure all other parts are correct. Make sure the material of RScamera is "Pointcloud" rather than "PointcloudGeom".

If point cloud is still missing, try adding OnEnable() in Start function in RSDevice.cs, since some functions cannot be auto called in Android Platform (On Win, RS camera intializatioin is auto called)


### Configuration of Quest 2 and changes comapred with quest 1
1. Hand tracking is set in the ovrcamerarig (1st gen), but we should drag the OVRHandPrefab to handAnchor and select "quest 2" in ovrcamerarig settings (2nd gen).
2. If the controller is not recognised as the 2nd gen, manually set the variable "headset" in "OVRControllerHelper.cs" (OVRHandPrefab) to "activeControllerType = ControllerType.Quest2;" (line 81).
3. To use hand trackig,  the passthrough mode must be enabled in headset. On starting the application, will see a popup window that requires to enable passthrough mode. Tap the side of head two times as the popup window describes to enable it.









# Demo video
Please find it in the Repository.
