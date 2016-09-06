---
layout: default
date:   2016-09-02 13:00:00 +0000
permalink: /:year/:month/:day/:title/
---
# How to export android library as .aar file

make changes in `build.gradle`, tell gradle - you project is a library

    apply plugin: 'com.android.library'

Execute Gardle task `assemble`

    gradle assemble

Find output in

    PROJECT / app / build / outputs / aar / app-release.aar

# Import `aar` library into project

if the name of the aar file is `maplib.aar`

* copy this file to your app `libs` dir

* build.gradle

```
allprojects {
    repositories {
        jcenter()
        flatDir {
            dirs 'libs'
        }
    }
}
```

* app/build.gradle

```
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.android.support:appcompat-v7:22.2.0'
    compile 'com.quadriq.maplib:maplib@aar'
}
```
