services_oop
============
Allows declarative definition of resource-based [services](http://drupal.org/project/Services) through class declarations.

## *THIS IS A WORK IN PROGRESS* ##

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

Resource declaration
--------------------
The resource class itself can have three different annotations; *Relationship*, *Action* and *TargetedAction*

### Relationship ###
Relationships let you specify a method that returns an *index* of related objects. Any easy to understand example could be getting all comments ID's for a specific node.

It's considered good practice to only return an index, not a list of complete objects.

#### Defining a Relationship ####
A relation ship is defined on the resource that means it should be defined in the doc comment for the class.

The definition looks like this
   @Relationship(name='a_name', controller='method')
* `name` is used to map the controller method to an URI.
* `controller` is the name of the callback method.

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
* `name` is used to map the controller method to an URI.
* `controller` is the name of the callback method.

#### Accessing an Action ####
If you're using the _REST server_ you would access this relationship by performing a GET call to the following URL:
   endpoint/resource/nameOfRelationship

### Targeted Actions ###
The last of the resource wide annotations (there's actually one more provided by REST Server. I'll talk about that one briefly below) is the targeted action.

Practical uses may include publishing a specific node or perhaps getting the name of a nodes author.

#### Defining a Targeted Action ####
Also defined on the class, written like this:
   @TargetedAction(name='a_name', controller='method')
* `name` is used to map the controller method to an URI.
* `controller` is the name of the callback method.

#### Accessing a Targeted Action ####
A targeted action is accessed the same way as a relationship.

If you're using the _REST server_ you would access this relationship by performing a GET call to the following URL:
   endpoint/resource/id/nameOfTargetedAction