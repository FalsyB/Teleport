#Service Setup
##Create a new Service
A `TeleportService` is a class that extends `WearableListenerService`. It contains an internal reference to 
a `TeleportClient` and it exposes all methods available from the client to send messages and sync data.

##Setup a new Service
In order to get started you must create a new class and extend the `TeleportService` class:
```java
public class MobileService extends TeleportService{
   // your implementation goes here
}
```
This class can be created either on your Android Mobile project or your Android Wear project or both.
The `TeleportService` is a service running in background that can listen for `DataItem` or `Messages` 
received from one of your devices.

The second step is to declare the service inside your Android Manifest file. This is required by Android 
in order to **activate** the service when the .apk is loaded into the system:
```xml
<service
    android:name=".MobileService"
    android:enabled="true"
    android:exported="true" >
    <intent-filter>
        <!-- let the system start the service if needed -->
        <action android:name="com.google.android.gms.wearable.BIND_LISTENER" />
        <!-- listeners receive events that match the action and data filters -->
        <action android:name="com.google.android.gms.wearable.DATA_CHANGED" />

        <data
            android:host="*"
            android:scheme="wear" />
    </intent-filter>
</service>
```

In this example I have declare an **action** of type **com.google.android.gms.wearable.BIND_LISTENER** and 
an action of type **com.google.android.gms.wearable.DATA_CHANGED** to inform Android that this service should 
be triggered only when a new data is sent.

For further configurations, I suggest you to visit this link: [WearableListenerService](https://developers.google.com/android/reference/com/google/android/gms/wearable/WearableListenerService)

##Setup the Receiver Task
Now that your service is available you need to create an async Task that is capable of receiving information.
In Teleport there are 2 different async Tasks, one called `TeleportService.OnGetMessageTask` and one called 
`TeleportService.OnSyncDataItemTask` depending if you are looking for a Message or a DataItem.

>###Message vs DataItem
>First question that pop to my mind was, when should I use `DataItem` and when should I use a `Message`?

>According to Google, the `DataItem` allows you to send a more complex object (serializable Bundle) and in case of failure 
>during the communication Android will keep the `DataItem` and send it to the other device as soon as a connection 
>is established.
>A `Message` is a more simple object sent across the network that **get lost** if the other side is not capable to receive it, example 
>when the two devices are not connected.

###Message Receiver Task
A Message sent by another device can be received by implementing a new `OnGetMessageTask` like the following:
```java
//Task that shows the path of a received message
public class ReadMessageTask extends TeleportService.OnGetMessageTask {

   @Override
   protected void onPostExecute(String  path) {
        // do something with the message

        // let`s reset the task (otherwise it will be executed only once)
        setOnGetMessageTask(new ReadMessageTask());
    }
}
```
>This example is using the old class which will get replaced soon in version 2.0 of Teleport (Reloaded). As you can see from here, you can only pass and receive a string while actually this string should be your `key` and the `value` should be a byte[] array.

In order to use this Task on your Service you have to specify that inside the `onCreate` method of your service as following:
```java
public class MobileService extends TeleportService{

    @Override
    public void onCreate() {
        super.onCreate();
    
        setOnGetMessageTask(new ReadMessageTask());
    
    }
}
```

**Final note:**
In this example we are setting up a Task that will get executed only **once*. The second time your service will receive a message 
the Teleport library will throw an exception because ***an async task can be executed only once in Android*** and this is why in the example above 
we reset the task after it is handled:
```java
   @Override
   protected void onPostExecute(String  path) {
        // do something with the message

        // let`s reset the task (otherwise it will be executed only once)
        setOnGetMessageTask(new ReadMessageTask());
    }
```

###DataItem Receiver Task
If you are not interested in using Messages but you want to send a `DataItem`, because it gives you more 
flexibility, then you have to implement a Task of type `TeleportClient.OnSyncDataItemTask` in the following way:
```java
public class ReceiveDataItemTask extends TeleportClient.OnSyncDataItemTask {

    @Override
    protected void onPostExecute(DataMap dataMap) {

        //let`s get the String from the DataMap, using its identifier key
        String myValue = dataMap.getString("myKey");

        //let`s do something with the data item received
        
        
        // let`s reset the task (otherwise it will be executed only once)
        setOnSyncDataItemTask(new ReceiveDataItemTask());
     }
}
```

Like we did for a message, in the same way we can setup the Task to retrieve a DataItem using the following syntax 
inside the `onCreate()` method of our Service:

```java
public class MobileService extends TeleportService{

    @Override
    public void onCreate() {
        super.onCreate();
    
        setOnSyncDataItemTask(new ReceiveDataItemTask());
    
    }
}
```

The major difference between Message and DataItem, in Teleport, is the ability to send primitive types using 
a DataItem. In this case we sent a `key` `value` pair and we can easily retrieve the value using the well known key and 
cast the result.

Teleport (Reloaded) offer a method for each primitive type, so from a `TeleportClient` you can easily send:
 - Integer, Long, Decimal, Float
 - Boolean, Byte[]
 - String and Images
 
[Go back to homepage](https://github.com/raffaeu/Teleport/blob/master/README.md)