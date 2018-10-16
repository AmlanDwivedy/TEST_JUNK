# Hand gesture Android App


### Overview
This app performs Object hand gestures on live camera feed and displays the results in real-time on the screen.

## Screenshots
 
![picture](screenshot/device-2018-10-15-171752.png)
![picture](screenshot/device-2018-10-15-171241.png)

## Build the demo using Android Studio                      

### Prerequisites

* If you don't have already, install **[Android Studio](https://developer.android.com/studio/index.html)**, following the instructions on their website.
* You have to generate model file using following **[doc](../../ml/gesture_recognition/tfjs_tflite_conversion.md)** 
  


### Building
* Open Android Studio, and from the Welcome screen, select Open an existing Android Studio project.

* From the Open File or Project window that appears, navigate to and select the tensorflow-lite/android/image_classification directory from wherever you cloned the TensorFlow-Lite sample GitHub repo. Click OK.

* If it asks you to do a Gradle Sync, click OK.

* You may also need to install various platforms and tools, if you get errors like "Failed to find target with hash string 'android-23' and similar.
Click the Run button (the green arrow) or select Run > Run 'android' from the top menu. You may need to rebuild the project using Build > Rebuild Project.

* If it asks you to use Instant Run, click Proceed Without Instant Run.

* Also, you need to have an Android device plugged in with developer options enabled at this point. See **[here](https://developer.android.com/studio/run/device)** for more details on setting up developer devices.
* Read the following **[doc](../../ml/gesture_recognition/tfjs_tflite_conversion.md)** to generate tflite model file.
* Now once you get the tflite file put that into assets folder.

