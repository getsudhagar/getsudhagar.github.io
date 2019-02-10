---
layout: post
title:  "A Easy JMS Connection Pooling"
categories: [ Home, blog ]
image: assets/images/jms_connection_pool.jpg
featured: true
---

I have written a simple JMS connection pool class to save connection creation time during message producing or consuming time.

````
JMSConnectionPool.java

package com;

import java.util.concurrent.BlockingQueue;
import java.util.concurrent.LinkedBlockingQueue;
import java.util.concurrent.TimeUnit;

import javax.jms.Connection;
import javax.jms.JMSException;

import org.apache.log4j.Logger;


/**
 * This is simple JMS connection pool class using BlockingQueue java collection.
 */
public class JMSConnectionPool  {

  private static final Logger LOGGER = Logger.getLogger(JMSConnectionPool.class);
 private final BlockingQueue<Connection> jmsConQueue;
 private final int maxSize;
 public JMSConnectionPool(int initConns, int maxConns,
   boolean waitIfBusy) throws JMSException
 {

   maxSize = 10; //Number of concurrent request
  jmsConQueue = new LinkedBlockingQueue<>(maxConns);

   for (int i = 0; i < initConns; i++) {
   jmsConQueue.add();

   }

   LOGGER.info("Initial JMS Connection Pool Size is:"+initConns+" and Max connection limit is:"+maxConns);
 }


  /**
  * Method to free the Connections
  */
 public void free(Connection connection) {

   jmsConQueue.add(connection);

   LOGGER.info("Add released connection back to the pool and pool size is:"+jmsConQueue.size());

  }

  /**
  * This method would pull the connection from connection pool whenever requested.
  */
 public  Connection pullConnection() throws JMSException {

   LOGGER.info("Current JMS connection pool size is..:"+jmsConQueue.size());

   Connection connection = null;
  if(jmsConQueue.isEmpty()) {
   jmsConQueue.add(/* JMS connection creation method goes here */);
  }

   try {
   connection = jmsConQueue.poll(1, TimeUnit.SECONDS);

    while (null == connection) {
    connection = pullConnection();

    }
  } catch (InterruptedException e) {
   LOGGER.error(e.getMessage(),e);

   }



   return connection;

  }
  
  

 /**
  * Actual JMS connection creation logic goes here.
  */
 public Connection createConnection() throws JMSException {
  Connection connection = /* JMS connection creation method goes here */;
 
  return connection;
 }


  /**
  * This prints connection pool details whenever this method is called.
  */
 @Override
 public String toString() {
  String info = "MaintainSubjectStubPool: available=" + jmsConQueue.size() + ", max=" + maxSize;
  return info;
 }



}
````



