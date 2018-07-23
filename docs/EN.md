# Mediastream Platform SDK Android

## Overview

This library allows you to embed and control your VOD and Live Stream player on your Android application.

# Usage

## Getting started

First you need to add the library in your dependencies. You can do that by adding the following in the build.gradle of your application:

```
dependencies {
    ...
    implementation "am.mediastre.mediastreamplatformsdk:mediastreamplatformsdkandroid:4.0.0"
    implementation "com.google.android.exoplayer:exoplayer:2.8.1"
    implementation "com.google.android.exoplayer:extension-ima:2.8.1"
    implementation "com.google.android.exoplayer:exoplayer-ui:2.8.1"
    implementation "com.google.android.exoplayer:exoplayer-core:2.8.1"
    implementation "com.google.android.exoplayer:exoplayer-dash:2.8.1"
    implementation "com.google.android.exoplayer:exoplayer-hls:2.8.1"
    implementation "com.google.android.exoplayer:exoplayer-smoothstreaming:2.8.1"
}
```

If you wanna enable google cast you need to add these libraries too:

```
dependencies {
    implementation "com.android.support:mediarouter-v7:27.0.0"
    implementation "com.google.android.gms:play-services-cast-framework:12.0.0"
}
```

You can change the version of mediarouter and play-services-cast-framework in case that you have conflicts with firebase or something similar.

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
<FrameLayout
    android:id="@+id/main_media_frame"
    android:layout_width="match_parent"
    android:layout_height="0dp"
    android:layout_weight="0.5"
    android:background="#000000">
    <com.google.android.exoplayer2.ui.PlayerView
        android:id="@+id/video_frame"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
</FrameLayout>
```

Additionally you need to add the exo_playback_control_view.xml file that you can find in the sources folder and then add this to your layouts folder in your project.

After doing this, you can start with this basic example of usage:

```java
import android.app.Activity;
import android.os.Bundle;
import android.widget.FrameLayout;
import com.google.android.exoplayer2.ui.PlayerView;
import am.mediastre.mediastreamplatformsdkandroid.MediastreamPlayer;
import am.mediastre.mediastreamplatformsdkandroid.MediastreamPlayerCallback;
import am.mediastre.mediastreamplatformsdkandroid.MediastreamPlayerConfig;

public class MainActivity extends Activity {
    private PlayerView playerView;
    private MediastreamPlayer mdstrm;
    private MediastreamPlayerConfig config;
    private MediastreamPlayerCallback mediastreamPlayerCallback;
    private FrameLayout container;

    @Override
    protected void onCreate(Bundle saveInstanceState) {
        super.onCreate(saveInstanceState);
        setContentView(R.layout.activity_main);
        playerView = findViewById(R.id.video_frame);
        container = findViewById(R.id.main_media_frame);
        config = new MediastreamPlayerConfig();
        config.id = "5b43b0fed770f00772fce743";
        config.accountID = "55117baedc01616019533551";
        config.environment = MediastreamPlayerConfig.Environment.PRODUCTION;
        config.type = MediastreamPlayerConfig.VideoTypes.VOD;
        config.castAvailable = false;
        config.loop = true;
        config.autoplay = true;
        config.showControls = true;
        config.debug = true;
        mdstrm = new MediastreamPlayer(this, this, playerView, config, container);
    }

    @Override
    protected void onDestroy() {
        if (player != null) {
            player.release();
        }
        super.onDestroy();
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
| type | MediastreamPlayerConfig.VideoTyoes | Yes | Video type. Possible values: `VOD`, `LIVE` |
| environment | MediastreamPlayerConfig.Environment | No | Environment where the video is located, `PRODUCTION` or `DEV`. Default: `PRODUCTION` |
| adUrl | String | No | AdURL (e.g. VAST). Optional, if not specified will play ads configured in Mediastream Platform. |
| accessToken | String | No | Access token for restricted videos. |
| autoplay | boolean | No | Autoplay video if true. Default: false |
| drmData | DrmData | No | DRM support, url and headers. Default: null |
| videoType | VideoType | No | Type of video (e.g. DASH). Can use `DASH`, `MP4`. Default: `HLS` |
| customerID | Customer ID | No | Unique Identification string for the video consumer customer  |
| mute | boolean | No | Player starts muted. Default: false |
| castAvailable | boolean | No | Google cast is available. To use it you need to add the dependencies on the build.gradle file on your project. Default: false |
| dvr | boolean | No | Player starts prepared to use dvr, to use you need to set a windowDRV. Default: false |
| windowDVR | int | No | Window dvr voiced in seconds.|
| showControls | boolean | No | Hide the controls of the player. Default: true |
| loop | boolean | No | Loop the current item. Default: false |
| referer | string | No | Allows set a custom referrer to find in the statistics. |

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

### Constructors

MediastreamPlayer(Context context, Activity activity, PlayerView playerView, MediastreamPlayerConfig config, FrameLayout container)

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
| void | onLoop() | Called when the current video enter to loop mode. |

## Enum MediastreamPlayerConfig.Environment

### Options

`PRODUCTION`, `DEV`

## Enum MediastreamPlayerConfig.PlayerType

### Options

`LIVE`, `VOD`

See JavaDoc in docs/javadoc/ for further information.
