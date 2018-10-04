## swagger-all-in-one-docker-container
### 概要
Swagger EditorとSwagger UIとSwaggerのモックAPIサーバー(openapi: 3.x)を手軽に同時に環境に立ち上げられるようdocker-compose化してみました
もしswagger specを`swagger: "2.0"`形式で書きたい場合は`swagger2.0`branchをお使いください
このコンテナには初期でサンプルのswagger specが入っていて、editor, ui, api, nginxが初期状態で立ち上がり、最初から設定無しで起動出来るようになってます
あとはEditorでopenapi.jsonを保存しコンテナを立ち上げ直すだけで、UIとモックAPIサーバーを最新に更新していくことが出来ます

### 起動方法
```yaml
docker-compose up -d

こんな感じで起動します
docker-compose ps
         Name                       Command               State           Ports
----------------------------------------------------------------------------------------
swagger-api      /usr/local/bin/apisprout / ...   Up      0.0.0.0:8083->8000/tcp
swagger-editor   sh /usr/share/nginx/docker ...   Up      0.0.0.0:8081->8080/tcp
swagger-nginx    nginx -g daemon off;             Up      80/tcp, 0.0.0.0:8084->8084/tcp
swagger-ui       sh /usr/share/nginx/docker ...   Up      0.0.0.0:8082->8080/tcp
```

### 使い方
1. swagger-editorでswagger specを編集
2. swagger specをjson形式でswagger-editorの画面から保存
3. 保存したjsonを`swagger/openapi.json`に移動
4. `docker-compose restart`でswagger-uiとswagger-api(mock server)に変更が反映される
5. もしswagger-editorで外部ファイルを読み込みたい場合は、画面内`File > Import File`から読み込む

### 注意
- `http://localhost:8082/`で参照するとキャッシュで変更が反映されない場合があるので`http://127.0.0.1:8082/`で参照した方が良い(apiも同様)
- swagger-apiの起動に失敗した場合は、`swagger/openapi.json`からモックAPIの立ち上げに失敗した可能性が高いので、`docker logs`などでデバッグし、openapi.jsonを直してから立ち上げ直す
- swagger-apiを他ドメインからアクセスしたい場合は(CORS対策)、swagger-nginx経由でswagger-apiにアクセスする

### swagger-editor
- swagger specを編集出来る
- swagger specはjson, yaml形式などにしてエクスポート出来て、swagger-uiから参照するとドキュメントとして見れる。swagger-apiからjsonを参照するとモックAPIサーバーになる

### swagger-ui
- swagger specをドキュメントとして見れる
- swagger specは環境変数でjsonファイルまたは、API_URLからjsonを参照できる
```
environment:
  SWAGGER_JSON: /openapi.json
  # API_URL: ""
```
- このレポジトリの./swagger/openapi.jsonが参照先になっている

### swagger-api(apisprout)
- 内部的に[apisprout](https://github.com/danielgtaylor/apisprout)を使用しています
- swagger specは`openapi: 3.x`に対応しています
- こちらも./swagger/openapi.jsonが参照先になっている
- ただしapisproutがヘッダーに`Access-Control-Allow-Origin`を付けてくれないので、前段にnginxを置き、ヘッダーを付与してAPIにプロキシしています。(他ドメインからAPIにアクセスできないと不便なので。CORS対策。)

### swagger-nginx
- ヘッダー修正用に配置
- `8084`portでnginx経由でモックAPI(swagger-api)にアクセス出来ます。
- curl等で叩けます

  ```json
  * 例
  curl -i -X GET http://127.0.0.1:8084/pets/1 -H "accept: application/json"

  HTTP/1.1 200 OK
  Server: nginx/1.15.3
  Date: Thu, 04 Oct 2018 07:58:19 GMT
  Content-Type: application/json
  Content-Length: 49
  Connection: keep-alive
  Access-Control-Allow-Origin: *
  Access-Control-Allow-Methods: POST, GET, PATCH, DELETE, PUT, OPTIONS
  Access-Control-Allow-Headers: Origin, Authorization, Accept
  Access-Control-Allow-Credentials: true

  {
    "id": 1,
    "name": "doggie",
    "tag": "dog"
  }
  ```
