# Facedetection
OpenCV for face detection

# Implement face detection sample on Android Studio
- Using the OpenCV4Android SDK
OpenCV4Android SDK package enables development of Android applications with use of OpenCV library.
The structure of package contents looks as follows:
```sh
OpenCV-2.4.13-android-sdk
|_ doc
|_ samples
|_ sdk
|    |_ etc
|    |_ java
|    |_ native
|          |_ 3rdparty
|          |_ jni
|          |_ libs
|               |_ armeabi
|               |_ armeabi-v7a
|               |_ x86
|
|_ LICENSE
|_ README.android
``` 
- Modify the Android.mk and Application.mk
You should choose the right hardware platform version, just like armeabi-v7a etc.
```sh
LOCAL_PATH := $(call my-dir)

include $(CLEAR_VARS)

OPENCV_CAMERA_MODULES:=on
OPENCV_INSTALL_MODULES:=on
OPENCV_LIB_TYPE:=SHARED
ifdef OPENCV_ANDROID_SDK
  ifneq ("","$(wildcard $(OPENCV_ANDROID_SDK)/OpenCV.mk)")
    include ${OPENCV_ANDROID_SDK}/OpenCV.mk
  else
    include ${OPENCV_ANDROID_SDK}/sdk/native/jni/OpenCV.mk
  endif
else
  include $(LOCAL_PATH)/../java/native/jni/OpenCV.mk
endif

LOCAL_SRC_FILES  := DetectionBasedTracker_jni.cpp
LOCAL_C_INCLUDES += $(LOCAL_PATH)
LOCAL_LDLIBS     += -llog -ldl

LOCAL_MODULE     := detection_based_tracker



include $(BUILD_SHARED_LIBRARY)
``` 
```sh
APP_STL := gnustl_static
APP_CPPFLAGS := -frtti -fexceptions
APP_ABI := armeabi-v7a
APP_PLATFORM := android-23
```
- Set up the AIDL configuration file
Moving the OpenCVEngineInterface.aidl from java/org/opencv/engine/ to /src/main/aidl/engine/
This Class provides a Java interface for OpenCV Engine Service. It's synchronous with native OpenCVEngine class.
- Config the NDK build
Add some source in the file of build.gradle (app/src)
```sh
//禁止自带的ndk功能
    sourceSets.main.jniLibs.srcDirs = ['src/main/libs']

    externalNativeBuild{
        ndkBuild {
            path "src/main/jni/Android.mk"
        }
    }
```
- Camera Permission Setting
Add the Camera Permission into the AndroidManifest.xml
```sh
<uses-permission android:name="android.permission.CAMERA"/>

    <uses-feature android:name="android.hardware.camera"/>
    <uses-feature android:name="android.hardware.camera.autofocus"/>
    <uses-feature android:name="android.hardware.camera.front"/>
    <uses-feature android:name="android.hardware.camera.front.autofocus"/>
```
Modify the FdActivity java source to add the verification of Camera permission
```sh
...
private void verifyCameraPermission() {
        int permission = checkSelfPermission(Manifest.permission.CAMERA);
        if (permission != PackageManager.PERMISSION_GRANTED){
            requestPermissions(new String[] {Manifest.permission.CAMERA}, 1);
        }
    }
...
public void onCreate(Bundle savedInstanceState) {
        Log.i(TAG, "called onCreate");
        super.onCreate(savedInstanceState);
        getWindow().addFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);

        setContentView(R.layout.face_detect_surface_view);

        //add camera permission
        verifyCameraPermission();

        mOpenCvCameraView = (CameraBridgeViewBase) findViewById(R.id.fd_activity_surface_view);
        mOpenCvCameraView.setVisibility(CameraBridgeViewBase.VISIBLE);
        mOpenCvCameraView.setCvCameraViewListener(this);
    }
...
```
# Install the application after finishing building
## Photography detection
![Sample Show](http://img.blog.csdn.net/20170406161027197?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGVsaTAwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

## Video detection
![Sample Show](/image_for_readme/1.jpg)
![Sample Show](/image_for_readme/2.jpg)
![Sample Show](/image_for_readme/3.jpg)
![Sample Show](/image_for_readme/4.jpg)
![Sample Show](/image_for_readme/5.jpg)
