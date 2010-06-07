services_oop
============
Allows declarative definition of resource-based [services](http://drupal.org/project/Services) through class declarations.

## *THIS DOCUMENT IS A WORK IN PROGRESS* ##

Defining a resource
-------------------
Services_oop let's you create a resource as a class with describing comments instead of a gigantic associative array.

If you are familiar with phpDoc or Doxygen it should be fairly ease to get started.

How it works
------------
By using reflection, services_oop can inspect your class and it comments by using the data it finds it constructs a resource for you.

You do not need to worry about performance, this stuff is cached pretty hard.

*All callback methods must be public static*

Annotations
-----------
Annotations are what makes services_oop work. An annotation is basically a property written in a comment prefixed with an at sign. It can look like this:
    /**
     * Class defining the resource
     *
     * @Action(name='occasions', controller='retrieveOccasions')
     */
    class OccasionResource {
     ...
    }
`@Action` is one of the available class annotations and describes an action your resource can take. Read more about `@Action` below

Resource Declaration
--------------------
The resource class itself can have three different annotations; *Relationship*, *Action* and *TargetedAction* (there's actually one more provided by REST Server. I'll talk about that one briefly below).

### Relationship ###
Relationships let you specify a method that returns an *index* of related objects. Any easy to understand example could be getting all comments ID's for a specific node.

It's considered good practice to only return an index, not a list of complete objects.

#### Defining a Relationship ####
A relation ship is defined on the resource that means it should be defined in the doc comment for the class.

The definition looks like this
    @Relationship(name='a_name', controller='method')

   *  `name` is used to map the controller method to an URI.
   *  `controller` is the name of the callback method.

#### Accessing a Relationship ####
If you're using the _REST server_ you would access this relationship by performing a GET call to the following URL:
    endpoint/resource/id/nameOfRelationship

### Action ###
An (untargeted) action is a way of providing an API for performing other actions than *CRUD* and *index* on a resource.

For example you could provide an API for deleting unpublished nodes or an API for close comments on nodes older then x days. It's up to you, really.

This type of actions are performed directly on the resource type, not an individual resource.

#### Defining an Action ####
Just like the relationship annotation, the action is defined on the resource itself and it looks like this:
    @Action(name='a_name', controller='method')

   *  `name` is used to map the controller method to an URI.
   *  `controller` is the name of the callback method.

#### Accessing an Action ####
If you're using the _REST server_ you would access this relationship by performing a GET call to the following URL:
    endpoint/resource/nameOfRelationship

### Targeted Actions ###
The last of the resource wide annotations is the targeted action.

Practical uses may include publishing a specific node or perhaps getting the name of a nodes author.

#### Defining a Targeted Action ####
Also defined on the class, written like this:
    @TargetedAction(name='a_name', controller='method')

   *  `name` is used to map the controller method to an URI.
   *  `controller` is the name of the callback method.

#### Accessing a Targeted Action ####
A targeted action is accessed the same way as a relationship.

If you're using the _REST server_ you would access this relationship by performing a GET call to the following URL:
    endpoint/resource/id/nameOfTargetedAction

Resource Method Declaration
---------------------------
In this chapter I will talk about how to declare methods in your resource. Basically there are three things you can do with a method; you can declare it's *arguments*, it's *access callback* and a *model*.

### Mapping Request Parameters to Your Callback ###
Your server probably isn't magic and thus it can't automatically figure what to do with a request what resources to access. This is where parameters come into the picture.

A `@param` is a method annotation that defines what arguments your resource callback takes. A method can have several `@param`s and they need to be specified in the same order as the function arguments they map against.

`@param` itself takes a few arguments:
   *  type - the data type
   *  name - name of the argument
   *  source - where to get the data from, can be *path*, *param* or *data*.

#### Sources ####
##### Path #####
*Path* maps URL segments to your callback. Here's an example:
    /**
     * Returns the node with the specified ID
     *
     * @param   int   $id   ["path", "0"]
     * @return  object
     */
     public static function retrieve($id) {
       ...
     }

The first URL segment *after* the resource is number zero, since the segments before that are given by your endpoint and resource.

Assume this URL for the retrieve method defined in the example above (using *REST Server*):
    GET http://domain.tld/endpoint/resource/5
This will make services call YourResource::retrieve(5);

##### Param #####
*Param* maps a named GET parameter to your callback. Another example:
    /**
     * Returns the node with the specified ID
     *
     * @param   int   $id   ["param", "nid"]
     * @return  object
     */
     public static function retrieve($id) {
       ...
     }

Do achieve the same as in the *path*-example you would call:
    GET http://domain.tld/endpoint/resource?nid=5

##### Data #####
The *data* option is used in conjuction with POST requests and is simply the entire POST-data array.

For an update request your method may look like this:
      /**
       * Updates a node
       *
       * @param   int     $id   ["path", "0"]
       * @param   object  $data ["data"]
       * @return  object
       */
       public static function update($id, $data) {
         // $id is the nid of the node to update
         // $data is the POST-data to replace old data with
       }

### Controlling Access to Your Callback ###
The `@Access` annotation let's you specify an access callback for your method. The annotation looks like this:
    @Access(callback='callable', args={'arguments'}, appendArgs=true)

   *  _callback_ is the name of a [callable](http://www.php.net/manual/en/language.pseudo-types.php#language.types.callback). If you provide a static method called `access` in your class this would be `YourResourceClass::access`
   *  _args_ a comma separated list of arguments enclosed in curly braces passed to your callback. Mostly used for passing in the context of the access check, *view*, *index* etc.
   *  _appendArgs_ TODO

Return *TRUE* in your access callback to allow access, *FALSE* to restrict access.

### Models ###
TODO