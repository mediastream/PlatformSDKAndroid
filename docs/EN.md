# Mediastream Platform SDK Android

## Overview

This library allows you to embed and control your VOD and Live Stream player on your Android application.

# Usage

## Getting started

First you need to add the library in your dependencies. You can do that by adding the following in the build.gradle of your application:

```
dependencies {
    ...
    implementation "am.mediastre.mediastreamplatformsdk:mediastreamplatformsdkandroid:6.0.25"
    implementation "com.google.android.exoplayer:exoplayer:2.9.6"
    implementation "com.google.android.exoplayer:extension-ima:2.9.6"
    implementation "com.google.android.exoplayer:exoplayer-ui:2.9.6"
    implementation 'com.nostra13.universalimageloader:universal-image-loader:1.9.5'
    implementation 'com.android.volley:volley:1.1.1'
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

Additionally you need to add the exo_playback_control_view.xml file that you can find in the sources folder and then add this to your layouts folder in your project.

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
    private PlayerView playerView;
    private MediastreamPlayer player;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_vodplayer);

        MediastreamPlayerConfig config = new MediastreamPlayerConfig();
        config.id = "5d543e1b4fd7920fbadb1b35";
        config.type = MediastreamPlayerConfig.VideoTypes.VOD;
        config.trackEnable = false;
        String[] drmHeaders = new String[]{"X-AxDRM-Message",   "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ2ZXJzaW9uIjoxLCJjb21fa2V5X2lkIjoiMjdGNkNDMTEtODRGMS00MzFELTk5MDItQTZDODAwRUM4NzBCIiwibWVzc2FnZSI6eyJ0eXBlIjoiZW50aXRsZW1lbnRfbWVzc2FnZSIsImV4cGlyYXRpb25fZGF0ZSI6IjIwMjAtMDMtMDNUMTg6NTM6MjcuMzA2WiIsImtleXMiOlt7ImlkIjoiRUNFQjhEQ0MtMEI1RS00RUExLTkxRkMtNDJDMzJFNEQzMjVEIn1dfX0.9vDzNNjPiM9mi9ajDbXklP5Gbea8a65sfn-JxSV7KLY"};
        config.drmData = new MediastreamPlayerConfig.DrmData("https://drm-widevine-licensing.axtest.net/AcquireLicense", drmHeaders);        

        playerView = findViewById(R.id.player_view);
        container = findViewById(R.id.main_media_frame);
        player = new MediastreamPlayer(this, config, container, playerView);
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
| referer | string | No | Allows set a custom referrer to find in the statistics. |
| src | string | No | Arbitrary source to reproduce. |
| loadNextAutomatically | boolean | No | Allows play next episode if exists. Available only when EPISODE type is setted. |

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

### Constructors

MediastreamPlayer(Context context, MediastreamPlayerConfig config, FrameLayout container, PlayerView playerView)

If you wanna access to the full Exoplayer control you can get the current instance like this:

```java
private MediastreamPlayer mdstrm;
mdstrm = MediastreamPlayer(Context context, Activity activity, PlayerView playerView, MediastreamPlayerConfig config, FrameLayout container);

mdstrm.msPlayer //Current exoplayer instance
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
| void | onError() | Called when an error occurs during video playback. |
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

## Enum MediastreamPlayerConfig.Environment

### Options

`PRODUCTION`, `DEV`

## Enum MediastreamPlayerConfig.PlayerType

### Options

`LIVE`, `VOD`, `EPISODE`

See JavaDoc in docs/javadoc/ for further information.
