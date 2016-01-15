#Send and Receive DataItem
If you are planning to send a set of key value pairs and you need to be sure that the data is sent across 
all devices connected then you have to use DataItem and not Messages: [DataItem](http://developer.android.com/training/wearables/data-layer/data-items.html)

>A DataItem defines the data interface that the system uses to synchronize data between handhelds and wearables.

##Send a DataItem
In order to send a DataItem you need an active `TeleportClient` object:
```java
TeleportClient teleportClient = new TeleportClient(context);
```

Second step, you must ensure that your client is connected:
```java
teleportClient.connect();
```

Then you can send a DataItem with a **payload** of any primitive type:
```java
teleportClient.syncString("MyKey", "MyValue");
teleportClient.syncFloat("MyKey", 2.0f);
```

>**Note:**
>Since version 8.3 of Google Play, a DataItem is not sent immediately but it is cached into a Queue. This queue 
>may send your DataItem even after 20-30 minutes.
>To avoid this I modified Teleport by including a `request.setUrgent();` and an Item of type `String` with a tag `ID`.
>In this way your DataItem is always delivered immediately because for Android this is a new value.

##Receive a DataItem
In order to receive a DataItem, first you need to Setup a `TeleportService`, see [documentation](https://github.com/raffaeu/Teleport/blob/master/doc/SERVICE.md).
```java
public class MobileService extends TeleportService{
   // your implementation goes here
}
```

When your Service class is created you need to create a custom Task to handle DataItem. The task should extends 
the `TeleportService.OnSyncDataItemTask`.
Remember that Teleport will pass inside the `DataItem` one item called `ID` which contains a unique ID for your request.
This is required because for Android Wear, if a `DataItem` is sent with the same value twice, the second time it will not be sent.

```java
//Task that shows the path of a received message
public class ReadDataItemTask extends TeleportService.OnSyncDataItemTask {

   @Override
   protected void onPostExecute(DataMap dataMap) {
        // do something with the DataMap
        Log.d(TAG, String.format("My Value: %s", dataMap.getString("MyKey")));
        
        // let`s reset the task (otherwise it will be executed only once)
        setOnSyncDataItemTask(new ReadDataItemTask());
    }
}
```

Now you can inform your TeleportService that a Task is available to handle a DataItem in the following way:
```java
// Override the onCreate() method of your TeleportService class
@Override
public void onCreate() {
    super.onCreate();
    
    setOnSyncDataItemTask(new ReadDataItemTask());
}
```

If you do not want to re-create a Task inside the Task itself you can use the Teleport Factory in the following way:
```java
// Override the onCreate() method of your TeleportService class
@Override
public void onCreate() {
    super.onCreate();
    
    // this factory will re-create a task after a message is handled
    setOnSyncDataItemTaskBuilder(new OnSyncDataItemTask.Builder() {
        @Override
        public TeleportService.OnSyncDataItemTask build() {
            return new ReadDataItemTask();
        }
    });
}
```

[Go back to homepage](https://github.com/raffaeu/Teleport/blob/master/README.md)