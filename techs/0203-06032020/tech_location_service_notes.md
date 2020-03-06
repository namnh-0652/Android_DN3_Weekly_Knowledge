# Tech Link
`Docs link`
- https://developer.android.com/training/location
- https://viblo.asia/p/tao-service-de-lay-location-trong-android-WAyK8PWeKxX

`Sample Links`
- https://github.com/android/location-samples

# Description

Learning how to create location service, with flow specs:
- If app in foreground, no notification
- When app moves to background, show notification

## Idea

As you know, from android O (8), background location service is limitted regardless of an app's target SDK version. https://developer.android.com/about/versions/oreo/background-location-limits
So as the recommendation from Google, I consider to do the flow for getting location in background like below: 
1. Create a bound service
2. When App is in background, unbind service, but make it become a foreground service with a notification to user 
3. When App is on foreground, bind app (activity, fragment...) with the service, make it back to normal bound service (un-foreground), and remove the notification.
Of course, the flow work in case user accept our permission requests.

## How & Notes
### Permissions
```XML
    <!-- Required for all versions if need precise location --> 
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <!-- Required for background location services on Q+ (10) -->
    <uses-permission android:name="android.permission.ACCESS_BACKGROUND_LOCATION" />
    <!-- Required for foreground services on P+ (9), this is normal permission -->
    <uses-permission android:name="android.permission.FOREGROUND_SERVICE" />

    <!-- Foreground services in Q+ require foregroundServiceType -->
    <service
      android:name=".location.MyLocationService"
      android:foregroundServiceType="location" />
```

### Activity

```java
private var locationService: MyLocationService? = null
private var isLocationServiceBound = false
private val serviceConnection = object : ServiceConnection {
    override fun onServiceDisconnected(name: ComponentName?) {
        // locationService?.removeLocationUpdates()
        locationService = null
        isLocationServiceBound = false
    }

    override fun onServiceConnected(name: ComponentName?, service: IBinder?) {
        val binder = service as? MyLocationService.LocalBinder
        locationService = binder?.service
        isLocationServiceBound = true
        // locationService?.requestLocationUpdates()
    }
}
override fun onStart() {
    super.onStart()
    if (permissionsGranted()) {
        bindService(Intent(this, MyLocationService::class.java), serviceConnection, Context.BIND_AUTO_CREATE)
    } else {
        requestPermissions()
    }
}

override fun onStop() {
    if (isLocationServiceBound) {
        unbindService(serviceConnection)
        isLocationServiceBound = false
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O && shouldStartLocationService()) {
            // From android O+, we need to startForeground within 5 seconds from the time it's created
            // So, with the context of our app, we can not do that, we need restart the service
            stopLocationService()
            startForegroundService(Intent(this, HealthLocationService::class.java))
         }
    }
    super.onStop()
}
private fun shouldStartLocationService(): Boolean {
   return permissionsGranted() && isLocationEnabled()
}
```

### Service
```java
class MyLocationService : Service() {
    private val serviceBinder = LocalBinder()
    inner class LocalBinder : Binder() {
        internal val service: MyLocationService by lazy {
            this@MyLocationService
        }
    }
    
    override fun onBind(intent: Intent): IBinder {
        // App is on foreground, at least 1 binder
        stopForeground(true)
        return serviceBinder
    }

    override fun onRebind(intent: Intent) {
        // App back to foreground, at least 1 binder
        stopForeground(true)
        super.onRebind(intent)
    }

    override fun onUnbind(intent: Intent): Boolean {
        // App go to background
        if (Build.VERSION.SDK_INT < Build.VERSION_CODES.O &&requestingLocationUpdates()) {
            // only when app needs to use background location.
            startForeground(NOTIFICATION_ID, notification)
        }
        return true // True make sure onRebind() called when a client rebinds
    }

    override fun onStartCommand(intent: Intent, flags: Int, startId: Int): Int {
      // ...
      // Don't re-start service
      return START_NOT_STICKY
    }

    override fun onCreate() {
      fusedLocationClient = LocationServices.getFusedLocationProviderClient(this)
      locationCallback = object : LocationCallback() {
         override fun onLocationResult(locationResult: LocationResult) {
               super.onLocationResult(locationResult)
               onNewLocation(locationResult.lastLocation)
         }
      }
      locationRequest = LocationRequest().apply {
         // interval want to update, inexact, maybe larger or shorter than this
         interval = UPDATE_INTERVAL_IN_MILLISECONDS
         // Fastest time to update
         fastestInterval = FASTEST_UPDATE_INTERVAL_IN_MILLISECONDS
         priority = LocationRequest.PRIORITY_HIGH_ACCURACY
      }
      getLastLocation()
      ...
   }
   private fun getLastLocation() {
   try {
      fusedLocationClient.lastLocation.addOnCompleteListener { task ->
         if (task.isSuccessful && task.result != null) {
            latestLocation = task.result
         }
      } catch (unlikely: SecurityException) {
         Log.e(TAG, "Lost location permission. ${unlikely}")
      }
   }
   private fun onNewLocation(location: Location) {
      // Can use local broadcast to send to activity or fragment
   }
   fun requestLocationUpdates() {
        if (Build.VERSION.SDK_INT < Build.VERSION_CODES.O) {
            startService(Intent(applicationContext, MyLocationService::class.java))
        }
        try {
            fusedLocationClient.requestLocationUpdates(
                locationRequest,
                locationCallback,
                Looper.myLooper()
            )
        } catch (unlikely: SecurityException) {
            Log.e(TAG, "Lost location permission. Could not request updates. $unlikely")
        }
    }
    fun removeLocationUpdates() {
        try {
            fusedLocationClient.removeLocationUpdates(locationCallback)
            stopSelf()
        } catch (unlikely: SecurityException) {
            Log.e(TAG, "Lost location permission. Could not remove updates. $unlikely")
        }
    }
}
```

# Tech hashtags
**#Location**, **#ForegroundService**, **#BindService**

> 1. From android O+, we need to startForeground within 5 seconds from the time it's created
> 2. Permission and type of service
