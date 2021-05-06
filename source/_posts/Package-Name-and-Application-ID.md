---
title: Package Name & Application ID
date: 2017-12-06 11:10:37
tags: Android
---
# applicationId
## Get Define
```
BuildConfig.APPLICATION_ID
```
## Some feature
Can switch suffix by add
```
android{
    buildTypes{
        release {
            applicationIdSuffix ".release"
        }
        debug {
            applicationIdSuffix ".debug"
        }
    }  
}
```
in app's build.gradle

# packageName

## Get define
```
getApplicationContext().getPackageName()
```

The value will be overrided with applicationId

# Java package

The structure of a project, it's ok that differ from applicationId.
