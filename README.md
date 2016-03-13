KOA 2 REST API Boilerplate
==================================

A simple Koa 2 rest api that implement oauth2 Resource Owner Password Flow with JSON Web Token and Refresh Token.

Koa app and mongo db dockerized with docker-compose.

**Note: This project is under development.**

## Features
  * Koa 2 ( async/await replace generator functions) : <https://github.com/koajs/koa/tree/v2.x>
  * Simple oAuth2 password flow implementation with oauth2orize : <https://github.com/jaredhanson/oauth2orize/>
  * Docker and Docker-compose
  * ES6 support via [babel](https://babeljs.io)
  * ES6 modules ( transpiled with babel )
  * Moongoose.js (for MongoDB interaction): <http://mongoosejs.com/>
  * JSON Web Token ([JWT](http://jwt.io)) authentication

## Install

  ```bash
  $ git clone https://github.com/ddellamico/koa-rest-api-es6
  ```
  On OS X and Windows you'll need to install **vagrant**: <https://www.vagrantup.com/docs/installation/>

  After that, in the project directory, run :

  ```bash
  $ vagrant up
  ```
  When vm is started, run `vagrant ssh` and finally
  run `cd koa-rest-api && docker-compose up` in the project dir to create and start the container.

  The app should then be running on your docker daemon on port **3000**.

  **NOTE:** [dotenv](https://github.com/motdotla/dotenv) is used to manage environment variables from a `.env` file.
  Place in root project a `.env` file and "overwrite" the default values. ES:

```bash
NODE_PORT=3000
NODE_ENV=development
MONGO_DB_NAME=koa-rest-api
TOKEN_SECRET=top-secret-key
TOKEN_EXPIRATION=86400
REFRESH_TOKEN_EXPIRATION=172800

```

## Make Requests
For the following requests, I use httpie. Check out here for more info : <https://github.com/jkbrzt/httpie>

First, set the following variables :

```bash
BASIC_AUTH="Basic S3JhQXBwOnNlY3JldA=="
BASE_URL="http://localhost:3000/api"
```

The BASIC_AUTH variable is assigned with the client credentials ( base64 encoded )
In this case I'm just using the credentials of a client seeded at application startup : KraApp:secret

Exchange username/password for access token :

```bash
http POST $BASE_URL/auth/token "Authorization:$BASIC_AUTH" grant_type=password username=damien.dellamico@gmail.com password=test

```

Sample response :

```bash
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpZCI6IjU2OTI4MjBmNzgyMmU3OTMyMmQ2NzFlMSIsInVzZXJuYW1lIjoiZGFtaWVuLmRlbGxhbWljb0BnbWFpbC5jb20iLCJpYXQiOjE0NTc4ODIxNTIsImV4cCI6MTQ1Nzk2ODU1Mn0.LWukMiUOl658AzULZsfYoITyPndUHnjp7JtU1aToQSw",
    "expires_in": "86400",
    "refresh_token": "a6b4be54-2a03-476d-9e0a-d1b4205e457c",
    "token_type": "Bearer"
}


```

Exchange obtained refresh token for access token.

```bash
http POST $BASE_URL/auth/token "Authorization:$BASIC_AUTH" grant_type=refresh_token refresh_token=a6b4be54-2a03-476d-9e0a-d1b4205e457c

```

Now, let's add the jwt token in a variable in order to simplify the next requests :

```bash
JWT_TOKEN="eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpZCI6IjU2OTI4MjBmNzgyMmU3OTMyMmQ2NzFlMSIsInVzZXJuYW1lIjoiZGFtaWVuLmRlbGxhbWljb0BnbWFpbC5jb20iLCJpYXQiOjE0NTc4ODIxNTIsImV4cCI6MTQ1Nzk2ODU1Mn0.LWukMiUOl658AzULZsfYoITyPndUHnjp7JtU1aToQSw"
```

Getting all users :
```bash
http $BASE_URL/users Authorization:"Bearer $JWT_TOKEN"
```
Getting user by id :
```bash
http $BASE_URL/users/56d576d19f73e3c32309636c Authorization:"Bearer $JWT_TOKEN"
```
Creating your user data :
```bash
http POST $BASE_URL/users Authorization:"Bearer $JWT_TOKEN" \
name=trey lastName=azagthoth username=morbid@angel.com password=covenant
```
Updating user data:
```bash
http PUT $BASE_URL/users/56e56c3a54028fd7343f52d0 Authorization:"Bearer $JWT_TOKEN" \
name=Abbath lastName=Occulta active=true phone=1-770-736-8031-666 website=http://www.immortalofficial.com
```
Deleting user :
```bash
http DELETE $BASE_URL/users/56e5380b07fd69e524f81d2e Authorization:"Bearer $JWT_TOKEN"
```
Getting user's images :
```bash
http $BASE_URL/users/56e56c3a54028fd7343f52d0/images Authorization:"Bearer $JWT_TOKEN"
```
