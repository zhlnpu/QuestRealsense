# QuestRealsense_NewVersion
 Use RS camera in Quest platform.
 This is a new version targeted for Quest 2, with newer dependencies. 
 
# [Old version is here](https://github.com/zhlnpu/QuestRealsense/tree/Quest_Gen_1).

|items| Version|
|--|--|
| Unity| 2019.3.1f1
|Headset| Quest 2
| Camera |SR300, D415/435
|SDK | API 25,29
|Gradle| [6.5 ](https\://services.gradle.org/distributions/gradle-6.5-all.zip) |
|NDK| 21|
| JDK| jdk1.8.0_271, or so|
|librealsense| [2.44](https://github.com/IntelRealSense/librealsense/tree/v2.44.0)|


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


### Pre-requirement
1. Working network can go to google.
2. Download [librealsense](https://github.com/IntelRealSense/librealsense)
3. Use Android Studio to open the project "librealsense/wrappers/android/"
4. After that, commandline go to "librealsense/wrappers/android/"
5. Generate aar file by running command below. [Refer to here](https://github.com/GeorgeAdamon/quest-realsense#step-2-building-the-librealsenseaar-android-library).
![image](https://user-images.githubusercontent.com/55628470/113701046-32a3e100-970a-11eb-9878-8508cf9767a8.png)
5. If see error like this:
![image](https://user-images.githubusercontent.com/55628470/113701220-726ac880-970a-11eb-9461-aecdc0d7b8d0.png)
go to the file and change the Gradle version to 6.5
6. If the build is succesful, the generated .aar file will be located in <librealsense_root_dir>/wrappers/android/librealsense/build/outputs/aar.
7. change the name of aar file to "librealsense.aar"

### Unity configurations
1. The generated librealsense.aar file should be placed inside your Unity project, in the Assets / RealSenseSDK2.0 / Plugins directory, alongside the Intel.RealSense.dll and librealsense2.dll. A succesful setup should look like this:
![image](https://user-images.githubusercontent.com/55628470/113701491-ce355180-970a-11eb-889f-2b8f0a2f14f9.png)
2. Configure NDK, SDK, Gradle, JDK
![image](https://user-images.githubusercontent.com/55628470/113700583-97ab0700-9709-11eb-97e0-f0b03c6ff0a4.png)

3. set environment variables for JDK and Gradle
![image](https://user-images.githubusercontent.com/55628470/113701845-41d75e80-970b-11eb-941f-e325b5b45095.png)

![image](https://user-images.githubusercontent.com/55628470/113701756-2704ea00-970b-11eb-887b-67195ab8deaf.png)

4. Set player attributes:
![image](https://user-images.githubusercontent.com/55628470/113701959-71866680-970b-11eb-991b-1251d26ed1e4.png)
![image](https://user-images.githubusercontent.com/55628470/113702310-efe30880-970b-11eb-876b-042cb07c5c21.png)
![image](https://user-images.githubusercontent.com/55628470/113702334-f5d8e980-970b-11eb-9a41-e4a5704ffa37.png)
![image](https://user-images.githubusercontent.com/55628470/113702383-025d4200-970c-11eb-9802-ef795e673608.png)

5. Set aar parameters
![image](https://user-images.githubusercontent.com/55628470/113702619-52d49f80-970c-11eb-841c-b23a8869b347.png)
Corresponding error:
"allowBackup=true error"

6. Make sure all other parts are correct. Make sure the material of RScamera is "Pointcloud" rather than "PointcloudGeom".

If point cloud is still missing, try adding OnEnable() in Start function in RSDevice.cs, since some functions cannot be auto called in Android Platform (On Win, RS camera intializatioin is auto called)
```
public class RsDevice : RsFrameProvider
{

    void Start()
    {
        OnEnable();
    }

    /// <summary>
    /// The parallelism mode of the module
    /// </summary>
    public enum ProcessMode
    {
        Multithread,
        UnityThread,
    }
```

### Initializing the RsContext Java class from Unity
Now that all the libraries are in place, before actually being able to access the RealSense camera, we need a C# script that performs two crucial jobs:

Initializes a new instance of the Java class RsContext
Makes sure that Android Camera Permissions are explicitly requested from the user, if not provided already.
Attaching the following script to any GameObject in your Scene, would ensure that those two operations are executed in the beginning of your application:
```
using UnityEngine;

public class AndroidPermissions : MonoBehaviour
{
#if UNITY_ANDROID && !UNITY_EDITOR
    void Awake()
    {
        if (!UnityEngine.Android.Permission.HasUserAuthorizedPermission(UnityEngine.Android.Permission.Camera))
        {
            UnityEngine.Android.Permission.RequestUserPermission(UnityEngine.Android.Permission.Camera);

        }

        using (var javaUnityPlayer = new AndroidJavaClass("com.unity3d.player.UnityPlayer"))
        using (var currentActivity = javaUnityPlayer.GetStatic<AndroidJavaObject>("currentActivity"))
        using (var rsContext = new AndroidJavaClass("com.intel.realsense.librealsense.RsContext"))
        {
            Debug.Log(rsContext);
            rsContext.CallStatic("init", currentActivity);
        }
    }
#endif
}
```

### Using Quest-friendly shaders
As stated in the original discussion, if you are using any other XR mode apart from Multi-Pass Stereo, Geometry Shaders will not work on the Quest.

This means that if you try to load an example Unity project, such as the PointCloudDepthAndColor scene from the Unity samples, where the PointCloudMat material assigned to the PointCloudRenderer component is using by default the Custom/PointCloudGeom shader ( a geometry shader ), you will get an
OPENGL NATIVE PLUG-IN ERROR: GL_INVALID_OPERATION: Operation illegal in current state error.

Switching the shader of this material to the simple Custom/PointCloud shader should work like a charm!

Alternatively, you can switch your XR mode to Multi-Pass stereo.


### Configuration of Quest 2 and changes comapred with quest 1
1. Hand tracking is set in the ovrcamerarig (1st gen), but we should drag the OVRHandPrefab to handAnchor and select "quest 2" in ovrcamerarig settings (2nd gen).
2. If the controller is not recognised as the 2nd gen, manually set the variable "headset" in "OVRControllerHelper.cs" (OVRHandPrefab) to "activeControllerType = ControllerType.Quest2;" (line 81).
3. To use hand trackig,  the passthrough mode must be enabled in headset. On starting the application, will see a popup window that requires to enable passthrough mode. Tap the side of head two times as the popup window describes to enable it.

# Demo video
Please find it in the Repository.
