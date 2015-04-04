---
layout: post
title: "A Real Quick and Clear Intro of Java RMI"
description: ""
category: 
tags: []
published: true
---

*Preface: This semester I'm taking the [Distributed System](http://www.cs.cmu.edu/~15-440/) course. It's one of the best course I've ever taken in CMU. But that's not point - the point is we use RMI a lot. We write our own version of RMI in C, and we use Java RMI to implement distributed systems. It's really fascinating, let me introduce it --*

### What is RMI

RMI(Remote Procedure Call) technique is very useful in distributed system development. Basically, it allows us to call a function on a remote machine **as if** we're calling a local function. It hides many details in the RMI interface, such as marshall and unmarshall packages, network transmission, etc. 

This figure should be able to illustrate the basic idea of RMI:

<img src="/assets/images/ds-rmi.png">

In the figure, local machine A make a remote procedure call, and the function is actually being executed at a remote machine B. While B is executing the code, A waits, and when B finishes the function and return, A gets the returned result - as if the function was executed locally at A. 

With that I believe you should have understood the essence of RMI. Now, to make a clarification, A and B can also be different processes locate on the same machine. From the perspective of A, it doesn't need to know where is B, it doesn't even need to know B exists - it just call the function, as if it's a local function. 

### The Java RMI - Architecture

Let's get to the point here. Java directly provides RMI interface in the <code>java.rmi</code> package. Before looking at the code, here's a rough illustration of how Java RMI works:

<img src="/assets/images/java-rmi.png">

As you can see:

1.  There're three roles:
    *   RegistryServer is responsible to managing the mapping from a service name to a service class
    *   A RMI provider can bind a class instance (with some requirement, which we'll cover later) to a specific name inside a registry server, and
    *   A RMI user/client can download the corresponding class instance by looking up that name in the same registry server
2.  In a registry server, one name can only be mapped to one class instance, but the same class instance can be binded with multiple names. Multiple clients can use the same name to access the same class instance simultaneously

Here client A and B both have access to the class instance provided by RMI provider A. So when client A or B execute some method of that instance, the code is actually being executed at RMI Provider A. And client A and B can run the same method at the same time, if we assume that's no thread synchronization issue in the class AccountManager, because RMI is multi-thread by default. 

### Show Me The Code

Alright, here are the minimum code to implement both the client and the RMI provider.

First of all, we need to have an object that can be binded to the registry. We need to first define an interface:

{% highlight java %}
import java.rmi.Remote;
import java.rmi.RemoteException;

public interface QueueOperate extends Remote {
    public void put(QueueItem item) throws RemoteException;
    public QueueItem poll() throws RemoteException;
    public int size() throws RemoteException;
}
{% endhighlight %}

**Notes**:

1.  The interface must extends <code>java.rmi.Remote</code>
2.  All methods defined in this interface must throw <code>java.rmi.RemoteException</code>

Then, we write a class to implement this interface

{% highlight java %}
import java.rmi.RemoteException;
import java.rmi.server.UnicastRemoteObject;
import java.util.LinkedList;
import java.util.Queue;

public class QueueOperateImpl extends UnicastRemoteObject implements QueueOperate{

    Queue<QueueItem> queue;
    protected QueueOperateImpl() throws RemoteException {
        super();
        queue = new LinkedList<QueueItem>();
    }

    @Override
    public synchronized void put(QueueItem item) throws RemoteException {
        queue.add(item);
    }

    @Override
    public synchronized QueueItem poll() throws RemoteException {
        return queue.poll();
    }

    @Override
    public synchronized int size() throws RemoteException {
        return queue.size();
    }
}
{% endhighlight %}

**Notes**:

1.  This class implements the defined interface. But it must also extends <code>java.rmi.server.UnicastRemoteObject</code>
2.  Recall that RMI in Java is multi-thread. Therefore I add <code>synchronized</code> to all methods to ensure consistency

And the <code>QueueItem</code> is just a very simple class. But since this object will be passed through network during remote invocation, it must implements <code>java.io.Serializable</code>.

{% highlight java %}
import java.io.Serializable;

public class QueueItem implements Serializable {
    int id;
    public QueueItem(int i){
        id = i;
    }
}
{% endhighlight %}

Finally, we come to the server...

{% highlight java %}
public class RMIServer {
    public static int REGISTRY_PORT = 17283;
    public static String REGISTRY_HOST = "127.0.0.1";
    
    public static void main(String[] args) 
        throws RemoteException, MalformedURLException, AlreadyBoundException{
        // Create the local registry server
        LocateRegistry.createRegistry(REGISTRY_PORT);
        
        // Initialize the class instance we want to bind
        QueueOperate operator = new QueueOperateImpl();
        
        // Bind the class instance to the registry server
        String bindingName = "queueOperate";
        String bindingUrl = String.format("//%s:%d/%s", 
                REGISTRY_HOST, REGISTRY_PORT, bindingName);
        Naming.bind(bindingUrl, operator);
    }
}
{% endhighlight %}

... and the client, which look up the interface from the registry server and use it

{% highlight java %}
public class Client {
    static class ClientThread extends Thread{
        int id;
        public ClientThread(int i) {
            id = i;
        }
        
        private void log(String msg){
            System.out.println("[Client " + id + "]" + msg);
        }
        
        @Override
        public void run(){
            String bindingName = "queueOperate";
            String lookUpUrl = String.format("//%s:%d/%s", 
                    RMIServer.REGISTRY_HOST, RMIServer.REGISTRY_PORT, bindingName);
            try {
                QueueOperate operator = (QueueOperate) Naming.lookup(lookUpUrl);
                Random randomGenerator = new Random();
                while(true){
                    // With 50% probability, it either fetch an item
                    // from the queue, or put a new item into the queue
                    if(randomGenerator.nextDouble() < 0.5){
                        // Fetch
                        QueueItem item = operator.poll();
                        if(item == null){
                            log("Queue Empty");
                        }else{
                            log("Fetch Item " + item.id);
                        }
                    }else{
                        // Put
                        QueueItem newItem = new QueueItem(randomGenerator.nextInt(10000));
                        log("Put Item " + newItem.id);
                        operator.put(newItem);
                    }
                }
            } catch (MalformedURLException | RemoteException
                    | NotBoundException e) {
                e.printStackTrace();
            }
            
        }
    }
    
    public static void main(String[] args){
        // Generate multiple clients to run simultaneously
        int threadNumber = 10;
        for(int i = 0; i < threadNumber; i++){
            new ClientThread(i).start();
        }
    }
}
{% endhighlight %}

We start the server first, then when we start the clients we can get results like:

{% highlight sh %}
>> [Client 3]Put Item 4186
>> [Client 1]Fetch Item 8544
>> [Client 1]Put Item 4368
>> [Client 0]Fetch Item 6889
>> [Client 9]Put Item 6594
>> [Client 5]Fetch Item 1411
>> [Client 5]Put Item 433
{% endhighlight %}

You can download the [source code](/files/rmi_intro.tar). Hope it helps :)

