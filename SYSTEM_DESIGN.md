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
