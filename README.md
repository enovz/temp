# APIs-based-on-directory-structure

### Synopsis

Attempt of automating API routes generation based on lib/api/resources directory structure. Needs a lot of work and love.

##Code Example

In the lib/api/resources directory every directory(resource) is treated as an API route.

* /api
  * /resources
    * /dogs

Results in generation of a route like: api/dogs

The route needs to offer methods that manipulate the resource in context. So every resource dir has a Model and a Controller.

-Model is a model of resource we are operating on (api/dogs -> Dog.js). 

-Controllers have 2 sections :

* controller actions
* contrller behaviour 

1. Controllers actions is an array of actions that can be preformed on a route. These actions get binded with the resource route to form API endpoints.

```
 actions = [
            {
                method: "get",
                parameter: "",
                func: "getAll"
            },
            {
                method: "get",
                parameter: "/:id",
                func: "getById"
            }
        ];
```

An action is defined by:

* method that is invoking the action
* parameter that a route accepts (same parameter is passed to the controller behaviour)
* func that handles logic for the endpoint

Results in generation of endpoints:

* server.get(api/dogs) => handled by func getAll
* server.get(api/dogs/:id) => handled by func getById



2. Controller behaviour defines implemetations of functions that need to be executed when an action is requested.
* controller behaviours consume a resource object(if not empty) and respond with a response callback. 

```
    getAll(dogs, response) {
        response("get all dogs called");
    }
    getById(dogs, response) {
       response("getById called " + dogs.id);
    }
```

The dogs object descibes the resource requested:

```
    dogs:{
          id: req.params.id,
          data: {
                  name: body.name             
                }        
        }
```
## Nesting routes

Nesting routes is simple as creating a new directory named after a resource which we operate on.

* /api
  * /resources
    * /users
      * /dogs

Results in generation of routes 

* api/users 
* api/users/:users-id/dogs

The api/users/:users-id/dogs route also needs a controller and a model for handeling endpoints. 

Define actions in the controller :

```
   actions = [
            {
                method: "post",
                parameter: "",
                func: "create"
            },
            {
                method: "get",
                parameter: "/:id",
                func: "getById"
            }
        ];
```
The difference in nested routes is in controller behaviour parameters.

``` 
    create(resources, response) {
        response(" user: " + resources.users.id + " called create dog with data: " + resources.dogs.data.name);
    }
    getById(resources, response) {
        response(" user: " + resources.users.id + " called getById on dogs id : " + resources.dogs.id);
    }
```
The resources object descibes the requested resource.

 ```
     resources {
                user:{
                        id: req.params.user-id
                },  
                dogs:{
                        id: req.params.id
                        data: {
                                name: body.name             
                               }        
                      }
               }
``` 

Every request is parsed to a resources object before being handled to corresponding contoller behaviour(handler function),
so the controller behaviour can mold the controller logic referencing the resource in context (users, dogs, resources.users.id, resources.dogs.data.name and do on). 

## Middlewears

Middlewears are defined in the lib/api/middlewear directory.

* there is a middlewear for each route.
* name of the middlewear bears the same name of the route we want that middlewear to be applied to

Common middlewears are defined in the lib/api/middlewear/common directory

* in the common directory we define middlewears that are common to all endpoints 
* require the wanted middlewear in the api/lib/middlewear/index.js, add it to the middlewear object and it will be run on every endpoint

### Motivation

* Ability to work in a "one route/resource at a time" way, without having to define all the API routes, endpoints, logic, so on, but to simply
create a folder named after a route/resource and give it a controller and middlewear.

* Intention is not to create an API in this way, but simply to go from an idea of a API to a rough schema of the service.
With a schema we can refactor and refine the idea an implemetations of the idea.   

##Instalation

* download
* npm install
* create resource dirs, controllers, middlewears
* npm start
          
