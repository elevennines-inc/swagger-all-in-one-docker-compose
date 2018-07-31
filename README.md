## swagger-all-in-one-docker-container
### Overview
I docker composed Swagger Editor, Swagger UI and Swagger mock api server to handle them more easily.
There is a sample swagger spec in this so the Editor, UI and the mock API server will run without any configuration from the start.
All you need to do is edit the swagger spec, save as openapi.json, and restart docker. Voila, UI and the mock API server are updated.

### How to Run
```
docker-compose up -d

It will look like this.
docker-compose ps
         Name                       Command               State           Ports
----------------------------------------------------------------------------------------
swagger-api      /usr/local/bin/apisprout / ...   Up      0.0.0.0:8083->8000/tcp
swagger-editor   sh /usr/share/nginx/docker ...   Up      0.0.0.0:8081->8080/tcp
swagger-ui       sh /usr/share/nginx/docker ...   Up      0.0.0.0:8082->8080/tcp
```

### How to Use
1. Edit swagger spec with swagger-editor
2. Save swagger spec as json from swagger-editor File menu
3. Move and save the json file as `swagger/openapi.json`
4. Execute `docker-compose restart` and swagger-ui and swagger-api(mock server) will be updated

### Heads-up
- When the UI is referenced as `http://localhost:8082/`, cache might be used even the changes are made in swagger files. So it may be better to refference as `http://127.0.0.1:8082/`(api, too.)
- Whern swagger-api failed to run, it's likely that api server failed to run because the openapi.json was not properly read. So use `docker logs` command and get rid of the cause then restart it again.

### swagger-editor
- Can edit swagger spec
- Can export swagger spec as json, yaml and etc. swagger-ui can read the files and they can be beautifly referenced as documentation. apisprout can read the yml and json then it can serve the mock API.

### swagger-ui
- Can referrence the documentation from swagger spec.
- swagger spec can be assined from json file path or API_URL path.
```
environment:
  SWAGGER_JSON: /openapi.json
  # API_URL: ""
```
- ./swagger/openapi.json is refferenced in this repository

### swagger-api(apisprout)
- Internally using [apisprout](https://github.com/danielgtaylor/apisprout) in docker.
- swagger spec is compatible with `openapi: 3.x`.
- ./swagger/openapi.json is also refferenced from api in this repository
- Of course, you can use the api from curl, etc.

  ```
  * example
  curl http://127.0.0.1:8083/pets/1 -H "accept: application/json"

    {
      "id": 1,
      "name": "doggie",
      "tag": "dog"
    }
  ```
