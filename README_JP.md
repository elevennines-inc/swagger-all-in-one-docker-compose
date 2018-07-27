## swagger-all-in-one-docker-container
### 概要
Swagger EditorとSwagger UIとSwaggerのモックAPIサーバーを手軽に同時に環境に立ち上げられるようdocker-compose化してみました
このコンテナには初期でサンプルのswagger specが入っていて、editor, ui, apiが初期状態で立ち上がり、最初から設定無しで起動出来るようになってます
あとはEditorでswagger specを編集し、swagger.jsonとswagger.yamlを保存しコンテナを立ち上げ直すだけで、UIとモックAPIサーバーを最新に更新していくことが出来ます

### 起動方法
```
docker-compose up -d

こんな感じで起動します
docker-compose ps
         Name                       Command               State           Ports
----------------------------------------------------------------------------------------
swagger-api      /bin/sh -c grunt                 Up      0.0.0.0:8083->8000/tcp
swagger-editor   sh /usr/share/nginx/docker ...   Up      0.0.0.0:8081->8080/tcp
swagger-ui       sh /usr/share/nginx/docker ...   Up      0.0.0.0:8082->8080/tcp
```

### 使い方
1. swagger-editorでswagger specを編集
2. swagger specをjsonとyml形式でswagger-editorの画面から保存
3. 保存したjsonを`swagger/swagger.json`に移動
4. 保存したyamlを`swagger/swagger.yaml`に移動
5. `docker-compose restart`でswagger-uiとswagger-api(mock server)に変更が反映される

### 注意
- `http://localhost:8082/`で参照するとキャッシュで変更が反映されない場合があるので`http://127.0.0.1:8082/`で参照した方が良い(apiも同様)
- swagger-apiの起動に失敗した場合は、`./swagger/swagger.yml`からモックAPIの立ち上げに失敗した可能性が高いので、`docker logs`などでデバッグし、swagger.ymlを直してから立ち上げ直す

### swagger-editor
- swagger specを編集出来る
- swagger specはjson, yaml形式などにしてエクスポート出来て、swagger-uiから参照するとドキュメントとして見れる。swagger-mock-apiからymlを参照するとモックAPIサーバーになる

### swagger-ui
- swagger specをドキュメントとして見れる
- swagger specは環境変数でjsonファイルまたは、API_URLからjsonを参照できる
```
environment:
  SWAGGER_JSON: /swagger.json
  # API_URL: ""
```
- このレポジトリの./swagger/swagger.jsonが参照先になっている

### swagger-api(swagger-mock-api)
- こちらも./swagger/swagger.ymlが参照先になっている
- もちろんcurl等で叩けます

  ```
  * 例
  curl -X GET  "http://127.0.0.1:8083/pet/1" -H "accept: application/json"

  {
      "category": {
          "id": -197211912667135,
          "name": "dog"
      },
      "status": "sold",
      "name": "doggie",
      "tags": [
          {
              "id": 2291624918908929,
              "name": "tag2"
          },
          {
              "id": -6490121805234175,
              "name": "tag1"
          },
          {
              "id": 1832877234847745,
              "name": "tag3"
          },
          {
              "id": 471700957298689,
              "name": "tag1"
          },
          {
              "id": 8771245264863233,
              "name": "tag2"
          }
      ],
      "photoUrls": "bbbb",
      "id": -3508953661046783
  }
  ```
