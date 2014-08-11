# Overview #

This skeleton structure allows you to use the Android flavors mechanism (described [here](http://tools.android.com/tech-docs/new-build-system/user-guide#TOC-Build-Variants)) to build variations of your app for all the supported platforms.

For example, you might have a Free app with advertising and a Paid app without. Using this framework you can go through the full development cycle with either version and keep the fileset as [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) as possible.

As of this writing, the framwork will work with libGDX from 1.0 to 1.3.

Feedback and improvements are solicited. Please email support+github@eggheadgames.com or add an issue or pull request. Released under an Apache 2.0 License. Enjoy.

# Multiple Application Support #

This repo framework currently builds 2 different applications for 3 platforms.

 * YourApp Free 
 * YourApp Paid 
 
Each of these is built for Google Play, Amazon and iOS, for a total of 6 variations.

## How to Configure It ##

The checked in files are a stub of a full gdx application with readme.txt files at the end of each branch. Most of them are there to browse, not to actually install.  This is the suggested installation strategy:

* make sure your existing libGDX checkout is fully commited to git or you have some other backup copy
* check out this repo somewhere else
* copy the pack folder into your existing libGDX app
* do a `grep -r example pack` to find all the places where `com.example.yourapp` appears and change them to match your app's package syntax (most will be in the `pack/build.gradle` file)
* copy the contents from your existing `android/assets` folder into `pack/src/main/assets`
* copy the contents from your existing `android/res` folder into `pack/src/main/res`
* copy the contents from your existing `ios/resources` into `pack/src/ios/main/resources`
* look at your existing `android/AndroidManifest.xml` file and your existing `android/build.gradle` file and make matching changes to your `pack/build.gradle` and `pack/AndroidManifest.xml` files.

*possibly some other things that are missing - please update this as needed!*

**Check again to be sure you've backed up your files. Running gradle will absolutely definitely DELETE FILES!**

You may wish to to a `git add *` to capture the current set of file changes.

Run `./gradlew :pack:prepFreeGoogle`.

After doing that, look at the git diffs. If everything went well, the `android/` folder will be much the same, though the `AndroidManifest.xml` file may have some comment lines in it, and, depending on how much configuration you did, it may now have some things wrong in it.

## How to Use It ##

In normal development, you'll work as normal (including Desktop and Android debugging). If you wish to test a specific variation, then you'll run a Gradle `:pack` task, refresh Eclipse (or wait for it to do so itself), then continue developing as normal. You should also be able to do a *clean* in Eclipse and everything should work.

Run one of the following gradle tasks to configure Android builds. 

    :pack:prepFreeGoogle
    :pack:prepFreeAmazon
    :pack:prepFreeIos
    :pack:prepPaidGoogle
    :pack:prepPaidAmazon
    :pack:prepPaidIos

I.e. the task is named: `:pack:prep<<TYPE>><<PLATFORM>>`, where:

 * `TYPE` is one of Free or Paid
 * `PLATFORM` is one of Google, Amazon or iOS


## How it Works ##

The magic for this is provided by the `pack` subdir. This relies heavily on the Android Gradle plugin support for [Build Variants and Product Flavors](http://tools.android.com/tech-docs/new-build-system/user-guide#TOC-Build-Variants). Essentially all the pieces (manifest settings, icons and data files) are arranged into separate subdirs under `pack/src` and they get assembled auto-magically into `pack/build`. The `pack/build.gradle` file creates a matching set of tasks to take those files and copy them over to appropriate places in the libGDX build structure.

Some notes:

 * Most of the build settings change in the file `pack/build.gradle`. That's where you'll set version numbers, packageName etc.
 * The tree `pack/src/main` contains the defaults for all auto-generated files. The various other `pack/src/*` are overlaid and cleverly merged as needed, with the results all placed in `pack/build` in several redundant ways. Most of the magic from this is provided by the Android gradle plugin. The iOS support is analagous and provided by the copy scripts in `pack/build.gradle` which build on the Android results.
 * `BuildConfig.java` is copied into the `core/src folder`. It's values are set in the `pack/build.gradle` file. This is very handy for having your app decide how to behave based on settings in the build file.
 * The Android file `android/gen/.../R.java` is actually ignored (for various complicated reasons to do with the package name and Eclipe's use of the Android `aapt` generator tool). The actual file used is `android/src/com/example/yourapp/android/R.java`, copied there by `pack`.
 * `AndroidManifest.xml` is auto generated from multiple pieces which are well documented within the file itself
 * There is a stub Android app in `plan` which can be ignored. It's there to keep Eclipse and Gradle happy.
 * A similar approach is used for the iOS configuration. It's source files live under `pack/src/main/ios` in an analogous fashion (i.e. with a `main` folder for shared files and then individual folders for specific files).
 * `pack/src/main/ios/robovm.properties` is filled in with the correct values and copied into the `ios/` folder.

It turns out the Gradle (and the Groovy language it's based on) is remarkably good for this type of work. If you're bored, check out the `pack/build.gradle` file to see how the tasks are declared in a parameterized way. This keeps things quite DRY, in keeping with the approach of flavors themselves.
