# nekochans-openapi
OpenAPIのスキーマを管理する

## APIスキーマの追加方法

`docs/` 以下に任意のディレクトリ名でディレクトリを作成し `openapi.yaml` を追加して下さい。

https://swagger.io/specification/ に以下の記述があるので `openapi.yaml` とするのが良さそうです。

>It is RECOMMENDED that the root OpenAPI document be named: openapi.json or openapi.yaml.

## OpenAPIの設計方針について

APIの設計方針に関しては以下のようなドキュメントを参考に設計します。

- [Web API 設計](https://docs.microsoft.com/ja-jp/azure/architecture/best-practices/api-design)
- [翻訳: WebAPI 設計のベストプラクティス](https://qiita.com/mserizawa/items/b833e407d89abd21ee72)

`openapi.yaml` には他にも色々と設定を行う必要がありますが、その点に関しては [スキーマファースト開発のためのOpenAPI（Swagger）設計規約](https://future-architect.github.io/articles/20200409/) を参考にしています。

## `openapi.yaml` をGUIで生成する

[OpenAPI Specification](https://swagger.io/specification/) の仕様を参考にしながら `openapi.yaml` を書いていく事になります。

GUIツールを利用すると比較的簡単にyamlファイルを生成出来ます。

公式のツールとしては [Swagger Editor](https://editor.swagger.io/) がありますがオススメは [Stoplight Studio](https://stoplight.io/studio/) です。

https://stoplight.io/studio/ から `Mac App` をダウンロード＆インストールします。

![StoplightStudio1](https://user-images.githubusercontent.com/11032365/88380746-13655d80-cde0-11ea-8739-167b0e642355.png)

`Open Existing Folder` から 目的の `openapi.yaml` があるフォルダを選択します。

## MockServerの起動

[Stoplight Studio](https://stoplight.io/studio/) を利用していれば [Prism](https://stoplight.io/open-source/prism/) 製のMockServerが起動します。

以下のように右下にMockServerへの接続情報があるので、リクエストを送信するとランダムに生成されたレスポンスが返ってきます。

![StoplightStudio2](https://user-images.githubusercontent.com/11032365/88381051-a0101b80-cde0-11ea-971e-56b3939be95c.png)

```
curl -v http://127.0.0.1:3100/members/1
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to 127.0.0.1 (127.0.0.1) port 3100 (#0)
> GET /members/1 HTTP/1.1
> Host: 127.0.0.1:3100
> User-Agent: curl/7.64.1
> Accept: */*
>
< HTTP/1.1 200 OK
< Access-Control-Allow-Origin: *
< Access-Control-Allow-Headers: *
< Access-Control-Allow-Credentials: true
< Access-Control-Expose-Headers: *
< X-Request-Id: dolore labore ex
< Content-type: application/json
< Content-Length: 117
< Date: Fri, 24 Jul 2020 10:09:19 GMT
< Connection: keep-alive
<
* Connection #0 to host 127.0.0.1 left intact
{"id":8676789581172568000,"githubUserName":"Excepteur id","githubPicture":"http://ab.k)","cvUrl":"http://_ENTBIEwgh"}
* Closing connection 0
```

ちなみにError系のレスポンスもちゃんと返ってきます。

## 他プロジェクトからの参照方法

色々な方法がありますが、1つの解決手段としてはGitのサブモジュールを利用する方法があります。

[portfolio-backend](https://github.com/nekochans/portfolio-backend) はこの方法を利用しています。

[portfolio-backend](https://github.com/nekochans/portfolio-backend) でサブモジュールを追加した際の手順を記載しておきます。

```
# portfolio-backendのルートディレクトリで以下を実行
git submodule add git@github.com:nekochans/nekochans-openapi.git docs/openapi
```

これによって [docs/openapi](https://github.com/nekochans/portfolio-backend/tree/master/docs) が本リポジトリを参照するようになります。

サブモジュールはGitのコミットIDを参照しています。

その為、本リポジトリに更新があった場合は参照元である [portfolio-backend](https://github.com/nekochans/portfolio-backend) でも更新を行う必要があります。

更新は通常以下のコマンドで行います。

```
# portfolio-backendのルートディレクトリで以下を実行
git submodule update --recursive --remote
```

これによってコミットIDが最新を指すようになります。

サブモジュールは慣れていないと少々扱いにくく感じるので、以下の記事等を見て基礎知識を身に着けておく事をオススメします。

[Git submoduleの押さえておきたい理解ポイントのまとめ](https://qiita.com/kinpira/items/3309eb2e5a9a422199e9)

## `openapi.yaml` から各言語のコードを生成する

- [openapi-generator](https://github.com/OpenAPITools/openapi-generator)
- [oapi-codegen](https://github.com/deepmap/oapi-codegen)

フロントエンドは `openapi-generator` がオススメです。

バックエンドは言語によりますがGoを使う場合 `oapi-codegen` をオススメします。

理由としてはOpenAPI3.0形式に対応しているからです。

Goだと [go-swagger](https://github.com/go-swagger/go-swagger) がもっとも有名ですがこのツールは残念ながらOpenAPI3.0形式に未対応です。

ただし `go-swagger` に関しては利用者が多い関係上、様々なノウハウが既に確立されているというメリットがあります。

[api-spec-converter](https://github.com/LucyBot-Inc/api-spec-converter) のようなツールを使って・・・

- `openapi.yaml` を3.0形式で書く
- `api-spec-converter` を利用して `openapi.yaml` を2.0形式に変換する

といった流れにする必要があります。

`go-swagger` を使う場合は [go-swaggerを用いたWebアプリケーション開発Tips19選](https://future-architect.github.io/articles/20200630/) という記事が参考になります。
