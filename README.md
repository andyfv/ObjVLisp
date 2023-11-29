# ObjVLisp

Basic Reflective Object Kernel from Stephane Ducasse. Based on the VLisp extension from 1984. 

Written in Smalltalk. 

## Considerations               

From a usability point of view an Object system has three elements:

- **Metaclasses** : classes that generates other classes. Their instances are normal classes.

- **Classes**     : classes that are instances of a metaclass. They respond to the message **new:** and create instances.

- **Instances**   : instances of the classes.

----

We have to take into account the needed structures and behaviours to implement
the three elements.

**Instances**:
- Structure:
  - instance variables: ordered sequence of instance varaibles defined by a class
  - pointer to its class
- Behaviour:
  - methods: belong to the class of the insance. They are stored into a method dictionary(selector -> method body).

**Classes**:
- Structure:
  - list of instance variables: to hold the state of the created instance
  - method dictionary: to hold the behaviour of the instances
  - superclass pointer: to lookup inherited methods

## Message sending
This is the computational step. It is a two step process:

1) the method corresponding to the selector is looked up.

2) If the method is found it its applied to the receiver.

## Method lookup.
Here is the expanded method lookup process:

1) start the lookup at the class of the receiver

2) if the method is defined in that class, return it

3) otherwise continue the search in the superclass

4) if no method is found and there is no superclass to explore, this is an error

## Object creation:
Object creation is a two step process:

1) Memory allocation

2) Object initialization

An Object is implemented as an Array with the following structure primitive:
- (1) : identifier to its class
- (2) : its name
- (3) : identifier to its superclass
- (4) : collection(array) of instance variables
- (5) : colelction(array) of initialization keywords:
- (6) : method dictionary

Example: 
**#( #ObjClass #ObjPoint #ObjObject #(class x y) #(:x :y) nil )**

at: **offsetForClass**        (1) -> **#ObjClass**

at: **offsetForName**         (2) -> **#ObjPoint**

at: **offsetForSuperclass**   (3) -> **#ObjObject**

at: **offsetForIVs**          (4) -> **#(class x y)**

at: **offsetForKeywords**     (5) -> **#(:x :y)**

at: **offsetForMethodDict**   (6) -> **nil** (empty method dicitonary)

---
This implementation consists from only two classes - **Class** and **Object**.

- **Object** - root of the inheritance graph and is an instance of Class.
- **Class**  - the first class and root of the instantation tree and the instance of itself(meta-circularity). It is the initial class and the metaclass.

This implementation unifies **Classes** and **Instances(Objects)**
