#Send from a Service
From time to time it may happens that inside a `TeleportService` you want to receive a Message and fan out 
a new one, in case you need to implement a pattern of type Request/Response like the following one:
![Image](http://developer.android.com/wear/images/wear_cloud_node.png)

So within your Service you would like to receive a Message and reply with a new one.

##TeleportService is a TeleportClient
First of all you need to understand that a `TeleportService` is a `TeleportClient` so within a Service you 
can access all methods exposed by a Client:
```java
public class MobileService extends TeleportService{
  
   // your implementation goes here
   public MobileService()
   {
        // here you can access all methods
        syncString("MyKey", "MyValue");
        sendMessage("My Message", myPayload);
   }
}
```

But if you need to re-send a message within a `TeleportService.OnGetMessageTask` or a `TeleportService.OnSyncDataItemTask` you need 
to implement a pattern called *Inversion of Control* where actually your Task get injected an instance of the Service itself.

##Inject a Service instance inside a Task
Let's have a look a this specific task, it receives a Message and later it replies with another Message to acknowledge the sender.
```java
//Task that shows the path of a received message
public class ReadAndReplyMessageTask extends TeleportService.OnGetMessageTask {

    private TeleportService service;
    
    public ReadAndReplyMessageTask(TeleportService service){
        this.service = service;   
    }
    
    @Override
    protected void onPostExecute(String  path) {
        // if we get a request, we send a response
        if(path.equals("MESSAGE_REQUEST")){
            service.sendMessage("MESSAGE_RESPONSE", null);
        }
    }
}
```

Now we can inject the Service inside our Task in the following way:
```java
public class MobileService extends TeleportService{

    @Override
    public void onCreate() {
        super.onCreate();
    
        setOnGetMessageTask(new ReadAndReplyMessageTask(this));
    
    }
}
```

[Go back to homepage](https://github.com/raffaeu/Teleport/blob/master/README.md)