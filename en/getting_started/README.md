# Getting Started

This topic explains how to get the *QGroundControl* source code and build it either natively or within a *Vagrant* environment.
It also provides information about optional or OS specific functionality.

## Daily Builds

If you just want to test (and not debug) a recent build of *QGroundControl* you can use the [Daily Build](https://docs.qgroundcontrol.com/en/releases/daily_builds.html). Versions are provided for all platforms.

## Source Code

Source code for *QGroundControl* is kept on GitHub here: https://github.com/mavlink/qgroundcontrol.
It is [dual-licensed under Apache 2.0 and GPLv3](https://github.com/mavlink/qgroundcontrol/blob/master/COPYING.md).

To get the source files:
1. Clone the repo (or your fork) including submodules:
   ```
   git clone --recursive -j8 https://github.com/mavlink/qgroundcontrol.git
   ```
2. Update submodules (required each time you pull new source code):
   ```
   git submodule update --recursive
   ```

> **Tip** Github source-code zip files cannot be used because these do not contain the appropriate submodule source code. You must use git!


## Build QGroundControl

### Native Builds

*QGroundControl* builds are supported for macOS, Linux, Windows, iOS and Android. *QGroundControl* uses [Qt](http://www.qt.io) as its cross-platform support library and uses [QtCreator](http://doc.qt.io/qtcreator/index.html) as its default build environment.

- **macOS:** v10.11 or higher
- **Ubuntu:** 64 bit, gcc compiler
- **Windows:** Vista or higher, [Visual Studio 2017 compiler](#vs) (64 bit)
- **iOS:** 10.0 and higher
- **Android:** Jelly Bean (4.1) and higher. Standard QGC is built against ndk version 19.
- **Qt version:** {{ book.qt_version }} **(only)** <!-- NOTE {{ book.qt_version }} is set in the variables section of gitbook file https://github.com/mavlink/qgc-dev-guide/blob/master/book.json -->

> **Tip** For more information see: [Qt 5 supported platform list](http://doc.qt.io/qt-5/supported-platforms.html).

<span></span>
> **Note** Native [CentOS Builds](../getting_started/CentOS.md) are also supported, but are documented separately (as the tested environment is different).

#### Install Visual Studio 2017 (Windows Only) {#vs}

The Windows compiler can be found here: [Visual Studio 2017 compiler](https://visualstudio.microsoft.com/vs/older-downloads/) (64 bit)

When installing, select *Desktop development with C++* as shown:

![Visual Studio 2017 - Select Desktop Environment with C++](../../assets/getting_started/visual_studio_select_features.png)


#### Install Qt

You **need to install Qt as described below** instead of using pre-built packages from say, a Linux distribution, because *QGroundControl* needs access to private Qt headers.

To install Qt:

1. Download and run the [Qt Online Installer](http://www.qt.io/download-open-source)
   - **Ubuntu:**
     - Set the downloaded file to executable using: `chmod +x`.
     - Install to default location for use with **./qgroundcontrol-start.sh.** If you install Qt to a non-default location you will need to modify **qgroundcontrol-start.sh** in order to run downloaded builds.
   - **Arch Linux:** Run the [setup script](https://github.com/mavlink/qgroundcontrol/blob/master/tools/setup/arch.sh) for all dependiencies **including Qt**.
1. In the installer *Select Components* dialog choose: {{ book.qt_version }}.

   > **Note** If the version needed is not displayed, check the archive (show archive and refresh).

   Then install just the following components:
   - **Windows**: *MSVC 2017 64 bit*
   - **MacOS**: *macOS*
   - **Linux**: *Desktop gcc 64-bit*
   - All:
     - *Qt Charts* <!-- and *Qt Remote Objects (TP)* -->
     - *Android ARMv7* (to build Android)
1. Install Additional Packages (Platform Specific)
   - **Ubuntu:** `sudo apt-get install speech-dispatcher libudev-dev libsdl2-dev`
   - **Fedora:** `sudo dnf install speech-dispatcher SDL2-devel SDL2 systemd-devel`
   - **Android:** [Qt Android Setup](http://doc.qt.io/qt-5/androidgs.html)
1. Install Optional/OS-Specific Functionality

   > **Note** Optional features that are dependent on the operating system and user-installed libraries are linked/described below.
     These features can be forcibly enabled/disabled by specifying additional values to qmake.

   - **Video Streaming/Gstreamer:** - see [Video Streaming](https://github.com/mavlink/qgroundcontrol/blob/master/src/VideoReceiver/README.md).



#### Building using Qt Creator

1. Launch *Qt Creator* and open the **qgroundcontrol.pro** project.
1. Select the appropriate kit for your needs:
  - **OSX:** Desktop Qt {{ book.qt_version }} clang 64 bit
    > **Note** iOS builds must be built using [XCode](http://doc.qt.io/qt-5/ios-support.html).
  - **Ubuntu:** Desktop Qt {{ book.qt_version }} GCC 64bit
  - **Windows:** Desktop Qt {{ book.qt_version }} MSVC2017 **64bit**
  - **Android:** Android for armeabi-v7a (GCC 4.9, Qt {{ book.qt_version }})
1. Build using the "hammer" (or "play") icons:

   ![QtCreator Build Button](../../assets/getting_started/qt_creator_build_qgc.png)

#### Build using qmake on CLI

Example commands to build a default QGC and run it afterwards:

1. Make sure you cloned the repository and updated the submodules before, see chapter *Source Code* above and switch into the repository folder:
   ```
   cd qgroundcontrol
   ```
1. Create and enter a shadow build directory:
   ```
   mkdir build
   cd build
   ```
1. Configure the build using the qmake script in the root of the repository:
   ```
   qmake ../
   ```
1. Run make to compile and link. To accelerate the process things you can use the -j{number of threds} parameter.
   ```
   make -j12
   ```
1. Run the QGroundcontrol binary that was just built:
   ```
   ./staging/QGroundControl
   ```

### Vagrant

[Vagrant](https://www.vagrantup.com/) can be used to build and run *QGroundControl* within a Linux virtual machine (the build can also be run on the host machine if it is compatible).

1. [Download](https://www.vagrantup.com/downloads.html) and [Install](https://www.vagrantup.com/docs/getting-started/) Vagrant
1. From the root directory of the *QGroundControl* repository run `vagrant up`
1. To use the graphical environment run `vagrant reload`

### Additional Build Notes for all Supported OS

* **Parallel builds:** For non Windows builds, you can use the `-j#` option to run parellel builds.
* **Location of built files:** Individual build file results can be found in the `build_debug` or `build_release` directories. The built executable can be found in the `debug` or `release` directory.
* **If you get this error when running _QGroundControl_**: `/usr/lib/x86_64-linux-gnu/libstdc++.so.6: version 'GLIBCXX_3.4.20' not found.`, you need to either update to the latest *gcc*, or install the latest *libstdc++.6* using: `sudo apt-get install libstdc++6`.
* **Unit tests:** To run the [unit tests](../contribute/unit_tests.md), build in `debug` mode with `UNITTEST_BUILD` definition, and then copy `deploy/qgroundcontrol-start.sh` script into the `debug` directory before running the tests.


## Building QGC Installation Files

You can additionally create installation file(s) for *QGroundControl* as part of the normal build process.

> **Note** On Windows you will need to first install [NSIS](https://sourceforge.net/projects/nsis/).

To add support for installation file creation you need to add `CONFIG+=installer` to your project file, or when you call *qmake*.

To do this in *Qt Creator*:
- Open **Projects > Build > Build Steps > qmake > Additional arguments**.
- Enter `CONFIG+=installer` as shown:
  ![Installer](../../assets/getting_started/qt_project_installer.png)

