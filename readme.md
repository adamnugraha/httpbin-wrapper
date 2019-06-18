# building a generic microservice for testing

1. clone this project
2. npm install
3. rename **.env.sample** to **.env**
4. to test execute command **npm test**
5. to start server, execute command **npm start_local**

run locally
```sh
export PORT=3000
export URL=https://httpbin.org/
export URI=delay/1

npm run start_local
```

run test
```
npm test
```

build docker image
```sh
docker build . -t js-httpbin:2.0
```

test docker image
CONTAINER_PORT defined the port to be used in the container.

the context root of the app can be configured with below values
- TARGET_URL defined the host url to be called
- TARGET_URI defined the uri to be called

```sh
docker run -e CONTAINER_PORT='3000' -e TARGET_URL='https://httpbin.org/' -e TARGET_URI='delay/1' -p 5000:3000 js-httpbin:2.0
```

push docker image to dockerhub
```sh
docker login
```

tag it
```sh
docker tag js-httpbin:2.0 jaricsng/js-httpbin:2.0
```

push it
```
docker push jaricsng/js-httpbin:2.0
```

