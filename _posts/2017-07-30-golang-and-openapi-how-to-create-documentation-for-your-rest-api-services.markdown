---
title: Golang and OpenAPI(Swagger). How to create documentation for your REST API services
layout: post
date: "2017-07-30 18:06:20"
categories: swagger golang go
---

With popularity of building and maintaining REST API services written with Go, developers face with the problems of documenting it. Swagger (Open API initiative) is a framework for describing your API and making it interactive using text-representative ```yaml``` or ```json``` format that can be understood by developers and non-developers. Benefits of documenting API using swagger:

- Explanation of what the method/resource does.
It helps you to understand what the method does such as scenarios, response codes, query parameters, JSON responses, etc. Moreover, developers can see the changes in API when they review Pull Requests or Merge Requests.

- Easier communication with different teams.
Developers, management, potential clients can participate in designing an API because they can use one of user friendly UIs([swagger-ui](https://swagger.io/swagger-ui/) or [ReDoc](https://github.com/Rebilly/ReDoc)). Therefore nobody nags you in a Slack chat asking about arguments, parameters, you look less in a file with routes, so you can just do your work concentrated on the final result.

- Ecosystem.
Swagger consists  non only of specification but of UI and [editor](https://swagger.io/swagger-editor/) making it easier to work with API documentation.

But this approach has one drawback:

- Time.
Developers have to spend time to understand the standard and support of the existing documentation, tools if there is no documentation team and they have to write documentation themselves.

If you write your REST API with Go, in fact you can use only [go-swagger](https://github.com/go-swagger/go-swagger) to generate docs by golang code. It is complex tool supporting design first API approach, code generation and docs generation by existing Go code.

From the one hand code generation by specification is not so bad idea, but from the other hand I want to control both the code and generation of my API documentation. Even if I decide to use code generation using ```go generate```, I want to controll that process. In addition at the API designing stage we know only how presenters have to look like and don't know the structure the code of future application. Thats why I will show you how to generate swagger spec for method stubs in existing Golang application and I will try to explain some unobvious moments. I will try to describe the situations I faced with and describe how to generate proper specification.

The full code of examples can also be found on [Github](https://github.com/valerykalashnikov/go-swagger-examples)

### Getting started.
Firstly you should install go-swagger. It is described how to do it properly in official documentations or in README.md.
Then we will use syntax of annotations - when swagger meet the comment section with proper tag, parser can understand that tag is for swagger documentation.

### Meta is necessary.
go-swagger generates specs for only compiled code and code with ```swagger:meta``` tag. So, make your code compileable and DON'T FORGET ABOUT ```swagger:meta``` tag. go-swagger will not generate specification without such information. Here we go!

So, let's type some lines of code
~~~go
// go-swagger examples.
//
// The purpose of this application is to provide some
// use cases describing how to generate docs for your API
//
//     Schemes: http, https
//     Host: localhost
//     BasePath: /
//     Version: 0.0.1
//
//     Consumes:
//     - application/json
//
//     Produces:
//     - application/json
//
// swagger:meta
package main

func main() {
}
~~~

Then run ```swagger generate spec -o ./swagger.json ``` to generate specification.
After that running ```swagger serve``` command opens new tab of the browser with ReDoc UI to show the docs in interactive format.

### Your endpoint accepts POST request with non-empty body and returns JSON object
Let's type some code to generate such doc. As an example we imagine that there is some endpoint that creates an order.


~~~go
// An OrderBodyParams model.
//
// This is used for operations that want an Order as body of the request
// swagger:parameters createOrder
type OrderBodyParams struct {
  // The order to submit.
  //
  // in: body
  // required: true
  Order *Order `json:"order"`
}

// An OrderResponse response model
//
// This is used for returning a response with a single order as body
//
// swagger:response orderResponse
type OrderResponse struct {
  // in: body
  Payload *Order `json:"order"`
}

// A ValidationError is a swagger response to represent error
//
// swagger:response validationError
type ValidationError struct {
  // in: body
  Body struct {
    Code    int32  `json:"code"`
    Message string `json:"message"`
    Field   string `json:"field"`
  } `json:"body"`
}

// CreateOrder swagger:route POST /orders orders createOrder
//
// Handler to create an order.
//
// Responses:
//        200: orderResponse
//        422: validationError
func CreateOrder(w http.ResponseWriter, req *http.Request) {
  // your code here
}
~~~
Than enter the commands we used below to generate and serve spec. If you did everything right, your browser opens a new tab with Redoc interface(you can change the interface using ```-u``` option):

![swagger serve example](https://valerykalashnikov.github.io/images/redoc_example.png)


Imagine you want to add some headers to your response, for example Rate-Limit-Remaining. You should add such header to the OrderResponse struct

~~~go
//
// This is used for returning a response with a single order as body
//
// swagger:response orderResponse
type OrderResponse struct {
  //Get number of rate limted requests remaining
  //
  RateLimitRemaining string `json:"Rate-Limit-Remaining"`
  //
  // in: body
  Payload *Order `json:"order"`
}
~~~

### Your method accepts GET request with query arguments and returns list of JSON objects
Let's type some code to generate such doc. As an example we imagine that there is some endpoint that returns a list of orders.

~~~go
// An OrderQueryParams model.
//
// swagger:parameters listOrder
type OrderQueryParams struct {
  // List order limitations.
  //
  // required: true
  // minimum: 1
  // maximum: 12
  Limit int32 `json:"limit"`
}

// GetOrders swagger:route GET /orders orders listOrders
//
// Handler returning list of orders.
//
// List of orders made by current user
//
// Responses:
//        200: []order
func GetOrders(w http.ResponseWriter, r *http.Request) {
  // your code here
}
~~~

### Your method accepts GET request with path argument and returns JSON object
Imagine we want to show detailed user profile made on order(without additional flags it will not generate valid spec):
~~~go
// User available to create an order.
// swagger:model user
type User struct {
  // ID of user
  //
  // required: true
  ID int64 `json:"id"`

  // nickname of user.
  //
  // required: true
  Nickname string `json:"nickname"`
}


// A UserNameParams parameter model.
//
// This is used for operations that want the ID of a user in the path
// swagger:parameters getUserByNickname
type UserNameParams struct {
  // The nickname of user
  //
  // in: path
  // required: true
  Nickname string `json:"nickname"`
}

// Me swagger:route GET /users{nickname} users getUserByNickname
//
// Handler returning information about user.
//
// Information about user
//
// Responses:
//        200: user
func GetUserByNickname(w http.ResponseWriter, r *http.Request) {
  // your code here
}

~~~
If you did the steps we did below to generate and serve spec, you could notice that spec was not generated properly. That's why we defined that response will be instance of ```swagger:model``` without using it at least at one ```swagger:response``` object. But in this case we don't need useless struct especially for swagger generator. In such case use command
```swagger generate  spec -mo ./swagger.json```
and than
```swagger serve swagger.json```

Now it looks good. If you still have proplems with served documentation try to clear browser cache.


### You need to write the full specification for your endpoint.
For example your have complex json fully formed by your database. Modern databases have extended API to work with JSON data type and response of the method (or the great part of it) was formed by database. I found out, that it possible to implement using ```swagger:operation``` tag.
By specification, operation uses to annotate paths or a method. Operation has a uniq ID which is uses in various places or method. So, you can use operation to define, for example, endpoint for current user and endpoint to display profiles of other users.

As you know, custom JSON as json.RawMessage returns from databases by array of bytes and generation mechanism is not detect it as JSON. Now we will write the full spec for the endpoint, for example for statistics reports:

~~~go
// GetOrderStats returns statistics report for specific product
func GetProductStats(w http.ResponseWriter, r *http.Request) {
  // swagger:operation GET /products/statistics products getProductStats
  //
  // Returns how many items was bought, only ordered and returned in the previous week.
  //
  // Could be info for any product
  //
  // ---
  // parameters:
  // - name: country_code
  //   in: query
  //   description: optional filter to obtain statistics by proper country code in ISO 3166-1 numeric
  //   type: integer
  // responses:
  //   '200':
  //     description: "returns statistics about bought, only ordered, and returned products"
  //     schema:
  //       type: array
  //       items:
  //         type: object
  //         properties:
  //           title:
  //             description: title of product
  //             type: string
  //           bought:
  //             description: how many items of specific products were bought in the previous week
  //             type: integer
  //           ordered:
  //             description: how many items of specific products were only ordered in the previous week
  //             type: integer
  //           returned:
  //             description: how many items of specific products were returned in the previous week
  //             type: integer
  //         "required": ["bought", "returned", "ordered"]

  // your code here
}
~~~

Than you have to generate and serve the docs and here we go!

### Summary
Here we looked into different cases of generating swagger docs. The idea of generating documentation is not new and I showed here how to use it with modern tools. I think this article helped you to consider the way how to improve the workflow for your REST API services.











