### Swagger
#### 起動方法
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

#### 使い方
1. swagger-editorでswagger specを編集
2. swagger specをjsonとyml形式でswagger-editorの画面から保存
3. 保存したjsonを`swagger/swagger.json`に移動
4. 保存したyamlを`swagger/swagger.yaml`に移動
5. `docker-compose restart`でswagger-uiとswagger-api(mock server)に変更が反映される

#### 注意
 `http://localhost:8082/`で参照するとキャッシュで変更が反映されない場合があるので`http://127.0.0.1:8082/`で参照した方が良い(apiも同様)


#### swagger-editor
- swagger specを編集出来る
- swagger specはjson形式にしてエクスポート出来、swagger-uiから参照するとドキュメントとして見れる。swagger-api-mock-dockerからymlを参照するとmock api serverになる

#### swagger-ui
- swagger specをドキュメントとして見れる
- swagger specは環境変数でjsonファイルまたは、API_URLからjsonを参照できる
```
environment:
  SWAGGER_JSON: /swagger.json
  # API_URL: ""
```
- 現在はこのレポジトリの./swagger/swagger.jsonが参照先になっている

#### swagger-api(swagger-api-mock-docker)
- こちらも./swagger/swagger.ymlが参照先になっている
- もちろんcurl等で叩けます

  ```
  * サンプルです
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
