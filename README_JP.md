## swagger-all-in-one-docker-container
### 概要
Swagger EditorとSwagger UIとSwaggerのモックAPIサーバー(openapi: 3.x)を手軽に同時に環境に立ち上げられるようdocker-compose化してみました
もしswagger specを`swagger: "2.0"`形式で書きたい場合は`swagger2.0`branchをお使いください
このコンテナには初期でサンプルのswagger specが入っていて、editor, ui, apiが初期状態で立ち上がり、最初から設定無しで起動出来るようになってます
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
swagger-ui       sh /usr/share/nginx/docker ...   Up      0.0.0.0:8082->8080/tcp
```

### 使い方
1. swagger-editorでswagger specを編集
2. swagger specをjson形式でswagger-editorの画面から保存
3. 保存したjsonを`swagger/openapi.json`に移動
4. `docker-compose restart`でswagger-uiとswagger-api(mock server)に変更が反映される

### 注意
- `http://localhost:8082/`で参照するとキャッシュで変更が反映されない場合があるので`http://127.0.0.1:8082/`で参照した方が良い(apiも同様)
- swagger-apiの起動に失敗した場合は、`swagger/openapi.json`からモックAPIの立ち上げに失敗した可能性が高いので、`docker logs`などでデバッグし、openapi.jsonを直してから立ち上げ直す

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
- もちろんcurl等で叩けます

  ```json
  * 例
  curl http://127.0.0.1:8083/pets/1 -H "accept: application/json"

    {
      "id": 1,
      "name": "doggie",
      "tag": "dog"
    }
  ```
