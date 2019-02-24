---
layout: post
title:  "Static methods vs Singleton class"
categories: [ Home, blog ]
image: assets/images/java-singleton-design-pattern.jpg
featured: false
---

In java there are two ways to duplicate memory usage by using either static methods or singleton classes. But is there any rule when we have to use singleton class over static or vice versa?

### When do we create static method?
When we feel certain business logic of a method is constant to all the instances of the class then we make that method as static method. So that particular method will not be duplicated in all the instances of that class.

### So what advantages we will get if we make a method as static?
* Avoiding unecessary usage of stack memory.
* Static method can be accessed using class name easily.
	
### Does static method gives any advantages over instance method on thread execution?
No, both methods would be executed on thread's stack memory only. Thread treats both methods in same way in execution.

### When do we use singleton class.
we can use singleton class when we think one instance is enough for particular class. Singleton class makes sure only one instance is exist at any point of time. 
	
### What benefit singleton class gives?
It provides same benefit as static class provides. It avoid unncessary usage of heap memory by duplicate instances of the same class. 

### Singleton class is same as if we make all the methods are static in one class?
Yes, Both are same by benifit. 
	
### So which one we should use either singleton or all static methods?
I prefer to use singlton class over all static methods. I see one benefit slightly. If we use singleton we can override methods if in case application requires different behavior for certain cases. Otherwise I think choice is purely developers convenince.
	
	 



