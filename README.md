# Flutter App CI/CD to TestFlight

![App Logo](./assets/app_logo.png)

This repository serves as a demonstration of setting up continuous integration and continuous deployment (CI/CD) for a Flutter app called "flutter_app_testflight_cicd" to deploy it to TestFlight. With this CI/CD setup, your Flutter app can be automatically deployed to TestFlight for beta testing.

## Overview

In this repository, we showcase how to automate the deployment process of a Flutter app to TestFlight using Fastlane and a CI/CD service. The setup includes:

- Configuring Fastlane for iOS app deployment.
- Setting up a CI/CD pipeline to trigger deployments on code changes.
- Automating the upload of your "flutter_app_testflight_cicd" app to TestFlight.

Follow the steps below to get started with the CI/CD setup.

## Prerequisites

Before you begin, ensure you have the following prerequisites:

- Flutter Installed on your machine.
- A Flutter app that you want to deploy.
- An active Apple Developer account.
- Fastlane installed on your local development machine.
- A CI/CD service (e.g., GitHub Actions, Bitrise, CircleCI) integrated with your GitHub repository.

## Getting Started

1. **App creation**: Start by creating a new app on your local machine (here I named the app flutter_app_testflight_cicd, but you can name it whatever you want).

   ```shell
   flutter create flutter_app_testflight_cicd -e
   cd flutter_app_testflight_cicd

The -e flag means **empty** , since I am trying to keep this demo simple. This will create a simple hello world app.
- Now lets ensure we can run our app (here I will use ios simulator but you can pick any simulator you want)

    ```shell
    flutter pub get
    flutter run

you should see something like this 

![Fig 1](./screenshots/firstrun.png)

Next let add some finishing touches on our app to get it ready for build and release.

First lets find an icon for our app. I like to use [Icon Kitchen](https://icon.kitchen/). Choose an icon, then download. Save the generated `play_store_512.png` file in a root folder called assets (here I renamed it app_logo).

Now that we have an icon for our app, lets use it. We will use the following package s[flutter_launcher_icons](https://pub.dev/packages/flutter_launcher_icons) which simplifies the task of updating your Flutter app's launcher icon, and [rename](https://pub.dev/packages/rename) which helps you to change your flutter project's AppName and BundleId for different platform.

Let add flutter_launcher_icons to our `pubspec.yaml`

```
    flutter_launcher_icons: "^0.13.1"

    flutter_launcher_icons:
        android: true
        ios: true
        image_path: "assets/app_logo.png"
```




run `flutter pub get` to update the dependencies.

Now let set our launcher icon.

```
flutter clean && flutter pub run flutter_launcher_icons:main
```

You should see the following output

```
Cleaning Xcode workspace...                                      1,575ms
Cleaning Xcode workspace...                                      1,801ms
Deleting build...                                                    9ms
Deleting .dart_tool...                                               1ms
Deleting Generated.xcconfig...                                       0ms
Deleting flutter_export_environment.sh...                            0ms
Deleting ephemeral...                                                0ms
Deprecated. Use `dart run` instead.
Resolving dependencies... 
Got dependencies.
Building package executable... (1.5s)
Built flutter_launcher_icons:main.
This command is deprecated and replaced with "flutter pub run flutter_launcher_icons"
  ════════════════════════════════════════════
     FLUTTER LAUNCHER ICONS (v0.13.1)                               
  ════════════════════════════════════════════
  
• Creating default icons Android
• Overwriting the default Android launcher icon with a new icon

WARNING: Icons with alpha channel are not allowed in the Apple App Store.
Set "remove_alpha_ios: true" to remove it.

• Overwriting default iOS launcher icon with new icon
No platform provided

✓ Successfully generated launcher icons
```

Now remove the app from the simulator
![remove](./screenshots/remove_app.png)

and rerun 
```
flutter run
```

You should see the flutter icon being replaced by the new `app_logo.png` file.
![newicon](./screenshots/newicon.png)


Tired already??? we still have a few things to do.

Now remember the rename package `rename`, we need to add this globally with the following command.

```
flutter pub global activate rename
```

Then we need to rename our app to make sure its more human friendly (and not camelcase).

run 

```
flutter pub global run rename --appname "Flutter app TestFlight cicd"
```

You should see this output 

```
┌───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
│ #0   FileRepository.changeIosAppName (package:rename/file_repository.dart:251:12)
│ #1   <asynchronous suspension>
├┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄
│ 💡 IOS appname changed successfully to : Flutter app TestFlight cicd
└───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
┌───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
│ #0   FileRepository.changeMacOsAppName (package:rename/file_repository.dart:276:12)
│ #1   <asynchronous suspension>
├┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄
│ 💡 MacOS appname changed successfully to : Flutter app TestFlight cicd
└───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
┌───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
│ #0   FileRepository.changeAndroidAppName (package:rename/file_repository.dart:301:12)
│ #1   <asynchronous suspension>
├┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄
│ 💡 Android appname changed successfully to : Flutter app TestFlight cicd
└───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
┌───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
│ #0   FileRepository.changeLinuxAppName (package:rename/file_repository.dart:353:12)
│ #1   <asynchronous suspension>
├┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄
│ 💡 Linux appname changed successfully to : Flutter app TestFlight cicd
└───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
┌───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
│ #0   FileRepository.changeWebAppName (package:rename/file_repository.dart:409:12)
│ #1   <asynchronous suspension>
├┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄
│ 💡 Web appname changed successfully to : Flutter app TestFlight cicd
└───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
┌───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
│ #0   FileRepository.changeWindowsAppName (package:rename/file_repository.dart:418:12)
│ #1   <asynchronous suspension>
├┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄
│ 💡 Windows appname changed successfully to : Flutter app TestFlight cicd
```

And also lets update the bundleId

run 

```
 flutter pub global run rename --bundleId com.jamesmilord.flutterapptestflightcicd
```

You should see the following 

```
┌───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
│ #0   FileRepository.changeIosBundleId (package:rename/file_repository.dart:90:12)
│ #1   <asynchronous suspension>
├┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄
│ 💡 IOS BundleIdentifier changed successfully to : com.jamesmilord.flutterapptestflightcicd
└───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
┌───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
│ #0   FileRepository.changeMacOsBundleId (package:rename/file_repository.dart:132:12)
│ #1   <asynchronous suspension>
├┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄
│ 💡 MacOS BundleIdentifier changed successfully to : com.jamesmilord.flutterapptestflightcicd
└───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
┌───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
│ #0   FileRepository.changeAndroidBundleId (package:rename/file_repository.dart:175:12)
│ #1   <asynchronous suspension>
├┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄
│ 💡 Android bundleId changed successfully to : com.jamesmilord.flutterapptestflightcicd
└───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
┌───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
│ #0   FileRepository.changeLinuxBundleId (package:rename/file_repository.dart:218:12)
│ #1   <asynchronous suspension>
├┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄
│ 💡 Linux BundleIdentifier changed successfully to : com.jamesmilord.flutterapptestflightcicd
└───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
```

Lets delete rerun the app to make sure we see the changes reflected (so we don t have two app in the simulator since we changed the appname and bundle id)

```
flutter run
```

If you look at the `info.plist` file in the `ios/Runner` folder you should see the `CFBundleName` updated to the new appname you added with `rename` and new bundle id can also be seen in `project.pbxproj` file in `Runner.xcodeproj` folder.




