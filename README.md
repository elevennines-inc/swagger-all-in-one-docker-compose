## swagger-all-in-one-docker-container
### Overview
I docker composed Swagger Editor, Swagger UI and Swagger mock api server to handle them more easily.
There is a sample swagger spec in this so the Editor, UI and the mock API server will run without any configuration from the start.
All you need to do is edit the swagger spec, save swagger.json and swagger.yaml, and restart docker. Voila, UI and the mock API server are updated.

### How to Run
```
docker-compose up -d

It will look like this.
docker-compose ps
         Name                       Command               State           Ports
----------------------------------------------------------------------------------------
swagger-api      /bin/sh -c grunt                 Up      0.0.0.0:8083->8000/tcp
swagger-editor   sh /usr/share/nginx/docker ...   Up      0.0.0.0:8081->8080/tcp
swagger-ui       sh /usr/share/nginx/docker ...   Up      0.0.0.0:8082->8080/tcp
```

### How to Use
1. Edit swagger spec with swagger-editor
2. Save swagger spec as json and yaml from swagger-editor File menu
3. Move and save the json file as `swagger/swagger.json`
4. Move and save the yaml file as `swagger/swagger.yaml`
5. Execute `docker-compose restart` and swagger-ui and swagger-api(mock server) will be updated

### Heads-up
- When the UI is referenced as `http://localhost:8082/`, cache might be used even the changes are made in swagger files. So it may be better to refference as `http://127.0.0.1:8082/`(api, too.)
- Whern swagger-api failed to run, it's likely that api server failed to run because the swagger.yml was not properly read. So use `docker logs` command and get rid of the cause then restart it again.

### swagger-editor
- Can edit swagger spec
- Can export swagger spec as json, yaml and etc. swagger-ui can read the files and they can be beautifly referenced as documentatioin. swagger-mock-api can read the yml and json then it can serve the mock API.

### swagger-ui
- Can referrence the documentation from swagger spec.
- swagger spec can be assined from json file path or API_URL path.
```
environment:
  SWAGGER_JSON: /swagger.json
  # API_URL: ""
```
- ./swagger/swagger.json is refferenced in this repository

### swagger-api(swagger-mock-api)
- ./swagger/swagger.yaml is also refferenced from api in this repository
- Of course, you can use the api from curl, etc.

  ```
  * example
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
