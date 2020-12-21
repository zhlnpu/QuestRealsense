# QuestRealsense
 Use RS camera in Quest platform

1. Unity: 2019.3.1f1
2. Headset: rift S, quest, quest 2
3. Camera: SR300, D415/435

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
First, make sure all other parts are correct. Make sure the material of RScamera is "Pointcloud" rather than "Geom".

If point cloud is still missing, try adding OnEnable() in Start function in RSDevice.cs, since some functions cannot be auto called in Android Platform (On Win, RS camera intializatioin is auto called)


# Demo video
Please find it in the Repository.





