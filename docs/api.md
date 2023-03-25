* トランスルーを通して読み上げ設定のコントロールをサポートします。

* トランスルーを通して翻訳と読み上げをサポートします。
!!! Tech "参考情報"
    ゆかりねっとコネクターNEOの翻訳/発話連携サーバプラグインAPIと互換があります。

## 翻訳

### １言語(POST/WS)

* 翻訳/発話連携サーバプラグインが開いているHTTPサーバもしくはWebSocketサーバに下記のリクエストを送付してください。
* 送付方式：HTTPの場合はPOST、WSの場合はテキスト

=== "Request"
    ```js
    {
        operation: 'translate',
        params: [
            {
                id: '0000-0000-0000-0000',
                lang: 'en_US',
                text: 'こんにちは'
            }
        ]
    }
    ```

=== "Response(OK)"
    ```js
    {
        operation: 'translate',
        status: 'success'
        id: '0000-0000-0000-0000',
        lang: 'ja_JP',
        text: 'Hello.'
    }
    ```

=== "Response(NG)"
    ```js
    {
        operation: 'translate',
        status: 'failure'
        id: '0000-0000-0000-0000',
        lang: 'ja_JP',
        text: 'こんにちは.'
    }
    ```

* 要求時は、翻訳してほしい言語を指定します。
* 返答時には、推定した言語と翻訳した文が来ます。
* statusがfailureの場合は、処理に失敗しています。

### 多言語(POST/WS)

!!! Tips "対応プラグインバージョン: v1.4以上"

* 翻訳/発話連携サーバプラグインが開いているHTTPサーバもしくはWebSocketサーバに下記のリクエストを送付してください。
* 送付方式：HTTPの場合はPOST、WSの場合はテキスト

=== "Request"
    ```js
    {
        operation: 'translates',
        params: [
            {
                id: '0000-0000-0000-0000',
                lang: [
                    'ja_JP',
                    'en_US',
                    'fr_FR'
                ],
                text: 'こんにちは'
            }
        ]
    }
    ```

=== "Response(OK) Case1"
    ```js
    {
        operation: 'translates',
        status: 'success'
        id: '0000-0000-0000-0000',
        detect_language : 'ja_JP',
        result : [
            {
                lang: 'ja_JP',
                text: 'こんにちは'
            },
            {
                lang: 'en_US',
                text: 'Hello.'
            },
            {
                lang: 'fr_FR',
                text: 'bonjour.'
            }
        ]
    }
    ```

=== "Response(OK) Case2"
    ```js
    {
        operation: 'translates',
        status: 'success'
        id: '0000-0000-0000-0000',
        detect_language : 'unknown',
        result : [
            {
                lang: 'ja_JP',
                text: 'こんにちは'
            },
            {
                lang: 'en_US',
                text: 'Hello.'
            },
            {
                lang: 'fr_FR',
                text: 'bonjour.'
            }
        ]
    }
    ```

=== "Response(NG)"
    ```js
    {
        operation: 'translates',
        status: 'failure'
        id: '0000-0000-0000-0000',
        message: '翻訳サーバに接続できません'
    }
    ```

* 要求時は、翻訳してほしい言語を指定します。
* 返答時には、推定した言語と翻訳した文が来ます。
* statusがfailureの場合は、処理に失敗しています。

## 読み上げ(GET)

* 送付方式：HTTP(GET)

=== "パラメータ"

|パラメータ|値    |例          |
|---------|------|------------|
|Engine   |エンジン名|さとうささら/CeVIO_64|
|Pitch    |高さ    | 1.0 |
|Accent   |抑揚    | 1.0 |
|Speed   |速度    | 1.0 |
|Volume   | 音量  | 1.0 |
|Quality   |声質    | 1.0 |
|Kuten   |句点待ち時間    | 1.0 |
|Toten   |読点待ち時間   | 1.0 |
|prePhoneme   |前空白    | 1.0 |
|postPhoneme   |後空白   | 1.0 |
|Text   |読む文章   | "おはよう" |
|ID   |識別用のID   | "00000-0000-0000-000000" |

* engineに指定する文字の区切り文字 ``/`` は、``%2F`` に置き換えてください
* パラメータ名は大文字・小文字を区別します
* IDを指定した場合は、何度要求しても1度しか読み上げません。

=== "Request"
    ```js
        http://localhost:15520/api/command?target=Plugin_PlayVoice&command=speech&Engine=さとうささら%2FCeVIO_64&Text=Hello
    ```

## 読み上げ(POST/WS)

* 翻訳/発話連携サーバプラグインが開いているHTTPサーバもしくはWebSocketサーバに下記のリクエストを送付してください。
* 送付方式：HTTPの場合はPOST、WSの場合はテキスト

=== "Request(Standard)"
    ```js
    {
        operation: 'speech',
        params: [
            {
                id: '0000-0000-0000-0000',
                text: 'こんにちは',
                talker: 'ずんだもん-ノーマル/VOICEVOX'
            }
        ]
    }
    ```
=== "Request(Extend)"
    ```js
    {
        operation: 'speech',
        params: [
            {
                id: '0000-0000-0000-0000',
                text: 'こんにちは',
                talker: 'ずんだもん-ノーマル/VOICEVOX',
                volume: 1.0
            }
        ]
    }
    ```
=== "Response(OK)"
    ```js
    {
        operation: 'speech',
        status: 'sended'
        id: '0000-0000-0000-0000',
        text: 'こんにちは.' 
    }
    ```
=== "Response(NG)"
    ```js
    {
        operation: 'speech',
        status: 'failure'
        id: '0000-0000-0000-0000',
        text: 'こんにちは.' 
    }
    ```

* 要求時は、発話してほしいボイスキャラクターを指定するとその音源で話そうとします。
* statusがfailureの場合は、プラグインが無効な場合など要求が出せなかった場合にでます。
* statusがsendedの場合、要求自体はだせたという意味で、発話が完了したわけではありません。


## 音声話者リスト(GET)

* 送付方式：HTTP(GET)

=== "Request"
    ```js
        http://localhost:15520/api/command?target=Plugin_PlayVoice&command=device
    ```

=== "Result"
    ```json
    [
    'ずんだもん-ノーマル/VOICEVOX',
    'ずんだもん-あまあま/VOICEVOX'
    ]  
    ```

## 音声話者リスト(POST/WS)

* 送付方式：HTTPの場合はPOST、WSの場合はテキスト
=== "Request"
    ```js
    {
        operation: 'speech.getvoicelist',
        params: [
            {
                id: '0000-0000-0000-0000',
            }
        ]
    }
    ```
=== "Response(OK)"
    ```js
    {
        operation: 'speech.getvoicelist',
        status: 'sended'
        id: '0000-0000-0000-0000',
        voice: [
                'ずんだもん/VOICEVOX',
                '弦巻マキ(日)/CeVIO AI'
            ]
    }
    ```
=== "Response(NG)"
    ```js
    {
        operation: 'speech.getvoicelist',
        status: 'failure'
        id: '0000-0000-0000-0000'
    }
    ```
