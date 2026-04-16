# AI Glasses Development Guide

*A step-by-step guide for setting up and building AI Glasses apps using Android Studio and the Android XR emulator — controllable with the Mudra Band.*

---

## Using with Mudra Band

The [Mudra Band](https://mudra-band.com/) allows you to control the AI Glasses emulator using hand gestures.

### How It Works

The Mudra Band sends keyboard events to your machine, which are forwarded to the **Phone AVD**, which in turn passes them to the **AI Glasses AVD** — the same way a real paired phone would relay input to physical glasses.

### Setup

1. Connect the Mudra Band to your machine via **Bluetooth** or using the **Mudra Link app**.
2. Launch both AVDs and make sure they are paired (see [Pairing the AVDs](#pairing-the-avds)).
3. Click on the **Phone AVD** window to make sure it is in focus.
4. Your gestures will now control the AI Glasses emulator.

### Gesture Mapping

The Mudra Link app defines default gesture-to-key mappings out of the box. You can customize these mappings in the **Mudra Link app's Gesture Mapper** — assigning any gesture to any keyboard key to suit your app's needs.

### Examples

This template provides a blank starting point. To see working examples of Mudra Band-controlled UI, check out *(examples coming soon)*.

---

## Table of Contents
1. [Using with Mudra Band](#using-with-mudra-band)
2. [Prerequisites & Setup](#prerequisites--setup)
3. [SDK Setup](#sdk-setup)
4. [Quick Start](#quick-start)
5. [Creating an AI Glasses Project](#creating-an-ai-glasses-project)
6. [Project Dependencies & Gradle Config](#project-dependencies--gradle-config)
7. [Android Virtual Device (AVD) Creation](#android-virtual-device-avd-creation)
8. [Running the App](#running-the-app)
9. [Developer Tips](#developer-tips)

---

## Prerequisites & Setup

To develop for AI Glasses, you must use **Android Studio Canary**.

1. **Download Android Studio Canary:**
   - Navigate to the [Android Studio Preview release page](https://developer.android.com/studio/preview).
   - Download the latest **Canary** build *(Note: Do not use the Stable or Beta builds)*.
   - Install the application.

---

## SDK Setup

### 1. Install SDK 36

1. Open **Android Studio Canary**.
2. Click on **Settings** in the bottom left corner.
3. Navigate to **Languages & Frameworks > Android SDK**.

![SDK Settings](images/SDK.png)

4. Under the **SDK Platforms** tab, ensure you have installed an SDK with a minimum **API Level of 36**.
5. Switch to the **SDK Tools** tab and check the boxes for the following components:
   - **Android SDK Build-Tools**
   - **Android Emulator**
   - **Android SDK Platform-Tools**
   - **Layout Inspector image server for API 31-36**

![SDK Tools](images/SDK_Tools.png)

6. Click **Apply** to download and install the components.

---

## Quick Start

Now that your environment is ready, you can either clone the pre-configured repo or set up a project manually.

If you don't want to configure a project manually from scratch, you can clone the pre-configured repo:
1. Go to https://github.com/wearable-devices/AndroidAIGlassesTemplate
2. Copy the repo URL and run: `git clone https://github.com/wearable-devices/AndroidAIGlassesTemplate.git`
3. Open the cloned folder in Android Studio Canary.

After cloning, rename the project to match your app:

4. **App name:** Open `app/res/values/strings.xml` and update the `app_name` value.
![Rename app name](images/Rename_app_name.png)
5. **Package name:** In Android Studio, right-click the `com.example.template` package → **Rename**.
![Rename package](images/Rename_Package.png)
A pop-up window will open. Click All Directories.
![All_Directories](images/All_Directories.png)
Choose your new package name and Click **Refactor**. Android Studio will update all references automatically.
![Package_Refactor](images/Package_Refactor.png)
6. **Application ID:** Open `Gradle Scripts/build.gradle.kts (Module: App)` and update the `applicationId` field to match your new package name.
![Application_ID](images/Application_Id.png)
7. Run your project.

*(Note: If you clone this repo, you can skip straight to **[Android Virtual Device (AVD) Creation](#android-virtual-device-avd-creation)**, as the code and dependencies are already configured!)*

---

## Creating an AI Glasses Project
1. In Android Studio Canary, click on **File > New > New Project**.
2. On the template list go to  **XR**.
3. Choose **Basic AI Glasses Activity**

![Project Template](images/Project_Template.png)

4. Click **Next**
5. Click **Finish**

![Project Template](images/Project_Defaults.png)

---

## Project Dependencies & Gradle Config

Update the project dependencies to match the required AI Glasses libraries:

### libs.versions.toml

1. Under the **Gradle Scripts** group, open `libs.versions.toml`.
2. In the `[versions]` block, update the existing variables to the following versions:

```toml
glimmer = "1.0.0-alpha08"
projected = "1.0.0-alpha05"
```

3. At the bottom of the `[versions]` block, add:

```toml
xrRuntime = "1.0.0-alpha11"
xrArcore = "1.0.0-alpha11"
```

4. In the `[libraries]` block, add the following dependencies:

```toml
androidx-xr-runtime = { group = "androidx.xr.runtime", name = "runtime", version.ref = "xrRuntime" }
androidx-xr-arcore = { group = "androidx.xr.arcore", name = "arcore", version.ref = "xrArcore" }
```

### app/build.gradle.kts

1. Open the module-level `build.gradle.kts` (Module: app).
2. Inside the `dependencies` block, add the new XR libraries you just defined:

```kotlin
dependencies {
    // ...existing dependencies...
    implementation(libs.androidx.xr.runtime)
    implementation(libs.androidx.xr.arcore)
}
```

3. Click **Sync Now** At the top of the page to download the new Gradle configuration.
![Sync Now](images/Sync_Now.png)

### AndroidManifest.xml

1. Open `app/manifests/AndroidManifest.xml`.
2. Locate the following attribute inside your `<activity>` tag (which is located inside `<application>`):

```xml
android:requiredDisplayCategory="@string/display_category_xr_projected" >
```

3. Remove the `@string/display_category_` prefix so the attribute explicitly uses the `"xr_projected"` string instead:

```xml
android:requiredDisplayCategory="xr_projected" >
```

---

## Android Virtual Device (AVD) Creation
In order to test and run your app on the emulator, you will need to create an **Android Virtual Device (AVD)** for both the phone and the AI Glasses.

### Host Phone AVD

Because AI Glasses act as a paired companion device, you must first create a standard mobile phone AVD to host the connection.

1. Go to **View -> Tool Windows -> Device Manager**
![Device Manager](images/Device_Manager.png)
2. Click **Create Virtual Device** (the `+` icon).
![Create Virtual Device](images/Add_AVD.png)
3. Click **Create Virtual Device**
4. Under the **Phone** category, select a modern device profile (like a Pixel 9 Pro).
![Phone Profile](images/Phone_Profile.png)
5. Click **Next**
6. Under the API selection dropdown choose **Show All**.
7. For the system image, select the **CANARY** (or API 36 / Baklava) build:
   - **For Mac (Apple Silicon):** Choose the `arm64-v8a` system image.
   - **For Windows / Intel Macs:** Choose the `x86_64` system image.
![System Image Selection](images/API_Canary.png)
8. Click **Finish**.

### AI Glasses AVD

Now, create the companion emulator for the glasses themselves:

1. Go to **View -> Tool Windows -> Device Manager**
![Device Manager](images/Device_Manager.png)
2. Click **Create Virtual Device** (the `+` icon).
![Create Virtual Device](images/Add_AVD.png)
3. Click **Create Virtual Device**
4. In the device category list, locate and select the **XR** category.
![XR Category](images/AI_Glasses_Profile.png)
5. Click **Next**
6. In the system image selection page leave everything as is.
![System Image Selection](images/AI_Glasses_System_Image.png)
7. Click **Finish**


### Pairing the AVDs

1. Launch **both** the Phone AVD and the AI Glasses AVD from your **Device Manager** by clicking the **Run** (Play) button.
2. Still in the **Device Manager**, click the three dots next to the **AI Glasses** AVD and click **Pair Glasses**.
![Pair Glasses](images/Pair_Glasses.png)
3. Choose your **Phone AVD** and click **Next**.
4. Follow the steps inside the **Companion App** on the **Phone AVD** to complete the pairing.

---

## Running the App

With your devices paired, you can now run your application on the emulator!

1. At the top of the Android Studio window, ensure your **app** module is selected in the run configuration dropdown.
2. For the target device, select the **Phone AVD** (the host device you just set up).
   *(Note: The app is installed and run on the host phone, which then drives the experience on the connected glasses).*
3. Click the green **Run** (Play) button.
4. Wait for the app to build and install. 
5. Press the **Power** button on the glasses emulator.
6. Press the **Launch** button on the **Phone** emulator.
![Run App](images/Run_App.png)

---

## Developer Tips

### 1. Removing White Corners and Borders

To remove the default white window background and rounded borders from your main UI:

**Make the Window Background Transparent:**  
In your `GlassesMainActivity.kt`'s `onCreate()` function, right before `setContent`, add:
```kotlin
window.setBackgroundDrawable(ColorDrawable(Color.TRANSPARENT))
```
![Remove White Corners](images/Remove_White_Corners.png)

**Remove Borders from your Compose Container:**  
When using Glimmer or Jetpack Compose `Surface` or `Card`, ensure you explicitly set the shape and border:
```kotlin
    Box(
        modifier = modifier
            .surface(focusable = false, border = null).fillMaxSize(),
        contentAlignment = Alignment.Center
    ) {
         // Your UI here
      }
```
![Remove Box Border](images/Remove_Box_Border.png)

### 2. Keep the Emulator Awake (Disable Sleep Timer)

To prevent the AI Glasses emulator from going to sleep while you are testing (without needing ADB commands), you need to add the `FLAG_KEEP_SCREEN_ON` layout flag specifically to the `ProjectedDisplayController`. 

Follow these steps:

1. Open your `GlassesMainActivity.kt`.
2. Add the `@OptIn(ExperimentalProjectedApi::class)` annotation to your `onCreate` function.
3. In `onCreate()`, use `lifecycleScope.launch` to create the `ProjectedDisplayController` and apply the keep-awake flag.
4. Close the controller in `onDestroy()`.

```kotlin
class GlassesMainActivity : ComponentActivity() {
    
    // 1. Declare the ProjectedDisplayController
    private var projectedDisplayController: ProjectedDisplayController? = null

    @OptIn(ExperimentalProjectedApi::class)
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        window.setBackgroundDrawable(ColorDrawable(android.graphics.Color.TRANSPARENT))

        // 2. Launch the projected display controller and add the keep-awake flags
        lifecycleScope.launch {
            try {
                projectedDisplayController = ProjectedDisplayController.create(this@GlassesMainActivity)
                projectedDisplayController?.addLayoutParamsFlags(android.view.WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON)
            } catch (e: Exception) {
                Log.e("GlassesApp", "Failed to create ProjectedDisplayController", e)
            }
        }
        
        setContent {
            // Your UI here
        }
    }

    // 3. Clean up the controller when the activity is destroyed
    override fun onDestroy() {
        super.onDestroy()
        projectedDisplayController?.close()
    }
}
```
