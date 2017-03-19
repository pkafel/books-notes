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
