# Mediastream Platform SDK Android

## Overview

This library allows you to embed and control your VOD and Live Stream player on your Android application.

# Usage

## Getting started

First you need to add the library in your dependencies. You can do that by adding the following in the build.gradle of your application:

```
dependencies {
    ...
    compile 'am.mediastre.mediastreamplatformsdk:mediastreamplatformsdkandroid:1.1.0'
}
```

Make sure to give your application permission for internet and access network state:

```xml
AndroidManifest.xml

<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

After doing this, you can start with this basic example of usage:

```java
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.FrameLayout;

import am.mediastre.mediastreamplatformsdkandroid.MediastreamPlayer;
import am.mediastre.mediastreamplatformsdkandroid.MediastreamPlayerConfig;

public class MainActivity extends Activity {

    private MediastreamPlayer player;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        View view = getLayoutInflater().inflate(R.layout.activity_main, null);

        FrameLayout playerContainer = (FrameLayout) view.findViewById(R.id.video_frame);

        String id = "567317b15050c6e76e896864";
        MediastreamPlayerConfig config = new MediastreamPlayerConfig(id, MediastreamPlayerConfig.PlayerType.VOD);

        player = new MediastreamPlayer(this, playerContainer);
        player.configure(config);

        setContentView(view);
        player.play();
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
| type | MediastreamPlayerConfig.PlayerType | Yes | Video type. Possible values: `VOD`, `LIVE` |
| environment | MediastreamPlayerConfig.Environment | No | Environment where the video is located, `PRODUCTION` or `QA`. Default: `PRODUCTION` |
| adUrl | String | No | AdURL (e.g. VAST). Optional, if not specified will play ads configured in Mediastream Platform. |
| accessToken | String | No | Access token for restricted videos. |
| autoplay | boolean | No | Autoplay video if true. Default: false |

### Methods

| Return | Method | Description |
| --- | --- | --- |
| void | addAdCustomParameter(java.lang.String key, java.lang.String value) | Adds custom parameter to ad. |

### Constructors

MediastreamPlayerConfig(java.lang.String id)
MediastreamPlayerConfig(java.lang.String id, MediastreamPlayerConfig.PlayerType type)
MediastreamPlayerConfig(java.lang.String id, MediastreamPlayerConfig.PlayerType type, java.lang.String adURL)

## Class MediastreamPlayer

Mediastream player for Live or VOD from Mediastream Platform. Includes ads from VAST.

### Methods

| Return | Method | Description |
| --- | --- | --- |
| void | configure(MediastreamPlayerConfig config) | Sets a new configuration for the player. |
| void | addPlayerCallback(MediastreamPlayerCallback callback) | Add callbacks which will be called when the player enters and leaves fullscreen mode. |
| void | play() | Resume video playback. |
| void | pause() | Pause video playback. |
| void | stop() | Stop video playback. Same as pause() |
| void | release() | When you are finished using this MediastreamPlayer, make sure to call this method. |
| void | isReady() | Returns if the player is ready or not. |
| void | isPlaying() | Returns if the player is playing or not. |
| void | getCurrentPosition() | Return the current position of the video. |
| void | addActionButton(Drawable icon, java.lang.String contentDescription, View.OnClickListener onClickListener) | Creates a button to put in the top right of the video player. |
| void | setChromeColor(int color) | Sets the color of the top chrome, bottom chrome, and background. |
| void | setLogoImage(Drawable logo) | Set the logo with appears in the left of the top chrome. |
| void | setPlaybackControlColor(int color) | Sets the color of the buttons and seek bar. |

### Constructors

MediastreamPlayer(Activity activity, FrameLayout container)

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

## Enum MediastreamPlayerConfig.Environment

### Options

`PRODUCTION`, `QA`

## Enum MediastreamPlayerConfig.PlayerType

### Options

`LIVE`, `VOD`

See JavaDoc in docs/javadoc/ for further information.
