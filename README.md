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
I will try to log the message on each function: onStartCommand, onCreate, onDestroy. And there is the result when I start this service on activity

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
You can see, first the service will call onCreate to create new instance of service. Then, Started Service will call onStartCommand to get data from intent and excute the job.

##### And what happen if I start this service again? Let's see

```
09-18 18:41:17.436 6293-6293/yendangn.com.androidsercice D/TAG: onStartCommand
```

onCreate doesn't call. The new service just call onStartCommand. Hmmmmm
When you start a service, Android OS will check that if your service still not have any instance in running before, Android will create a new instance for service. Else, Android DON'T CREATE a new instance , just call onStartCommand to excute new task.

#### And what happen if I stop a service
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
onDestroy will be called. 

