# Developing Reactive Microservices (by Markus Eisele)

## Are Microservices just SOA

* yes - thoughts behind isolation, composition, integration, and discrete and autonomous esrvices are the same
* no - complicated systems where ESB was used to hook up multiple monoliths communicating over complicated protocols

## Reactive Microservices principles (reactive manifesto):

* *responsive* - responsive application satisfies consumer's expectations in terms of availability and realtime response. Responsiveness is measured in _latancy_ 
* *resilient* - handling failures of an individual system in a gentle way without affecting the complete system
* *elastic* - possibility to scale up and down
* *message-driven* - loosely couples services with explicit communication protocol over messages and async non-blocking API 

