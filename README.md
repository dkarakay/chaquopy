# Development environment setup

(NOTE: This procedure has not been fully tested and may be incomplete.)

Install the following prerequisites:

* Java 8 or later, either on the `PATH` as `java`, or pointed to by `JAVA_HOME`.
* Python 2 and 3, on the `PATH` as `python2`/`python3` (on Linux) or `py` (on Windows).
* Android SDK, including the following packages:
   * Build Tools
   * CMake
   * NDK
   * SDK Platform version corresponding to `MIN_SDK_VERSION` in
     product/buildSrc/src/main/java/com/chaquo/python/Common.java.
* Crystax NDK 10.3.2

$CRYSTAX_DIR/sources/python must contain libraries and includes for the currently-supported
Python minor versions (differences in the micro version don't matter). If building against a
Python version which comes with Crystax, the Crystax copies can be used. Otherwise, generate
them using the process documented in target/README.md, or copy them from another machine where
this has already been done.

Create product/local.properties with the properties listed in product/runtime/build.gradle.


# Release procedure

## Runtime

Run `gradlew runtime:check`.


## Gradle plugin

Free up RAM if necessary. If restarting Android Studio, do it before starting the tests, as
this may kill the Gradle daemon.

On each supported workstation OS, run the following tasks with `gradlew -P
cmakeBuildType=Release`:

* `gradle-plugin:testPython`
* `gradle-plugin:testIntegration-X.Y` for each supported Android Studio version.

Remove any license key from pkgtest app, then test it for both Python 2 and 3 on the following
devices, with at least one app on each device being a clean install:

* API 18 emulator (earlier versions give "too many libraries" error (#5316)).
* targetSdkVersion emulator
* Any ARM device

On one of these devices, test on both Python 2 and 3 that the license notification and enforcement
works correctly.


## Demo apps

Run `gradlew gradle-plugin:writePom`.

Copy .jar and .pom from gradle-plugin/build/libs to Maven repository.

Run `demo/update_public.sh OLD_VER NEW_VER`, where `OLD_VER` is the label in *this* repository
from which the public repository was last updated. If the script reports any files which
require manual copying or merging (e.g. build.gradle), examine them and update the public
repository as necessary.

"Clean Project", then "Generate Signed APK" for both Python 2 and 3, and test all features on the
following devices, with at least one app on each device being a clean install:

* minSdkVersion emulator
* targetSdkVersion emulator
* Any ARM device

Update public/demo/CHANGELOG.md for demo app changes, and runtime/docs/sphinx/changelog.rst for
SDK changes.

Release apps on Google Play, updating description and screenshots if necessary.

Copy APKs to Maven repository.


## Documentation

If sphinx or javadoc have changed:

* Adjust VERSION.txt temporarily if rebuilding docs for an old version.
* Build and upload to server.
* If major.minor version number has changed:
  * Update "current" symlink.
  * Add link on WordPress documentation page.


## Source control

Commit public/demo, public/console and public/hello repositories, and push to chaquo.com and
GitHub.

Commit this repository, add version number tag, and push.

Increment python/VERSION.txt for next version number.


## User communication

Post blog entry to website and Facebook.

Update any GitHub issues.

Email any affected users, including anyone who commented or gave a thumbs up to a related
issue.
