#Send and Receive Messages
If you are planning to just send and receives Messages between devices and you need a solution that works 
primarily if the two devices are connected then you can work with [MessageApi](http://developer.android.com/training/wearables/data-layer/messages.html).

>Messages are a one-way communication mechanism that's good for remote procedure calls (RPC), such as sending a message to the wearable to start an activity.
>For this reason, in this current version of Teleport you can send only a Message string and you cannot pass or read a payload

##Send a Message
In order to send a Message you need an active `TeleportClient` object:
```java
TeleportClient teleportClient = new TeleportClient(context);
```

Second step, you must ensure that your client is connected:
```java
teleportClient.connect();
```

Then you can send a message with a **payload** of type `byte[]`:
*if you don't need to pass a Payload, the second parameter can be null*
```java
teleportClient.sendMessage("My Message", myPayload);
```

##Receive a Message
In order to receive a Message, first you need to Setup a `TeleportService`, see [documentation](https://github.com/raffaeu/Teleport/blob/master/doc/SERVICE.md).
```java
public class MobileService extends TeleportService{
   // your implementation goes here
}
```

When your Service class is created you need to create a custom Task to handle messages. The task should extends 
the `TeleportService.OnGetMessageTask`.
>**Note:**
>Remember that in Android a Task can be executed only once, then you need to recreate one if you plan to listen for 
>multiple messages.

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

Now you can inform your TeleportService that a Task is available to handle a Message in the following way:
```java
// Override the onCreate() method of your TeleportService class
@Override
public void onCreate() {
    super.onCreate();
    
    setOnGetMessageTask(new ReadMessageTask());
}
```

If you do not want to re-create a Task inside the Task itself you can use the Teleport Factory in the following way:
```java
// Override the onCreate() method of your TeleportService class
@Override
public void onCreate() {
    super.onCreate();
    
    // this factory will re-create a task after a message is handled
    setOnGetMessageTaskBuilder(new OnGetMessageTask.Builder() {
        @Override
        public TeleportService.OnGetMessageTask build() {
            return new ReadMessageTask();
        }
    });
}
```
