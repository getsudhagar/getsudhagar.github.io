---
layout: post
title:  "When do we use Lambda expression for functional interface"
categories: [ Home, blog ]
image: assets/images/lambda_expression.png
featured: false
---

In java 8 everybody knows that we can use lambda expressions on language provided functional interface and user created functional interface. Let's see when to use lambda expressions for user defined functional interface. 

### What benefit Lambda Expression provides in Java?
Lambda expressions does not do any magic other than reducing number of lines code. It means if one uses Lambda Expressions in the application's logic, it does not mean one wrote efficient logic. It only reduces number of lines of code, nothing other than that.
So one does not use Lambda Expression, no harm, still one can write efficient application logic.


### Why Lambda Expression came? and what was there in Java earlier?
Lambda expression is just replacement of anonymous inner class. Every time we have couple of more lines even if we have to write one line of application code logic. It looked ugly and unnecessary number of lines added. 

### When do we need to use anonymous inner class?
Anonymous inner class is a inner class with no class name. When ever we see an interface with only method expected to be number of unknown implementation in through out the application. It means concrete implementation of the method can be varied during writing of the business logic. It is painful to write concrete implementation every time during business logic.

For Instance,
In the AWT or Swing application, we use to see addListener method on ButtonListerner interface. Implementation of AddListener method is not same for every button. add button we have to write one action and edit button we have to write different action and go on.

````
addButton.addListerner(new AddButtonAction(){

//action code goes here

});

editButton.addListener(new EditButtonAction(){
//action code goes here
});

````
Look at above it easy to write in line code during writing business logic for add and edit action button. If no anonymous inner class, we would have end up with writing separate concrete class for every button, it is very painful for every time.
This is where exactly anonymous class is being used in java. Above syntax is little bit ugly by looking at and we have write curly brace every time even for one line code. 

This is where exactly Lambda Expression and replaces this ugly syntax. So above syntax becomes like below

````
addButton.addListerner(() -> y);

````
### So when do I write Lambda expression for user defined functional interface?
In java 8 by default every interface with only one method is a functional interface. But I see two reason we should not Lambda Expressions though functional interface.

+ If we don't have to do more than one implementation.
+ If we have to write more number of lines of code.

Lambda expression use case,
Suppose if we use case where we have to compare two employees but every time we may need to use different attributes of employee object. 

 + By employee id
 + By employee name
 + By employee date of birth and etc.,
 
 So in this case write Comparable interface with one compare method. We can write implementation using Lambda expression when ever we need of writing business logic.
 
 For example,
 
 ````
 Comparable comp = (emp1,emp2) -> emp1.id < emp2.id;
 Comparable comp = (emp1,emp2) -> emp1.name < emp2.name;
  
 ````
So every functional interface is not worth to use Lambda expression. Application code become less readable and messy if we use Lambda expression in wrong place.
 
 
 
 
 
 
 



 



