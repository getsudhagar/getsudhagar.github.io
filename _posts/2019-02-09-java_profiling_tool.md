---
layout: post
title:  "A Simple Java Profiling Tool"
categories: [ Home, blog ]
image: assets/images/java_profiling_tool.jpg
featured: true
---

I have written a simple Java Profiling Tool which will instrument java byte code on class loading time and inject required code on each method.
Here you go for step by step procedures.

### Step 1: Write Agent Class 
````
package com.javaagent;

import java.lang.instrument.Instrumentation;

public class ElamJavaAgent {
   public static void premain(String args, Instrumentation instrumentation){
    
    System.out.println("[Elam Java Agent] Elam Java Agent is instrumenting your application..");
    
     ClassInstrument transformer = new ClassInstrument();
     instrumentation.addTransformer(transformer);
   }
 }
````


### Step 2: Class instrumenting class. 
````
package com.elam.agent.profile;

import java.io.ByteArrayInputStream;
import java.lang.instrument.ClassFileTransformer;
import java.lang.instrument.IllegalClassFormatException;
import java.security.ProtectionDomain;

import javassist.ClassPool;
import javassist.CtClass;
import javassist.CtMethod;
import javassist.CtNewMethod;
import javassist.LoaderClassPath;
import javassist.Modifier;
import javassist.bytecode.BadBytecode;
import javassist.bytecode.ClassFile;
import javassist.bytecode.CodeAttribute;
import javassist.bytecode.CodeIterator;
import javassist.bytecode.MethodInfo;
import javassist.bytecode.Mnemonic;



public class ClassInstrument implements ClassFileTransformer {

  private static final StringBuilder WRAP_METHOD = new StringBuilder();

  static {

   WRAP_METHOD.append("{");
  WRAP_METHOD.append(" long startTimeAgent = System.currentTimeMillis(); ");
  WRAP_METHOD.append(" try { ");
  WRAP_METHOD.append(" {RETURN} {METHOD_NAME} ").append("({PARAMS});");
  WRAP_METHOD.append("}");
  WRAP_METHOD.append(" finally ");
  WRAP_METHOD.append(" { ");
  WRAP_METHOD.append(" long execTimeAgent = System.currentTimeMillis() - startTimeAgent; ");
  WRAP_METHOD.append(" System.out.println(").append("\"").append("[Elam Java Agent]");
  WRAP_METHOD.append(" {INSTRUCT_STMNT} ");
  WRAP_METHOD.append("\"").append("+");
  WRAP_METHOD.append("execTimeAgent").append("+");
  WRAP_METHOD.append("\"").append(" milliseconds.");
  WRAP_METHOD.append("\"").append("); ");
  WRAP_METHOD.append(" } }");

  }


  public byte[] transform(ClassLoader loader, String className, Class<?> classBeingRedefined,
   ProtectionDomain protectionDomain, byte[] classfileBuffer) throws IllegalClassFormatException {

   final String clazzName = className.replace("/", ".");
  final StringBuilder statmntBuilder = new StringBuilder();
  final StringBuilder clazzNameBuilder = new StringBuilder();
  final StringBuilder methodNameBuilder = new StringBuilder();


   ClassPool classPool = null;
  try {


    classPool = ClassPool.getDefault();


    if(clazzName.startsWith("com.elam.") ) {

     classPool.appendClassPath(new LoaderClassPath(loader));


     final CtClass ctClass = classPool.makeClass(new ByteArrayInputStream(classfileBuffer));

     clazzNameBuilder.append(clazzName).append(" ==> ");


     if (!ctClass.isInterface() && !ctClass.isFrozen())
     for (final CtMethod method : ctClass.getDeclaredMethods())
      if(!isGetterOrSetterMethod(ctClass.getClassFile(),method.getName()))
       if(!method.isEmpty()) {

         methodNameBuilder.append(method.getName()).append(" --> ");
        statmntBuilder.append(clazzNameBuilder).append(methodNameBuilder);


         final CtMethod agentMethod = CtNewMethod.copy(method, method.getName()+ "_AgentMethod", ctClass, null);

         makePrivateAccess(agentMethod);

         ctClass.addMethod(agentMethod);

         String methodBody = WRAP_METHOD.toString().replace("{METHOD_NAME}", agentMethod.getName());;
        methodBody = methodBody.replace("{INSTRUCT_STMNT}", statmntBuilder.toString());


         if (null != method.getParameterTypes() && method.getParameterTypes().length > 0)
         methodBody = methodBody.replace("{PARAMS}", "$$");
        else
         methodBody = methodBody.replace("{PARAMS}", "");


         if(method.getReturnType().toString().endsWith("[void]"))
         methodBody = methodBody.replace("{RETURN}", "");
        else
         methodBody = methodBody.replace("{RETURN}", "return");


         method.setBody(methodBody, "this", method.getName());



         methodNameBuilder.setLength(0);
        statmntBuilder.setLength(0);



        }




     final byte[] instrmntdByte = ctClass.toBytecode();



     return instrmntdByte;


    }

   } catch ( final Exception    e) {

    e.printStackTrace();
  }

   return classfileBuffer;

  }


  private boolean isGetterOrSetterMethod(ClassFile classFile,String methodName ) throws BadBytecode {

   final MethodInfo minfo = classFile.getMethod(methodName);
  final CodeAttribute codeAttr = minfo.getCodeAttribute();

   boolean isGetterOpCode = false;
  boolean isSetterOpCode = false;
  int opCodeCount = 0;

   if(!methodName.startsWith("get") &&  !methodName.startsWith("set"))
   return false;

   if( codeAttr == null)
   return true;


   final CodeIterator ci = codeAttr.iterator();


   while (ci.hasNext()) {
   opCodeCount++;
   final int index = ci.next();
   final int opByte = ci.byteAt(index);

    if(Mnemonic.OPCODE[opByte] != null )
    if(Mnemonic.OPCODE[opByte].equals("getfield"))
     isGetterOpCode = true;
    else if(Mnemonic.OPCODE[opByte].equals("putfield"))
     isSetterOpCode = true;

   }



   if(opCodeCount == 4 && isSetterOpCode || opCodeCount == 3 && isGetterOpCode)
   return true;

   return false;
 }

  private void makePrivateAccess(CtMethod copyMethod) {

   copyMethod.setModifiers( copyMethod.getModifiers() & ~(Modifier.PROTECTED | Modifier.PUBLIC)  | Modifier.PRIVATE );


  }

}

````

### Step 3: Add entry into MANIFEST.MF file of jar file

Manifest-Version: 1.0
Premain-Class: com.elam.agent.profile.ElamJavaAgent
Archiver-Version: Plexus Archiver



### Step 4: Add this jar as JVM argument into your running application. 

-javaagent:C:\Users\sudhagar\ElamJavaAgent-1.0.jar




Thats it about simple java profiler tool. This is just beginning, I will continue to enhance this tool to get proper details statistics reports out of it.

Please look at below github repository for complete source code. Thanks

https://github.com/getsudhagar/ElamJavaProfiler
