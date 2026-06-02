## 02nd June 2026: "Circuit Breaker"

I learnt about this pattern while exploring how to prevent cascading failures in distributed systems specifically when one slow service can take down everything upstream.

We all must have experienced a scenario where say Service A relies on Service B for response. B starts failing or responding slowly. A keeps retrying. A's thread pool fills up. Now A is also slow.  

Circuit breaker exists to stop this cascade early.

**The core idea**
* Wrap every outgoing call in a circuit breaker. It monitors failures. 
* If failures cross a threshold (that we set), it trips and stops forwarding calls to B entirely. 
* A gets a fast failure signal and can invoke its own fallback (e.g. cached response). Note the fallback is A's responsibility, not the breaker's.

**Example**
* Think of [litellm](https://docs.litellm.ai/docs/providers/github) which acts as gateway for different model providers. Say primary model which you're requesting is facing some outage. Should all requests still hit primary model endpoint? Doesn't make sense, right? 
* Circuit breaker trips on the failure threshold, routes everything to the backup model instantly, and probes the primary quietly in the background. No timeout wait. No cascade.

## 26th May 2026: "Ports & Adapters (Hexagon) Architecture"

Today, I learnt about this artchitecture while I was trying to understand how to keep business logic clean from atmospheric components. 

For sake of our understanding, let's take an example of an AI Chatbot which has a tool for web search. And 2 providers of web search are available parallel and perplexity.

So this architecture has 3 concepts:
* Core - This is your business logic. No import of parallel or perplexity python libraries in core. Core consists of logic like chatbot's orchestration logic, or Agentic Skills that talk about when to call which web search tool.

* Port - This is the core's stated needs (Python Protocol). Say, a web search tool call interface. And given a situation, this interface will be invoked.

* Adapters - Actual implementation of port per type of implementation. One port -> Multiple adapters. Say, parallel and perplexity having their own implementation of port interface.


**Relation between Hexagon and SOLID principles:**
* D (DIP) - Core depends on port (abstraction), not adapter (concretion)
* O (OCP) - New adapter = no change to core
* S (SRP) - Core: business logic only. Adapter: translation only
