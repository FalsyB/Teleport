Setup of Teleport (Reloaded)
--------------------------

**Teleport (Reloaded)** requires Android Wear and Android Mobile libraries. Depending on which library you are working with you will need different references.

###Mobile

For the mobile version, Teleport can run from version 11 of Android but it is compiled using Android Lollipop 5.1.1 compiler:
```java
    minSdkVersion 11
    targetSdkVersion 23
```
###Wear

For your wear device, Teleport requires Google Play Service 8.3, *earlier versions will not work*:

    compile 'com.google.android.gms:play-services-wearable:8.3.0'
##Install using Maven
I have published this release of Teleport on my Maven repository available here:
https://bintray.com/raffaeu/maven/Teleport/
I kept the same package name and library name so that you don't have to change anything during upgrade except the references.

###01 - Add Reference to your project
Inside your build.gradle file (*the one referring to your module and not the project one*) create a new repositories node and include my Maven URL:
```java
    apply plugin: 'com.android.application'
    
    // here include my URL
    repositories{
        maven{
            url "http://dl.bintray.com/raffaeu/maven"
        }
    }
```
Then add a reference to the new Teleport library (*the name is different because I don't use JCentral*):
```java
    dependencies {
        compile 'com.mariux.teleport.lib:teleportlib:0.1.9'
    } 
```
If you want to use the latest version you can simply replace **o.1.9** with a **+** but considering that this will be a new library and there are plenty of **uncompleted** APIs I would suggest you to keep using the version number and check the breaking changes between versions.
###02 - Add Reference to Google Play
Teleport (Reloaded) requires the latest version of Google Play Services (*at the moment 8.3*). In order to get Teleport working you must reference the gsm service on your project as following:

    compile 'com.google.android.gms:play-services-wearable:8.3.0'
###03 - Add Google Play Services metatag
This metatag should be included into your **Application** TAG and it is required by Android to locate the correct gsm service channel:
```xml
    <application
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        <meta-data
            android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
```
[Go back to homepage](https://github.com/raffaeu/Teleport/blob/master/README.md)
