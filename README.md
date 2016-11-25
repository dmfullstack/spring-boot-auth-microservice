# spring-auth-microservice
Try implement authetication and api gateway microservices.

## Overview
### auth-service
Create a new user account by using username/password.

Validate JWT token and retreive account information.

### api-gateway
Route all incoming messages to corresponing service.

Validate JWT token if request url containing **/private**.

Reject all private APIs access if failed JWT validation. 

![overview](https://raw.githubusercontent.com/oraclebox/spring-auth-microservice/master/docs/overview.png)

## Require Install 
### Mongodb 
```
 docker run --name mongo -p 27017:27017 -d mongo
```
### Redis
```
 docker run --name redis -p 6379:6379 -d redis
```
### Start spring-boot service
auth-service

api-gateway

## Create account
POST http://localhost:8888/auth/v1/continuous with JSON
```
{
	"username": "Oraclebox",
	"email":"abc@gmail.com",
	"password":"pw31931836193@SEED"
}
```
Response will give you JWT token
```
{
  "code": 200,
  "message": "Success",
  "token": "{JWT token.....}",
  "data": {
    "id": "5837f61325f710407084a13e",
    "username": "Oraclebox",
    "email": "abc@gmail.com"
  }
}
```

### Try to access private service
GET http://localhost:8888/auth/private/v1/greeting with Header

Authorization: Bearer {JWT token.....}

Return 200 if success, 403 if token invalid.

## Add New API to getway which require token validation. 
For example add http://localhost:9300/book/private/remove/{id} and http://localhost:9300/book/list

api-gateway > application.yml
```
zuul:
  sensitiveHeaders: Authetication
  routes:
    auth: #authetication service
      path: /auth/**
      url: http://localhost:9000/auth
    book: #authetication service
      path: /book/**
      url: http://localhost:9300/book      
```
The can access them via API Gateway
http://localhost:8888/book/private/remove/{id} (Require JWT header because url contain /private)
http://localhost:8888/book/list

