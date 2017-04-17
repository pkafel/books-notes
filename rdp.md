# Reactive Design Patterns (by Roland Kuhn)

## Chapter 1

### Reactive Manifesto:
* *responsive* - responsive application satisfies consumer's expectations in terms of availability and realtime response. Responsiveness is measured in _latancy_ 
* *resilient* - handling failures of an individual system in a gentle way without affecting the complete system
* *elastic* - possibility to scale up and down
* *message-driven* - loosely couples services with explicit communication protocol over messages and async non-blocking API 

### Replication possibilities:
* *Active–passive replication* - Replicas agree on which one of them can accept updates. Fail-over to a different replica requires consensus among the remaining ones when the active replica no longer responds.
* *Consensus-based multiple-master replication* — Each update is agreed on by sufficiently many replicas to achieve consistent behavior across all of them, at the cost of availability and latency.
* *Optimistic replication with conflict detection and resolution* — Multiple active replicas disseminate updates and roll back transactions during conflict or discard conflicting updates that were performed during a network partition.
* *Conflict-free replicated data types* — This approach prescribes merge strategies such that conflicts cannot arise by definition, at the cost of providing only eventual consistency and requiring special care when creating the data model.

## Chapter 2

Setting ratio between number of server threads and connection pool:
* no point in having bigger connection pool than number of server threads
* when equal you might have all the requests threads waiting for response from DB
* smaller number of connection pool than request threads might result in bottleneck

Number of connection pool can be calculated by applying Little's Law:
> The long-term average number of customers in a stable system L is equal to the long-term average effective arrival rate, λ, multiplied by the (Palm‑)average time a customer spends in the system, W; or expressed algebraically: L = λW.

Example:
L = 500 requests/second × 0.03 seconds/request

### Exploiting parallelism
* sequential and blocking
```java
ReplyA a = computeA();
ReplyB b = computeB();
ReplyC c = computeC();
Result r = aggregate(a, b, c);
```
* asynch and blocking
```java
Future<ReplyA> a = taskA();
Future<ReplyB> b = taskB();
Future<ReplyC> c = taskC();
Result r = aggregate(a.get(), b.get(), c.get());
```
* asynch and non-blocking
```scala
val fa: Future[ReplyA] = taskA()
val fb: Future[ReplyB] = taskB()
val fc: Future[ReplyC] = taskC()
val fr: Future[Result] = for (a <- fa; b <- fb; c <- fc)
                         yield aggregate(a, b, c)
```
Using synchronous, blocking APIs that hide the underlying message-driven structure wastes CPU resources. If messages were made explicit in the API such that instead of suspending a thread, you would just suspend the computation—freeing up the thread to do something else—then this overhead would be reduced substantially.

### Amdahl's Law
Amdahl’s Law specifies the maximum increase in speed that can be achieved by adding additional threads and is formulated as:
`S(n) = N / (1 + α (N – 1))`
where
* N is number of available threads
* α is the fraction of the program that is serialized

### Universal Scalability Law
Amdahl’s Law also does not take into account the overhead incurred for coordinating and synchronizing the different execution threads. A more realistic formula is provided by the Universal Scalability Law:
`S(n) = N / (1 + α(N – 1) + βN (N – 1))`
where:
* β is a fraction of time spent ensuring that the data throughout the system is consistent (coherency), combines all the delays associated with coordinating between threads to ensure consistent access to shared data structures

### Reacting to failure
There is only one generic way to protect your system from failing as a whole when a part fails: distribute and compartmentalize. The former can informally be translated as “don’t put all your eggs in one basket” and the latter adds “protect your baskets from one another.”

Distribute example:
* Bulkhead
* Circuit breaker

Compartmentalize example:
* Supervisor

### Consistency

#### CAP theorem
Networked shared-data system can have at most two of three desirable properties:
* Consistency (C) equivalent to having a single up-to-date copy of the data 
* High availability (A) of that data (for updates)
* Tolerance to network partitions (P)

#### BASE
* Basically available
* Soft state (state needs to be actively maintained instead of persisting by default) 
* Eventually consistent

#### ACID 2.0
* Associative
* Commutative 
* Idempotent 
* Distributed

### Managing complexity
* Essential complexity is the kind that is inherent in the problem domain. 
* Incidental complexity is the kind that is introduced solely by the solution.

## Chapter 3

### Existing support for Reactive Design

* Green threads
> Green threads are asynchronous and nonblocking, but they do not support message passing. They do not scale up to use multiple cores on a machine by themselves, although if the runtime supports it, it is possible to have more than one in a process, or multiple processes can be run. They do not scale outward across nodes in a net- work. They also do not provide any mechanisms for fault tolerance, so it is up to devel- opers who use them to write their own constructs to handle any failure that may occur.

* Event loops
> The suitability of an event loop for a Reactive application depends on the implemen- tation. As deployed via Node.js in JavaScript, event loops are similar to green threads in that they are asynchronous and nonblocking but do not support message passing. They do not scale up to use multiple cores on a machine by themselves, although if the runtime supports it, it is possible to have more than one in a process, or multiple processes can be run. They do not scale outward across nodes in a network. They also do not provide any mechanisms for fault tolerance, so it is up to developers to write their own constructs to handle any failure that may occur.

* Communicating Sequential Processes (CSP)
> CSP is asynchronous and nonblocking and supports message passing in rendezvous fashion. It scales up to use multiple cores on a machine, but none of the current implementations scale outward across nodes. CSP does not provide any mechanisms for fault tolerance, so it is up to developers to write their own constructs to handle any failure that may occur.

* Futures and promises
> Futures and Promises are asynchronous and nonblocking, but they do not support message passing. They do scale up to use multiple cores on one machine. Current implementations do not scale outward across nodes in a network. They do provide mechanisms for fault tolerance when a single Future fails, and some implementations aggregate failure across multiple Futures such that if one fails, all fail.

* Reactive extension
> Rx provides facilities to process streams of data in an asynchronous and nonblocking fashion. The current implementations scale up to use multiple cores on a machine but not outward across nodes in a network. Rx does not provide a mechanism for del- egating failure handling, but it does include provisions to reliably tear down a failed stream-processing pipeline via dedicated termination signals. RxJava in particular is a useful building block for implementing components of a Reactive system.

* The actor model
> Actors are asynchronous and nonblocking, and support message passing. They scale up to use multiple cores on one machine, and they scale outward across nodes in both the Erlang and Akka implementations. They provide supervision mechanisms, as well, in support of fault tolerance. They meet all the requirements for building Reactive applications. This does not mean Actors should be used for every purpose when build- ing an application, but they can easily be used as a backbone, providing architectural support to services that use other Reactive technologies.

## Chapter 4

### Flow control

> Flow control is the process of adjusting the transmission rate of a stream of messages to ensure that the receiver is not overwhelmed. Whenever this process informs the sender that it must slow down, the sender is said to experience back pressure.

Two basic flow control schemas:
* NACK - consumer rejects new messages when is still busy
* ACK - producer waits for consumer to let him know when to deliver more messages

### Delivery guarantees
* At-most-once
* At-least-once
* Exactly once

Implementing a runtime environment with very strong delivery guarantees is expensive in that extra mechanisms need to be built in — for example, resending network messages until receipt is confirmed - and these mechanisms degrade performance and scalability even when no failures occur.

### Message vs event

In event based systems producers are addressable whereas in message based systems consumers are.

## Chapter 5