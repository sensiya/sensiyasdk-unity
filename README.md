# Sensiya SDK Unity Integration For Android

## STARTING THE SDK

### Step 1: Add SensiyaSDK files to you project

- Download the Unity SDK from the website and open it.
- Make sure all the files are selected and import them into your project.

### Step 2: Modify your Android Manifest

The Android Manifest is an integral part of every Android application and contains data about the app that is being used by the Android system to run it. More information about the manifest can be found here: [http://developer.android.com/guide/topics/manifest/manifest-intro.html] [1]:

- If you don't have a custom Android Manifest in your project:

    1.	Copy the AndroidManifest.xml file from the Assets/SensiyaSDK/Examples folder to Assets/Plugins/Android folder.
    2.	Open your copied AndroidManifest.xml, find the below tag and replace with your Sensiya API key in the needed place:

```sh
<meta-data android:name="SENSIYA_APP_KEY" android:value="YOUR-API-KEY"/>
```

- If you already have a custom Android Manifest in your project (You can look at the AndroidManifest.xml in Assets/SensiyaSDK/Examples to see the needed format):

1.Add the following permissions under the manifest section:

```sh
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.READ_PROFILE" />
<uses-permission android:name="android.permission.READ_CONTACTS" />
<uses-permission android:name="android.permission.GET_ACCOUNTS" />
<uses-permission android:name="android.permission.READ_CALL_LOG" />
<uses-permission android:name="android.permission.READ_SMS" />
<uses-permission android:name="android.permission.GET_TASKS"/>
<uses-permission android:name="android.permission.WAKE_LOCK"/>
<uses-permission android:name="com.android.browser.permission.READ_HISTORY_BOOKMARKS" />
```
2.Add the following components under application section:

```sh
<!-- Declare the service of the SDK -->
<service android:name="com.sensiya.brainssdk.BrainsService"/>

<!-- Declare receiver that is used by the SDK -->
<receiver android:name="com.sensiya.brainssdk.BrainsBroadcastReceiver">
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="android.net.wifi.STATE_CHANGE"/>
        <action android:name="android.location.PROVIDERS_CHANGED"/>
    </intent-filter>
</receiver>

<!-- Declare the receiver that is used by the Unity SDK -->
<receiver android:name="com.sensiya.brainssdk.unity.BrainsReceiver">
    <intent-filter>
        <action android:name="brainssdk.intent.action.LEAVING_HOME"/>
        <action android:name="brainssdk.intent.action.ENTERING_HOME"/>
        <action android:name="brainssdk.intent.action.LEAVING_WORK"/>
        <action android:name="brainssdk.intent.action.ENTERING_WORK"/>
        <action android:name="brainssdk.intent.action.HEADING_TO_WORK"/>
        <action android:name="brainssdk.intent.action.HEADING_HOME"/>
        <action android:name="brainssdk.intent.action.WENT_TO_SLEEP"/>
        <action android:name="brainssdk.intent.action.WOKE_UP"/>
        <action android:name="brainssdk.intent.action.GEO_FENCE"/>
        <action android:name="brainssdk.intent.action.USER_ACTIVITY_CHANGED"/>
        <action android:name="brainssdk.intent.action.USER_BROWSING"/>
        <action android:name="brainssdk.intent.action.PARKED"/>
        <action android:name="brainssdk.intent.action.DRIVING"/>
        <action android:name="brainssdk.intent.action.TRAVELING"/>
        <action android:name="brainssdk.intent.action.BACK_FROM_TRAVEL"/>
        <action android:name="brainssdk.intent.action.ABOUT_TO_TRAVEL"/>
    </intent-filter>
</receiver>

<!-- Add the unique application key that was generated for your app in the web console -->
<meta-data android:name="SENSIYA_APP_KEY" android:value=" YOUR-API-KEY "/>
```

3.Put your API key where it says 'YOUR-API-KEY'

### Step 3: Add BrainsPrefab to your scene

- Drag BrainsPrefab from Assets/SensiyaSDK/Prefabs into the first scene in your project that loads. This GameObject will start SensiyaSDK automatically when your scene loads and will persist across scene changes so you don't have to worry about adding it to other scenes.

- BrainsAPICallback contains the events for monitoring the SDK state. Get notified when the SDK is connected by registering to the OnConnected event in BrainsAPICallback.OnConnected. Once you get OnConnected, your SensiyaSDK is ready for usage.

- You can also register to the other events:
    - BrainsAPICallback.OnError – Notifies when an error occurred.
    - BrainsAPICallback.OnDisconnected – Notifies when the SensiyaSDK got disconnected.
    - BrainsAPICallback.OnUserDataReady – Notifies when the user's demographic data is ready for usage.

## GETTING ASYNCHRONOUS UPDATES

### Step 1: Getting updates

In order to get asynchronous events from the SDK you need to register to the BrainsGameObject.OnBrainsEvent event.


### Step 2: Handling the SensiyaSDK's events

Every event contains an action (event type) and dictionary that contains optional extra data. All the possible event types reside in the BrainsConstants class. Every event is documented and tells exactly what extra data comes with it (Not all events contains extra data).


### CALLING SYNCHRONOUS API

After you get notified that the SensiyaSDK is connected, you can get specific information using the direct Brains API:

- Get the Sensiya SDK version
```sh
string version = BrainsAPI.GetVersion();
```
- Gender:
```sh
UserData user = BrainsAPI.User.GetUserData();
Gender gender = user.Gender; // Male/Female/Unknown
float genderConfidence = user.GenderConfidence; // Confidence level from 0 to 1
```
- Get us  er age range:
```sh
UserData user = BrainsAPI.User.GetUserData();
AgeRange ageRange = user.AgeRange;
float ageRangeConfidence = user.AgeRangeConfidence;
float estimatedAge = user.EstimatedAge;
```
- Get user home location:
```sh
Location home = BrainsAPI.Location.GetHomeLocation();
double latitude = home.Latitude;
double longitude = home.Longitude;
```
- Get user's last known actvity:
```sh
Location home = BrainsAPI.Location.GetHomeLocation();
double latitude = home.Latitude;
double longitude = home.Longitude;
```

## GEOFENCING
Using our SDK you can define custom geo fences for your users. You can define geo fences using the following APIs:

- Define simple geo fence with custom radius: 
```sh
BrainsAPI.LocationServices.GeoFencing.AddGeofence(geoFenceId, latitude, longitude, radius);
```
- Define simple geo fence with custom radius and expiration:
```sh
BrainsAPI.LocationServices.GeoFencing.AddGeofence(geoFenceId, latitude, longitude, radius, expiration);;
```
- Define simple geo fence with custom radius, expiration and loitering time to indicate if the user is dwelling:
```sh
BrainsAPI.LocationServices.GeoFencing.AddGeofence(geoFenceId, latitude, longitude, radius, expiration, loitering);
```

Upon receiving a geo event you will be able to identify the triggering geo fence and transition type from the extra data arrived with the event.
