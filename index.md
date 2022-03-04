# The anatomy of the event-stream attack

_This article mainly serves to analyze the malicious code of the attack. For a more thorough analysis of the incident, please consult [this]() paper_

### Introduction

In late 2018, a supply chain attack targeting the Bitcoin Wallet app [Copay]() took place. The [npm]() package served as the host of the malicious code through its dependency [flatmap-stream](), another npm package. The malicious code reached the Copay application and affected several versions on end user's devices. The main attacking technique that was utilized was code injection. Over the next few sections we will be examining the malicious code that was used to carry out the attack.

### The social engineering aspect of the attack

The attack commenced firstly as a social engineering effort, as both event-stream and flatmap-stream were both non malicious npm packages on which (especially event-stream) [a large amount]() of packages depended on. A malicious user, @right9ctrl contacted the original author and maintainer of event-stream offering to make contributions to the package. The latter, after a few innocuous contributory commits by the user, gave them ownership of the package along with npm publishing rights. Soon after, the flatmap-stream dependency was introduced to event-stream. The malicious code appeared in a consequent version of flatmap-stream and moved downstream through event-stream (which now depends on flatmap-stream) to Copay.

### Quick Jump

[Paper](https://es-incident.github.io/paper) | [Payloads](https://es-incident.github.io/payloads) | [Defence](https://es-incident.github.io/defences)
