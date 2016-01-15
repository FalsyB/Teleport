#Welcome to Teleport (reloaded)
![Teleport Icon](https://github.com/raffaeu/Teleport/blob/master/doc/images/teleport_256.png)

Teleport Reloaded is a fork of the original Teleport Library for Android Wear built by Mario Viviani and available here: https://github.com/Mariuxtheone.
Since Mario is not available anymore I contacted him and decided to move forward and customize Teleport to include features like:

 - Message Brokering
 - Messages and DataItem priority (since google gms version 8.3)
 - Upgrade to the latest Google Play and Android version

The original Teleport project had also some major bugs related to Message Api not capable to send and receive a payload and to DataApi mixing multiple requests. All these bugs have been addressed and are available on this version of Teleport, this is why I decided to call it Teleport (Reloaded).

> Note: the project is still Open Source using Apache 2.0 license and I
> am **NOT** claiming any rights on this project but because since the version
> 8.1 of Google Play Service, the library Teleport stopped to work, I decided to upgrade it and keep it alive as a new fork of the original
> one.

----------

##Overview

Teleport (reloaded), like the original teleport, can be used in multiple ways:

 - You can use it to send Message and/or DataItem from your Phone to your Smartwatch and vice-versa
 - You can use it to create ListenerServices for both Messages and DataItems
 - You can use it together with Google Api Client to reduce the amount of boiler plate code required to open a communication between two devices

At the moment there are two major classes available:

 - `TeleportClient`
 This class is in charge of opening and closing a connection with Google Api Client plus is capable to send Messages and/or DataItems
 - `TeleportService`
 This class is in charge of providing the basic boilerplate code for a `WearableListenerService` class, including connection with the `GoogleApiClient` class and handling notifications

##Table of Contents
 - [Setup your project](https://github.com/raffaeu/Teleport/blob/master/doc/SETUP.md)
 - [TeleportClient inside an Activity or Fragment](https://github.com/raffaeu/Teleport/blob/master/doc/CLIENT.md)
 - [TeleportService](https://github.com/raffaeu/Teleport/blob/master/doc/SERVICE.md)
 - [Send and Receive Messages](https://github.com/raffaeu/Teleport/blob/master/doc/MESSAGES.md)
 - [Send and Receive DataItem](https://github.com/raffaeu/Teleport/blob/master/doc/DATASYNC.md)
 - Advanced Usage
 - [Send from a Service](https://github.com/raffaeu/Teleport/blob/master/doc/SEND_SERVICE.md)
 - Synchronization tips
 - Demo Code and Samples

##Quick Setup
If you don't want to jump into the documentation but you just want to have a quick look at how Teleport (Reloaded) 
works, there you go:

###01 - Setup a Reference using Maven
```java
apply plugin: 'com.android.application'

// here include my URL
repositories{
    maven{
        url "http://dl.bintray.com/raffaeu/maven"
    }
}
// add a reference
dependencies {
    compile 'com.mariux.teleport.lib:teleportlib:0.1.9'
} 
```

###02 - Setup a Teleport Client
```java
public class Activity extends AppCompactActivity{
     // create a new variable of type TeleportClient
     TeleportClient client = null;
     
    // create a new instance of the client
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_mobile);
    
        mTeleportClient = new TeleportClient(this);
    
    }
     
    // Start the Client together with the Activity
    @Override
    protected void onStart() {
        super.onStart();
        mTeleportClient.connect();
    }
    
    // Stop the Client when you are done
    @Override
    protected void onStop() {
        super.onStop();
        mTeleportClient.disconnect();    
    }  
}
```    

###03 - Send a Message or a DataItem
```java
/** 
* send a Message with two paramenters
* a key to identify the message and a byte[] which contains a payload
*/
teleportClient.sendMessage("My Message", myPayload);

/** 
* send a DataItem with two paramenters
* a key to identify the message and a T value
*/
teleportClient.syncString("myKey", "myValue");
```

###04 - Create a Listener Service to retrieve data
```java
public class MobileService extends TeleportService{

    @Override
    public void onCreate() {
        super.onCreate();
    
        // create a custom TeleportClient.OnSyncDataItemTask to receive DataItem
        setOnSyncDataItemTask(new ReceiveDataItemTask());
    
        // create a custom TeleportService.OnGetMessageTask to receive Messages
        setOnGetMessageTask(new ReadMessageTask());
    }
}
```

##Original License
Copyright 2014-2015 Mario Viviani

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.