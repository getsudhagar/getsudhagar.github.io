---
layout: post
title:  "Java Exception handling Do's and Don't"
categories: [ Home, blog ]
image: assets/images/exception_handling.png
featured: true
---
I have worked with many Java projects and have seen several ways of handling exception in the applications. Each one had shown unique approach and had many good things to follow and bad things to avoid. Let's see what are all the good and bad practices..

##Classifying exception classes based out of architectural layer, example BusinessException, ServiceException and etc.
I see only one reason on this classification that we can know that exception is raised from which layer. But what's the use of knowing that issue from what layer, all we care about is what is the issue and what is the root cause of issue. It does not matter from which layer this exception is originated from. So I see this is the bad practice.
###So what type of custom exception is good? Runtime exception or Checked exception?
We have to decide based on type of the application or purpose of the application. For example if we are designing a API kind of application which is going to be used by external parties the we must choose Checked exception, So that we can force to handle certain exception who ever calls certain methods on API. This way consumer can classify this APIs error from their application error. For example JMSException on producing or consuming JMS queue.
But if we are designing application like Micro Service which is consumer by JSON or XML messages. This case we know that we are throwing custom exception from all over application, so we can handle it Controller class. So we have choose Runtime type of custom exception. This way we don't need to have throws clause on methods signatures all over the application.
###Having only one custom exception like ApplicationException is good?
I don't think that's a good approach. Having one custom exception means they throw only one exception every where in the application with different error code and messages.
For Example, throw new ApplicationException(101, "Validation Error"); throw new ApplicationException(102, "Business Error");
Advantages of this way is we can avoid so many exception class but we have to write so many if conditions in the place where we control the behavior of the application based on error. We may or may not report certain errors based on errors. This case we have write if conditions for every error code like below
if(errorCode = = 101 && errorCode == 102 ) { // }
###So having so many exception class is good approach?
Having so many custom exception class is equals to having one custom exception class. Because we have to write plenty of boiler plate code to control the application behavior based on error type like we do on one exception class.
so many custom exception class meant, having custom exception class for each error. For example, MaxLenthException, MinLengthException and etc.
So this approach has two disadvantages 1. We have to write plenty of exception classes 2. we have to write plenty of boiler plate code to control the behavior of the application.
###So what is the good number of custom exception classes we can have?
Having neither one nor plenty of classes are good. we should have reasonable number of custom exception classes. For example, we should have one exception class for all business rule errors, one exception class for all validation errors like below
BusinessRuleException, ValidationException and etc.,
In this way, we can control behavior of the all BusinessRuleErrors in one boiler plate code.
Same Code Snippet
````
try {
}catch(Exception exp) {
if(exp instanceof BusinessRuleException) {
// Our behavior goes here..
}
````
###What would be the best constructor parameters on custom exception class
In many custom exception classes I have ever seen use to take error code and error message parameters. For Instance,
BusinessRuleException exp = new BusinessRuleException(Constants.ErrorCode,Constants.ErrorMessage);
Let's what are all the draw back on this,
1.If we have error code and error message separately then there is chance people can use error code and error message interchangeably. So we should have error code and error message in one variable. Like below
BusinessRuleException exp = new BusinessRuleException(Constants.ErrorCodeMessage);
Think if constructor accepts String type of error code and messages like below
BusinessRuleException(String errorCodeMessage) {
}
In this approach we have one draw back that since constructor take String type there is no guarantee that developers will always refer string type from actual Constant class. If they avoid Constant class then error code and messages are scattered across the application. So we will loose complete control of the application's reporting behavior. Application's future maintenance become worst.
###What would be the better constructor parameters data type?
We should declare error codes and messages as Enum constants and constructor should accept only particular Enum constant type. So that it ensures developers to declare any new error code on Enum only. For Instance,
public enum ResponseCodeMsgEnum {
FID_NO_DL_LINES(new ResponseInfo(1, "Rule does not meet.")),
}
public class ResponseInfo {
private int responseCode;
private String responseMessage;
}
Constructor look like,
public BusinessRuleException(final ResponseCodeMsgEnum errorEnum, final Throwable throwable) {
}
###What are all places we have to throw exception in our application?
There are only two scenario we have throw exception on our application.
1.where ever we would like to throw application's custom error message on meeting certain conditions. For Instance,
if( ssn.legnth() > 9 ) {
throw new BusinessRuleException(ErrorCodeMEssagesEnum.INVALID_SSN)
}
2. Where ever external API's forced handle check exceptions. For Instanc, JMSException
try {
///
}catch(JMSException exp){
throw new MessageException(exp); //Custom exception
}
I don't think any reason we should throw exception or handle exception any where in the application.
We have to have only only place to handle all the exceptions including custom and system exceptions. That class is beginning of our application class, it's mostly our Controller class.
For Instance,
public class MyController {
@RequestMapping(value = "/myTest", method = RequestMethod.POST, consumes = MediaType.APPLICATION_JSON, produces = MediaType.APPLICATION_JSON)
@ResponseBody
public Test myTest(@RequestBody final TestReq testReq) {
try {
//Service call
}
catch(Exception exp) {
//All exception handling behavior would go here
}
}
}
###Do you think custom exception classes hierarchy is required?
Yes, All our application's custom exception should be inherited from base custom exception class. This was we can completely customize the behavior of application errors versus system errors. For instance
try {
}catch(Exception exp){
if(exp instanceof AppException) { //AppException is base exception

//Custom behavior goes here
} else { //All remaining system exceptions fall under here..
}
I hope by following these Do's and Dont's would make application's maintenance easier for ever.