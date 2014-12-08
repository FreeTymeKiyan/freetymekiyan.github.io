title: Get Google Advertising Id and Differences of Contexts
tags:
  - Android
  - Pro-tips
categories:
  - Coding
date: 2014-12-07 05:37:12
---

Recently I joined my friends in a side project named Money Maker. The idea of this project is fairly simple: it aims at integrating advertisement platforms together within our app and provide little task for users. User can finish these tasks and get credits, and exchange the credits to cash or gift cards. We are a team of 5: 1 iOS app dev, 1 Android app dev, 1 frontend dev, 1 backend dev, and 1 support guy and we are now supporting **126,822**(98560 + 28262) users in total. The problem I'm going to talk about today happened during the dev process.  
  
![Number of Users Powered UMeng](/images/user.png)
  
## Background  
  
I'm updating Android client to new version right now. In this version, we designed a new scheme to prevent fraudulent traffic. Basically, we try to bind user's phone number to their account and get the google advertising identifier(GAID) of the device. It should be a simple problem. But getting GAID is a real painful thing.  
  
## Original  Solution

According to the documentation on Android developer's site<sup>[[1]](#References)</sup>, to get GAID with code on device, we need to set up Google Play Services SDK first<sup>[[2]](#References)</sup>. Just open the SDK manager and download the latest version of it. Then we add it as a library project and modify your app's manifest file. Add the following tag as a child of the <application> element:  
```
<meta-data android:name="com.google.android.gms.version"
        android:value="@integer/google_play_services_version" />  
```
  
One more thing needed is preventing ProGuard from stripping away required classes. Add the following lines in the proguard-project.txt:  
  
```
-keep class * extends java.util.ListResourceBundle {
    protected Object[][] getContents();
}
  
-keep public class com.google.android.gms.common.internal.safeparcel.SafeParcelable {
    public static final *** NULL;
}
  
-keepnames @com.google.android.gms.common.annotation.KeepName class *
-keepclassmembernames class * {
    @com.google.android.gms.common.annotation.KeepName *;
}
  
-keepnames class * implements android.os.Parcelable {
    public static final ** CREATOR;
}
```
  
OK, now we should be able to use Google Play Services SDK in our app. To make sure it is the latest version, call ``GooglePlayServicesUtil.isGooglePlayServicesAvailable(Context context)`` and check if the result code is 0, which means SUCCESS.  
  
Now with the SDK set up, we can make use of the code provided by Google. An Example implementation is as below:    
```
import com.google.android.gms.ads.identifier.AdvertisingIdClient;
import com.google.android.gms.ads.identifier.AdvertisingIdClient.Info;
import com.google.android.gms.common.GooglePlayServicesAvailabilityException;
import com.google.android.gms.common.GooglePlayServicesNotAvailableException;
import java.io.IOException;
  
// Do not call this function from the main thread. Otherwise, 
// an IllegalStateException will be thrown.
public void getIdThread() {
    Info adInfo = null;
    try {
        adInfo = AdvertisingIdClient.getAdvertisingIdInfo(mContext);
    } catch (IOException e) {
        // Unrecoverable error connecting to Google Play services (e.g.,
        // the old version of the service doesn't support getting AdvertisingId).
    } catch (GooglePlayServicesAvailabilityException e) {
        // Encountered a recoverable error connecting to Google Play services. 
    } catch (GooglePlayServicesNotAvailableException e) {
        // Google Play services is not available entirely.
    }
    final String id = adInfo.getId();
    final boolean isLAT = adInfo.isLimitAdTrackingEnabled();
}
```
  
One extremely important thing here is DO NOT call ``getIdThread()`` from main UI thread. ``getAdvertisingIdInfo(Context context)`` is a blocking call. If called on the main thread, the method throws IllegalStateException.  
  
## Problem  
  
After I implemented the code with according to things above, I still can't get the GAID on my phone. Below is the error log and code.  
  
![Error Log](/images/error_log.jpg)  
  
```
new Thread(new Runnable() {
  
    @Override
    public void run() {
        try {
            String gaid = AdvertisingIdClient.getAdvertisingIdInfo(SettingsActivity.this).getId();
            if (gaid != null) {
                // Log.d("DEBUG", "gaid: " + gaid);
                setGaid(gaid);
            }
        } catch (IllegalStateException e) {
            e.printStackTrace();
        } catch (GooglePlayServicesRepairableException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } catch (GooglePlayServicesNotAvailableException e) {
            e.printStackTrace();
        }
    }
}).start();
```
  
It kept throwing IOException and Connection failure. I searched on google for possible solutions, but no matter I put it in an AsyncTask's ``doInBackground()`` method<sup>[[3]](#References)</sup> or a thread's runnable, it just didn't work.  
  
## Solution  
  
When I was about to give up and try to implement my own class according to the solution on StackOverFlow<sup>[[4]](#References)</sup>, it suddenly came to me that what if I change the context of current Activity to Application context? Although I didn't know the exact difference between them, I remembered that using getApplicationContext() crashes both ProgressDialog and Toast, which lead me to this feeling that it might be the problem.  
  
And it really worked. I couldn't believe my eyes when I saw the GAID in debug log. Then I tried on different devices and different places to put the thread call(``onCreate(), onResume(), onClick()``), it all worked.  
  
## Differences of Activity Context and Application Context  
  
I gave my answer on StackOverFlow<sup>[[5]](#References)</sup> and decided to figure out the exact differences between these contexts.  
  
Actually both Activity context and Application context are instances of Context class, but Application context is tied to the lifecycle of the process, while Activity context is to the lifecycle of related Activity. Thus they have access to different information about the application environment.<sup>[[6]](#References)</sup>  
  
In the documentation<sup>[[7]](#References)</sup> of method ``getApplicationContext()``, it mentions an example of how it interacts with registerReceiver``(BroadcastReceiver, IntentFilter)``. If used from an Activity context, the receiver is being registered within that Activity. When the Activity is destroyed, the receiver no longer works. If used from an Application context, the receiver will never be unregistered.  
  
You only use getApplicationContext() when you know you need a Context for something that may live longer than any other likely Context you have at your disposal. Scenarios include<sup>[[8]](#References)</sup>:  
  
* Use getApplicationContext() if you need something tied to a Context that itself will have global scope. I use getApplicationContext(), for example, in WakefulIntentService, for the static WakeLock to be used for the service. Since that WakeLock is static, and I need a Context to get at PowerManager to create it, it is safest to use getApplicationContext().
* Use getApplicationContext() when you bind to a Service from an Activity, if you wish to pass the ServiceConnection (i.e., the handle to the binding) between Activity instances via onRetainNonConfigurationInstance(). Android internally tracks bindings via these ServiceConnections and holds references to the Contexts that create the bindings. If you bind from the Activity, then the new Activity instance will have a reference to the ServiceConnection which has an implicit reference to the old Activity, and the old Activity cannot be garbage collected.  
  
## Conclusions  
  
### How to get GAID  
  
1. Download latest Google Play Services SDK.  
2. Import the code and add it as a library project.  
3. Modify manifest.xml.  
4. Add rules in proguard-project.txt.  
5. call ``AdvertisingIdClient.getAdvertisingIdInfo(getApplicationContext()).getId()`` in a worker thread to get the id in String. 
  
### Differences  
  
1. They are tied to different lifecyle.  
2. They have access to different information.  
  
## Extension Reading  
  
Except for Application and Activity contexts, there are other contexts in Android, namely Service context, ContentProvider context, and BroadcastReceiver context. Below is a clear table on context capabilities from a nice post by Dave Smith<sup>[[9]](#References)</sup>:  
  
![Context Capabilities](/images/contexts.png)
  
## References  
  
1. [Android Developers Official Site, *Advertising ID*](https://developer.android.com/google/play-services/id.html)
2. [Android Developers Official Site, *Setting Up Google Play Services*](https://developer.android.com/google/play-services/setup.html)
3. [AdMax Tech Blog, *【Android】AdvertisingIDの取得方法*](http://tech.admax.ninja/2014/09/12/how-to-get-advertising-id/)  
4. [StackOverFlow, *Using the new Android Advertiser id inside an SDK*](http://stackoverflow.com/questions/20097506/using-the-new-android-advertiser-id-inside-an-sdk)
5. [StackOverFlow, *My Answer On Ad SDKs can't seem to get the Advertising info from the device*](http://stackoverflow.com/questions/27257877/ad-sdks-cant-seem-to-get-the-advertising-info-from-the-device/27344612#27344612)
6. [StackOverFlow, *Difference between Activity Context and Application Context*](http://stackoverflow.com/questions/4128589/difference-between-activity-context-and-application-context)  
7. [Android Developers Official Site, *Documentation on getApplicationContext Method*](http://developer.android.com/reference/android/content/ContextWrapper.html#getApplicationContext%28%29)
8. [StackOverFlow, *When to call activity context OR application context?*](http://stackoverflow.com/a/7298955/3642996)
9. [Dave Smith, *Context, What Context?*](http://www.doubleencore.com/2013/06/context/)
  