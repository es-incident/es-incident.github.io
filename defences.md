---
layout: default
title:  "Mitigation Toolkit"
description: "An in-depth look at the potential defences"
---

### HARP - Supply-Chain Vulnerability Elimination via Active Learning and Regeneration

[ACM](https://dl.acm.org/doi/abs/10.1145/3460120.3484736) - [PDF](http://nikos.vasilak.is/p/harp:ccs:2021.pdf) 

Software supply-chain attacks target components that are integrated into client applications. Such attacks often target widely-used components, with the attack taking place via operations (for example, file system or network accesses) that do not affect those aspects of component behavior that the client observes. We propose new active library learning and regeneration (ALR) techniques for inferring and regenerating the client-observable behavior of software components. Using increasingly sophisticated rounds of exploration, ALR generates inputs, provides these inputs to the component, and observes the resulting outputs to infer a model of the component's behavior as a program in a domain-specific language. We present Harp, an ALR system for string processing components. We apply Harp to successfully infer and regenerate string-processing components written in JavaScript and C/C++. Our results indicate that, in the majority of cases, Harp completes the regeneration in less than a minute, remains fully compatible with the original library, and delivers performance indistinguishable from the original library. We also demonstrate that Harp can eliminate vulnerabilities associated with libraries targeted in several highly visible security incidents, specifically event-stream, left-pad, and string-compare.

### MIR - Preventing Dynamic Library Compromise on Node.js via RWX-Based Privilege Reduction

[ACM](https://dl.acm.org/doi/abs/10.1145/3460120.3484535) - [PDF](http://nikos.vasilak.is/p/mir:ccs:2021.pdf) - [Code](http://github.com/andromeda/mir) 

Third-party libraries ease the development of large-scale software systems. However, libraries often execute with significantly more privilege than needed to complete their task. Such additional privilege is sometimes exploited at runtime via inputs passed to a library, even when the library itself is not actively malicious. We present Mir, a system addressing dynamic compromise by introducing a fine-grained read-write-execute (RWX) permission model at the boundaries of libraries: every field of every free variable name in the context of an imported library is governed by a permission set. To help specify the permissions given to existing code, Mir’s automated inference generates default permissions by analyzing how libraries are used by their clients. Applied to over 1,000 JavaScript libraries for Node.js, Mir shows practical security (61/63 attacks mitigated), performance (2.1s for static analysis and +1.93% for dynamic enforcement), and compatibility (99.09%) characteristics— and enables a novel quantification of privilege reduction.


### LYA - Efficient Module-Level Dynamic Analysis for Dynamic Languages with Module Recontextualization

[ACM](https://dl.acm.org/doi/10.1145/3468264.3468574) - [PDF](http://nikos.vasilak.is/p/lya:fse:2021.pdf) - [Code](http://github.com/andromeda/lya)

Dynamic program analysis is a long-standing technique for obtaining information about program execution. We present module recontextualization, a new dynamic analysis approach that targets modern dynamic languages such as JavaScript and Racket, enabled by the fact that they feature a module-import mechanism that loads code at runtime as a string. This approach uses lightweight load-time code transformations that operate on the string representation of the module, as well as the context to which it is about to be bound, to insert developer-provided, analysis-specific code into the module before it is loaded. This code implements the dynamic analysis, enabling this approach to capture all interactions around the module in unmodified production language runtime environments. We implement this approach in two systems targeting the JavaScript and Racket ecosystems. Our evaluation shows that this approach can deliver order-of-magnitude performance improvements over state-of-the-art dynamic analysis systems while supporting a range of analyses, implemented on average in about 100 lines of code.


* * *
[back](./)
