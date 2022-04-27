# Mediastream Platform SDK Android

## Overview

This library allows you to embed and control your VOD and Live Stream player on your Android application.

# Usage

## Getting started

First you need to add the library in your dependencies. You can do that by adding the following in the build.gradle of your application:

### For android mobile apps

```
dependencies {
    ...
    implementation 'io.github.mediastream:mediastreamplatformsdkandroid:8.0.8'
}
```

### For android tv apps

```
dependencies {
    ...
    implementation "com.google.android.exoplayer:exoplayer:2.9.6"
    implementation "com.google.android.exoplayer:extension-ima:2.9.6"
    implementation "com.google.android.exoplayer:extension-cast:2.9.6"
    implementation "com.google.android.exoplayer:exoplayer-core:2.9.6"
    implementation 'com.android.volley:volley:1.2.1'    
    implementation 'io.github.mediastream:mediastreamplatformsdkandroid:7.0.4'
}
```

Remember enabled multidex feature on your android default config:

```
android {
    ...
    defaultConfig {
        ...
        multiDexEnabled true
    }
}
```

If you present issues related META-INF try to add this on your packaging options:

```
android {
    ...
    android {
        packagingOptions {
            exclude 'META-INF/DEPENDENCIES'
            exclude 'META-INF/LICENSE'
            exclude 'META-INF/LICENSE.txt'
            exclude 'META-INF/license.txt'
            exclude 'META-INF/NOTICE'
            exclude 'META-INF/NOTICE.txt'
            exclude 'META-INF/notice.txt'
            exclude 'META-INF/ASL2.0'
            exclude("META-INF/*.kotlin_module")        
        }
    }
}
```

In order to support youbora feature please add this dependency on you main build.gradle file:

```
allprojects {
    repositories {
        google()
        jcenter()
        maven { url "https://npaw.jfrog.io/artifactory/youbora/" }
    }
}
```

Make sure to give your application permission for internet and access network state:

```xml
AndroidManifest.xml

<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

Add "android:configChanges" to activity label with:
```xml
<activity ...
  android:configChanges="keyboard|keyboardHidden|orientation|screenSize|screenLayout|smallestScreenSize">
</activity>
```

Add receiver and service tags:

```xml
<receiver android:name="am.mediastre.mediastreamplatformsdkandroid.MediastreamActionService"/>
<service android:name="am.mediastre.mediastreamplatformsdkandroid.MediastreamPlayerService" />
```

Remember that you need a instance of Exoplayer view in your activity layout, for example:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".main_activity">

    <FrameLayout
        android:id="@+id/main_media_frame"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="0.5"
        android:background="#000000"
        android:keepScreenOn="true">
        <com.google.android.exoplayer2.ui.PlayerView
            android:id="@+id/player_view"
            android:layout_width="match_parent"
            android:layout_height="match_parent">
        </com.google.android.exoplayer2.ui.PlayerView>
    </FrameLayout>
</LinearLayout>
```

After doing this, you can start with this basic example of usage:

```java
import android.content.Intent;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.widget.FrameLayout;

import com.google.android.exoplayer2.ui.PlayerView;

import am.mediastre.mediastreamplatformsdkandroid.MediastreamPlayer;
import am.mediastre.mediastreamplatformsdkandroid.MediastreamPlayerConfig;

public class VODPlayer extends AppCompatActivity {
    private String TAG = "SampleApp";
    private FrameLayout container;
    private MediastreamPlayer player;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_vodplayer);

        MediastreamPlayerConfig config = new MediastreamPlayerConfig();
        config.id = "5d543e1b4fd7920fbadb1b35";
        config.type = MediastreamPlayerConfig.VideoTypes.VOD;
        //If you need support for DRM uncomment next lines
        /*String[] drmHeaders = new String[]{"X-AxDRM-Message",   "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ2ZXJzaW9uIjoxLCJjb21fa2V5X2lkIjoiMjdGNkNDMTEtODRGMS00MzFELTk5MDItQTZDODAwRUM4NzBCIiwibWVzc2FnZSI6eyJ0eXBlIjoiZW50aXRsZW1lbnRfbWVzc2FnZSIsImV4cGlyYXRpb25fZGF0ZSI6IjIwMjAtMDMtMDNUMTg6NTM6MjcuMzA2WiIsImtleXMiOlt7ImlkIjoiRUNFQjhEQ0MtMEI1RS00RUExLTkxRkMtNDJDMzJFNEQzMjVEIn1dfX0.9vDzNNjPiM9mi9ajDbXklP5Gbea8a65sfn-JxSV7KLY"};
        config.drmData = new MediastreamPlayerConfig.DrmData("https://drm-widevine-licensing.axtest.net/AcquireLicense", drmHeaders);*/

        container = findViewById(R.id.main_media_frame);
        //For Android Mobile Apps
        player = new MediastreamPlayer(this, config, container);
        
        //For Android TV Apps
        /*PlayerView playerView = findViewById(R.id.player_view);
        player = new MediastreamPlayer(this, config, container, playerView);*/
    }

    @Override
    public void onBackPressed() {
        player.releasePlayer();
    }
}
```

Don't forget to release the player.

## Class MediastreamPlayerConfig

Mediastream Player configuration.

### Attributes

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| id | String | Yes | Video ID |
| type | MediastreamPlayerConfig.VideoTypes | Yes | Video type. Possible values: `VOD`, `LIVE`, `EPISODE` |
| environment | MediastreamPlayerConfig.Environment | No | Environment where the video is located, `PRODUCTION` or `DEV`. Default: `PRODUCTION` |
| adUrl | String | No | AdURL (e.g. VAST). Optional, if not specified will play ads configured in Mediastream Platform. |
| accessToken | String | No | Access token for restricted videos. |
| autoplay | boolean | No | Autoplay video if true. Default: false |
| drmData | DrmData | No | DRM support, url and headers. Default: null |
| videoFormat | MediastreamPlayerConfig.AudioVideoFormat | No | Type of video (e.g. DASH). Can use `DASH`, `MP4`, `M4A`. Default: `HLS` |
| customerID | Customer ID | No | Unique Identification string for the video consumer customer  |
| mute | boolean | No | Player starts muted. Default: false |
| dvr | boolean | No | Player starts prepared to use dvr, to use you need to set a windowDRV. Default: false |
| windowDVR | int | No | Window dvr voiced in seconds.|
| showControls | boolean | No | Hide the controls of the player. Default: true |
| AdPreloadTimeoutMs | Long | No | Allows to change the default duration in milliseconds for which the player must buffer while preloading an ad group before that ad group is skipped and marked as having failed to load. Default: 10000ms |
| referer | string | No | Allows set a custom referrer to find in the statistics. |
| src | string | No | Arbitrary source to reproduce. |
| loadNextAutomatically | boolean | No | Allows play next episode if exists. Available only when EPISODE type is setted. |
| NotificationColor | Integer | No | Allows to change Notification background color when uses player as service. |
| NotificationImageUrl | String | No | Allows to change Notification image when uses player as service. |
| NotificationDescription | String | No | Allows to change Notification description when uses player as service. |
| NotificationSongName | String | No | Allows to change Notification song name when uses player as service. |
| NotificationAlbumName | String | No | Allows to change Notification album name when uses player as service. |
| NotificationIconUrl | String | No | Allows to change Notification icon when uses player as service. |
| appName | string | No | Very useful to identify traffic in platform analytics. Example: "mediastream-app-tv" or "mediastream-app-mobile"|
| youboraExtraParams | String[20] | No | Allows send youbora extraparams. The limit is 20 items, the index is equivalent in youbora to the index plus one. Example: youboraExtraParams[0] is equivalent to CustomDimension1 |
| automaticallyReconect | boolean | No | Allows try to reconnect when network is lost. Default: true |
| playerId | String | No | Takes player configuration from platform settings | 

### Methods

| Return | Method | Description |
| --- | --- | --- |
| void | addAdCustomParameter(java.lang.String key, java.lang.String value) | Adds custom parameter to ad. |

### Constructors

MediastreamPlayerConfig()
MediastreamPlayerConfig().DrmData(String drmUrl, String[] drmHeaders)

## Class MediastreamPlayer

Mediastream player for Live or VOD from Mediastream Platform. Includes ads from VAST.

### Methods

| Return | Method | Description |
| --- | --- | --- |
| void | addPlayerCallback(MediastreamPlayerCallback callback) | Add callbacks which will be called when the player enters and leaves fullscreen mode. |
| void | play() | Resume video playback. |
| void | pause() | Pause video playback. |
| void | releasePlayer() | When you are finished using this MediastreamPlayer, make sure to call this method. |
| void | getCurrentPosition() | Return the current position of the video. |
| void | seekTo(int newPosition) | Seeks to a position specified in milliseconds in the current video. |
| void | enterFullscreen | Enter to fullscreen mode |
| void | exitFullscreen | Out to fullscreen mode |
| void | backward(Long time) | Seek to time established |
| void | forward(Long time) | Seek to time established |
| void | reloadPlayer(MediastreamPlayerConfig) | Allows to reload the player with a new content without kill the player instance |
| void | ShowSelectorPopup(Int type) | Show a selector for video qualities if type is 0, audio if type is 1, and subtitles if type is 2 |
| string | getMediaTitle() | Returns the title of the media, if it does not exist, it will return an empty string. |
| string | getMediaPoster() | Returns a url poster of the media, if it does not exist, it will return an empty string. |
| void | RetryNetworkConnection() | Try to reconnect player to network manually. |

### Constructors

MediastreamPlayer(Context context, MediastreamPlayerConfig config, FrameLayout container, PlayerView playerView)

If you wanna access to the full Exoplayer control you can get the current instance like this:

```java
private MediastreamPlayer mdstrm;
mdstrm = MediastreamPlayer(Context context, Activity activity, PlayerView playerView, MediastreamPlayerConfig config, FrameLayout container);

mdstrm.msPlayer //Current exoplayer instance
```

If you wanna use player as service you needs to create a method to call MediastreamPlayerService that receives a MediastreamPlayer instances, linke that:

```java
    void startService(MediastreamPlayer player) {
        MediastreamPlayerService.setupService(player);
        Intent serviceIntent = new Intent(this, MediastreamPlayerService.class);
        serviceIntent.setAction(getPackageName()+".action.startforeground");
        ContextCompat.startForegroundService(this, serviceIntent);
    }
```

## Class DrmData
Class to set drm config values, like tokens and urls.

### Constructors
DrmData(String drmUrl, String[] drmHeaders)

## Interface MediastreamPlayerCallback

Interface for implementing the player callbacks.

### Methods

| Return | Method | Description |
| --- | --- | --- |
| void | onEnd() | Called when the current video has completed playback to the end of the video. |
| void | onError() | Called when an error not related to playback occurs. |
| void | onPause() | Called when the current video pauses playback. |
| void | onPlay() | Called when the current video starts playing from the beginning. |
| void | onReady() | Called when the current video resumes playing from a paused state. |
| void | onFullScreen() | Called when the current video enter to fullscreen mode. |
| void | offFullScreen() | Called when the current video out to fullscreen mode. |
| void | onNewSourceAdded() | Called when a new settings was setted |
| void | onLocalSourceAdded() | Called when a local source was setted. |
| void | onAdPlay() | Called when Ad start to play. |
| void | onAdPause() | Called when Ad is paused. |
| void | onAdLoaded() | Called when Ad is loaded. |
| void | onAdResume() | Called when Ad is on resume mode. |
| void | onAdEnded() | Called when Ad finish. |
| void | onAdError() | Called when Ad failed. |
| void | onPlaybackErrors(JsonObject error) | Called when a playback error occurs. |
| void | onEmbedErrors(JsonObject error) | Called when a embed error occurs. |

## Enum MediastreamPlayerConfig.Environment

### Options

`PRODUCTION`, `DEV`

## Enum MediastreamPlayerConfig.PlayerType

### Options

`LIVE`, `VOD`, `EPISODE`

See JavaDoc in docs/javadoc/ for further information.

### Examples
Check this repository to see some SDK examples. 
https://github.com/cruiz666/MediastreamPlatformSDKAndroidSamples
