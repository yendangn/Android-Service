# Android-Service

### Started Service

#### Life cycle

```java

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {

        Logger.d("onStartCommand");

        return super.onStartCommand(intent, flags, startId);
    }

    @Override
    public void onCreate() {

        Logger.d("onCreate");

        super.onCreate();
    }

    @Override
    public void onDestroy() {

        Logger.d("onDestroy");

        super.onDestroy();
    }
```
I will try to log the message on each function: ```onStartCommand```, ```onCreate```, ```onDestroy```. And there is the result when I start this service on activity

```java
private void startService() {
        Intent intent = new Intent(this, MyService.class);
        startService(intent);
    }
```

```
09-18 18:39:08.305 6293-6293/yendangn.com.androidsercice D/TAG: onCreate
09-18 18:39:08.306 6293-6293/yendangn.com.androidsercice D/TAG: onStartCommand
```
You can see, first the service will call ```onCreate``` to create new instance of service. Then, Started Service will call ```onStartCommand``` to get data from intent and execute the job.

##### And what happen if I start this service again? Let's see

```
09-18 18:41:17.436 6293-6293/yendangn.com.androidsercice D/TAG: onStartCommand
```

```onCreate``` doesn't call. The new service just call ```onStartCommand```. Hmmmmm
When you start a service, Android OS will check that if your service still not have any instance in running before, Android will create a new instance for service. Else, Android DON'T CREATE a new instance , just call ```onStartCommand``` to execute new task.

##### And what happen if I stop a service
```java

    private void stopService() {
        Intent intent = new Intent(this, MyService.class);
        stopService(intent);
    }
```
The log will be:
```
09-18 18:48:56.509 6293-6293/yendangn.com.androidsercice D/TAG: onDestroy
```
It's mean, when I stop the service, ```onDestroy``` will be called. 

##### Service run on background. So it will run on Worker thread or Main Thread? 
Ok, add some code to ```onStartCommand``` function

```java
    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {

        if(Looper.myLooper() == Looper.getMainLooper()) {
            Logger.d("I am run on main thread");
        } else {
            Logger.d("No, I am run on worker thread");
        }

        return super.onStartCommand(intent, flags, startId);
    }
```
And see the log:
```
09-18 20:54:13.494 8517-8517/yendangn.com.androidsercice D/TAG: I am run on main thread
```
So, service run on main thread!

##### When complete the job, Will the service stop or continue run?  
Come back to ``` onStartCommand``` where service will execute the job you want.
```java
 @Override
    public int onStartCommand(Intent intent, int flags, int startId) {

        int a = 1;
        int b = 2;
        int c = a + b;

        Logger.d("Hey, I completed your task, Just sum tow number haha");

        return super.onStartCommand(intent, flags, startId);
    }
```

and ```onDestroy```

```
 @Override
    public void onDestroy() {

        Logger.d("onDestroy");

        super.onDestroy();
    }
```


If the service will stop after complete "do sum", the    ```onDestroy``` will be called.
It's mean we will have tow logs: ```Hey, I completed your task, Just sum tow number haha"``` and ```onDestroy```.
But... this is actual log I got:

```
09-18 21:02:30.110 8867-8867/yendangn.com.androidsercice D/TAG: Hey, I completed your task, Just sum tow number haha
```

```onDestroy``` didn't not call. It's mean when complete the job, service still run, it's not stop.

##### How can I stop service after complete my job? I don't want it still run.

```java
@Override
    public int onStartCommand(Intent intent, int flags, int startId) {

        int a = 1;
        int b = 2;
        int c = a + b;

        Logger.d("Hey, I completed your task, Just sum tow number haha");

        stopSelf();

        return super.onStartCommand(intent, flags, startId);
    }
```

Call ``` stopSelf``` to stop the service.

The log:

```
09-18 21:12:22.646 9259-9259/yendangn.com.androidsercice D/TAG: Hey, I completed your task, Just sum tow number haha
09-18 21:12:22.978 9259-9259/yendangn.com.androidsercice D/TAG: onDestroy
```
```onDestroy``` was called, yeah yeah, I can stop it.
