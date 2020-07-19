# Object Classification using Machine Learning

### Object Detection Android App using Teachable Machine and Tensorflow Lite

Last updated: 07/19/2020

## Overview

This tutorial provides step-by-step instructions on how to create an Android app using Google's Teachable Machine and Android Studio. By following the tutorial, you will be able to use your Android app to detect objects through supervised machine learning. 

This is an example application for [TensorFlow Lite](https://tensorflow.org/lite) on Android. It uses [Image classification](https://www.tensorflow.org/lite/models/image_classification/overview) to continuously classify objects it sees from the device's back camera. Inference is performed using the TensorFlow Lite Java API. The demo app classifies frames in real-time, displaying the top most probable classifications. 

For details of the model used, visit [Image classification](https://www.tensorflow.org/lite/models/image_classification/overview).

<!-- TODO(b/124116863): Add app screenshot. -->


### Tutorial Overview

1. Data collection
2. Train the model using collected image data
3. Export machine learning model
4. Incorporate model into an Android app

### Requirements

*   Android Studio 4.0 (installed on a Mac, Linux, or Windows machine)
*   Android device in [developer mode](https://developer.android.com/studio/debug/dev-options) with USB debugging enabled
*   USB cable (to connect Android device to your computer)

<br />

## Create and train model

### Step 1: Data collection

To train the machine learning model, we'll be using [Google's Teachable Machine 2.0](https://teachablemachine.withgoogle.com/train/image). Aside from Image Project, Teachable Machine also includes options Audio Project and Pose Project, which will not be covered in this tutorial.

Begin by deciding which objects you plan to train your model on. Then, you can collect your data in two ways: 

1. Take photos of each object using your phone camera, separate them into different files, and upload the appropriate file for each class. 
2. Use the webcam option under "Add Image Samples" for each class on the Teachable Machine website.

### Notes
*   Be sure to only include one object for each class (i.e. make sure there are no banana images in the apple class and vice versa). 
*   It is recommended that you take at least 50 image samples for each class and have at least 3 classes. 
*   Your image samples should also ideally be from different angles.

Click the pencil icon next to each Class # and rename the class.


### Step 2: Train the model using collected image data

Once you have collected all the data and created your classes, you will train your model. Click the "Train Model" button. You do not need to change any of the values under Advanced (you can keep the number of epochs, batch size, and learning rate as the default values). 

In the preview window, turn the input on and test out your model. Under the output, your model should be able to distinguish between the objects. If you would like to add more image samples, you will need to go through the process again to re-train your model.

<br />

## Build and run app

### Step 3: Export machine learning model

Now that you've trained your model, you'll need to export it. As the model will be used in an Android app, click on the Tensorflow Lite tab and download both the floating point and quantized models. When you download your floating point model, the folder should include two files: labels.txt and model_unquant.tflite. Similarly, in your quantized model folder, you should have labels.txt and model.tflite. 

Now, get the project from [Github](https://github.com/tensorflow/examples). Click the green code button and download ZIP. You will need to click through a number of folders (examples-master > lite > examples > image_classification > android > app > src > main > assets). In assets, copy the labels.txt, model.tflite, and model_unquant.tflite files (you only need to keep one labels.txt file) into assets. 


### Step 4: Incorporate model into an Android app

Now, copy the file path for the android folder. Open Android Studio and click "Open an existing Android Studio project." A window should pop up that says "Open File or Project." At the top of the window, copy paste the file path and click OK. 

Your screen should look like the one below. Double click on CameraActivity (found under app > java > org.tensorflow.lite.examples.classification), find (using Ctrl+f on Windows) the line of code that says ``private Model model = Model.QUANTIZED_EFFICIENTNET;``.

and change QUANTIZED to FLOAT so that the line of code now reads ``private Model model = Model.FLOAT_EFFICIENTNET;``.

Now, inside the tflite package under the org.tensorflow.lite.examples.classification folder, double click on the ClassifierFloatEfficientNet.java class and find the function getModelPath(). Inside this function, there should be a line that says ``return "efficientnet-lite0-fp32.tflite";``.

Change the file so that the code now reads ``return "model_unquant.tflite";``.

In the same class, find getLabelPath() and change the code to return ``"labels.txt"`` instead of ``"labels_without_background.txt"``. 

Now, open the ClassiferFloatMobileNet.java class. Again, change the return statement in the function getModelPath() to ``return "model.unquant.tflite"`` instead of the existing line of code.

Next, open the ClassifierQuantizedEfficientNet.java class and find the function getModelPath(). This time, we will change ``return "efficientnet-lite0-int8.tflite";`` to ``return "model.tflite";`` and change the return value in ``getLabelPath()`` to ``return "labels.txt";``.

Do the same for ClassifierQuantizedMobileNet.java. 


### Step 5: Clone the TensorFlow examples source code

Clone the TensorFlow examples GitHub repository to your computer to get the demo
application.

```
git clone https://github.com/tensorflow/examples
```

Open the TensorFlow source code in Android Studio. To do this, open Android
Studio and select `Open an existing project`, setting the folder to
`examples/lite/examples/image_classification/android`

<img src="images/classifydemo_img1.png?raw=true" />

### Step 6: Build the Android Studio project

Select `Build -> Make Project` and check that the project builds successfully.
You will need Android SDK configured in the settings. You'll need at least SDK
version 23. The `build.gradle` file will prompt you to download any missing
libraries.

The file `download.gradle` directs gradle to download the two models used in the
example, placing them into `assets`.

<img src="images/classifydemo_img4.png?raw=true" style="width: 40%" />

<img src="images/classifydemo_img2.png?raw=true" style="width: 60%" />

<aside class="note"><b>Note:</b><p>`build.gradle` is configured to use
TensorFlow Lite's nightly build.</p><p>If you see a build error related to
compatibility with Tensorflow Lite's Java API (for example, `method X is
undefined for type Interpreter`), there has likely been a backwards compatible
change to the API. You will need to run `git pull` in the examples repo to
obtain a version that is compatible with the nightly build.</p></aside>

### Step 7: Install and run the app

Connect the Android device to the computer and be sure to approve any ADB
permission prompts that appear on your phone. Select `Run -> Run app.` Select
the deployment target in the connected devices to the device on which the app
will be installed. This will install the app on the device.

<img src="images/classifydemo_img5.png?raw=true" style="width: 60%" />

<img src="images/classifydemo_img6.png?raw=true" style="width: 70%" />

<img src="images/classifydemo_img7.png?raw=true" style="width: 40%" />

<img src="images/classifydemo_img8.png?raw=true" style="width: 80%" />

To test the app, open the app called `TFL Classify` on your device. When you run
the app the first time, the app will request permission to access the camera.
Re-installing the app may require you to uninstall the previous installations.

## Assets folder
_Do not delete the assets folder content_. If you explicitly deleted the
files, choose `Build -> Rebuild` to re-download the deleted model files into the
assets folder.
