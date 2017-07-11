# Mediastream Platform SDK Android

## Introdução

Essa biblioteca permite incluir e controlar mídias e live streams localizados na Plataform Mediastream em sua aplicação Android.

# Como usar

## Passos iniciais

Primeiro é necessário incluir a biblioteca nas dependências de seu projeto. Você pode fazer isso adicionando o código abaixo no build.gradle de sua aplicação:

```
dependencies {
    ...
    compile 'am.mediastre.mediastreamplatformsdk:mediastreamplatformsdkandroid:2.0.3'
}
```

Não se esqueça de dar permissão em sua aplicação para acesso à Internet e acesso ao estado da rede:

```xml
AndroidManifest.xml

<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

Adicione "android: configChanges" ao rótulo de atividade com:
```xml
<activity ...
  android:configChanges="keyboard|keyboardHidden|orientation|screenSize|screenLayout|smallestScreenSize">
</activity>
```

Com isso você pode começar a usar a biblioteca, veja o exemplo abaixo:

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

        String id = "57347e141ebc7caf3678f406";
        String accountID = "57330da6811307aa03fe6a65";
        MediastreamPlayerConfig config = new MediastreamPlayerConfig(id, MediastreamPlayerConfig.PlayerType.VOD, accountID);
        config.environment = (MediastreamPlayerConfig.Environment.PRODUCTION);

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

Não se esqueça de chamar release() ao terminar de utilizar o player.

## Classe MediastreamPlayerConfig

Essa classe possui as configurações do MediastreamPlayer.

### Atributos

| Nome | Tipo | Obrigatório | Descrição |
| --- | --- | --- | --- |
| id | String | Sim | ID do vídeo |
| type | MediastreamPlayerConfig.PlayerType | Sim | Tipo do vídeo. Valores possíveis: `VOD`, `LIVE` |
| environment | MediastreamPlayerConfig.Environment | Não | Ambiente em que está localizado o vídeo, `PRODUCTION` ou `QA`. Default: `PRODUCTION` |
| adUrl | String | Não | AdURL (ex: VAST). Opcional, se não especificado serão utilizadas as opções configuradas na Plataforma Mediastream. |
| accessToken | String | Não | Access token para vídeos restritos. |
| autoplay | boolean | Não | Inicia reprodução do vídeo automaticamente se for true. Default: false |
| drmData | DrmData | No | DRM suporte, url e cabeçalho. Default: null |
| videoType | VideoType | No | Tipo de vídeo (ex: DASH). Pode usar `DASH`, `HLS`, `MP4`, `OTHERS`. Default: `DASH` |

### Métodos

| Retorno | Método | Descrição |
| --- | --- | --- |
| void | addAdCustomParameter(java.lang.String key, java.lang.String value) | Adiciona um parâmetro customizado ao ad. Somente utilizado se foi passado adUrl. |

### Construtores

MediastreamPlayerConfig(java.lang.String id)
MediastreamPlayerConfig(java.lang.String id, MediastreamPlayerConfig.PlayerType type)
MediastreamPlayerConfig(java.lang.String id, MediastreamPlayerConfig.PlayerType type, java.lang.String adURL)

## Classe MediastreamPlayer

Mediastream player para Live ou VOD da Plataforma Mediastream.

### Métodos

| Retorno | Método | Descrição |
| --- | --- | --- |
| void | configure(MediastreamPlayerConfig config) | Configura o player. |
| void | addPlayerCallback(MediastreamPlayerCallback callback) | Adiciona callbacks que serão chamados quando o player entrar em modo tela cheia e sair de modo tela cheia. |
| void | play() | Inicia reprodução do vídeo. |
| void | pause() | Pausa reprodução do vídeo. |
| void | stop() | Pára reprodução do vídeo. Igual a pause() |
| void | release() | Deve ser chamado ao terminar de utilizar o player. |
| void | isReady() | Indica se o player está pronto ou não. |
| void | isPlaying() | Indica se o player está reproduzindo ou não. |
| void | getCurrentPosition() | Indica a posição atual (tempo) da reprodução do vídeo. |
| void | seekTo(int newPosition) | Avança até posição especificada vídeo. O valor de avanço é definido em milissegundos. |
| void | addActionButton(Drawable icon, java.lang.String contentDescription, View.OnClickListener onClickListener) | Cria um botão no topo direito do player. |
| void | setChromeColor(int color) | Muda a cor de fundo da faixa superior (onde o título é exibido) e do fundo da faixa inferior (controles do vídeo). |
| void | setLogoImage(Drawable logo) | Logo que aparecerá no topo esquerdo. |
| void | setPlaybackControlColor(int color) | Cor dos botões do player. |

### Construtores

MediastreamPlayer(Activity activity, FrameLayout container)

## Classe DrmData
Classe para definir valores de configuração do drm, como tokens e urls.

### Constructores
DrmData(String drmUrl, Map<String, String> drmHeaders)

## Interface MediastreamPlayerCallback

Interface para implementar os callbacks do player.

### Métodos

| Retorno | Método | Descrição |
| --- | --- | --- |
| void | onEnd() | Método chamado quando a reprodução do vídeo atual terminou. |
| void | onError() | Método chamado quando um erro ocorre durante a reprodução do vídeo. |
| void | onPause() | Método chamado quando o vídeo é pausado. |
| void | onPlay() | Método chamado quando inicia reprodução do vídeo. |
| void | onReady() | Método chamado quando o vídeo está pronto para reprodução. |

## Enum MediastreamPlayerConfig.Environment

### Opções

`PRODUCTION`, `QA`

## Enum MediastreamPlayerConfig.PlayerType

### Opções

`LIVE`, `VOD`

Veja o JavaDoc em docs/javadoc/ para maiores informações.
