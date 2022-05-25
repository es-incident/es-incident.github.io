# The anatomy of the event-stream attack

<!-- ### Introduction

In late 2018, a supply chain attack targeting the Bitcoin Wallet app [Copay]() took place. The [npm]() package served as the host of the malicious code through its dependency [flatmap-stream](), another npm package. The malicious code reached the Copay application and affected several versions on end user's devices. The main attacking technique that was utilized was code injection. Over the next few sections we will be examining the malicious code that was used to carry out the attack.

### The social engineering aspect of the attack

The attack commenced firstly as a social engineering effort, as both event-stream and flatmap-stream were both non malicious npm packages on which (especially event-stream) [a large amount]() of packages depended on. A malicious user, @right9ctrl contacted the original author and maintainer of event-stream offering to make contributions to the package. The latter, after a few innocuous contributory commits by the user, gave them ownership of the package along with npm publishing rights. Soon after, the flatmap-stream dependency was introduced to event-stream. The malicious code appeared in a consequent version of flatmap-stream and moved downstream through event-stream (which now depends on flatmap-stream) to Copay. -->

### Introduction

In today's software development world, developers encapsulate and share
reusable functionality through the use of software
*dependencies*---often called modules, libraries, packages, or imports.
Software dependencies offer several benefits: they offer additional
functionality that a developer might want to invoke from within their
program, without them having to implement that functionality. The use of
modern dependency (or package) managers has simplified sharing and
dealing with third-party dependencies. Package managers automate the
downloading and installation of software dependencies. This automation
has resulted in an explosion of third-party dependency use and re-use,
to the point where public language ecosystems experience exponential
growth. And as dependencies have themselves dependencies---often called
transitive or recursive dependencies---the resulting numbers of
dependencies included in modern software is concerning: modern
applications feature hundreds or thousands of dependencies, to the point
where the vast majority of the code comprising a modern application is
not written by its nominal
developers.

**Supply-chain attacks** This trend has profound security implications
and has given rise to *supply-chain attacks* employed increasingly by
malicious adversaries. Rather than directly targeting a victim software,
these attacks target a victim's software supplier to which the
adversaries have direct access. Long supply chains translate to a
disproportionately attack surface which is also easier to study and
manipulate, as dependencies and their acquisition channels are not
protected at the same degree as the software component that depend on
them. Open source software is the most prominent target for supply-chain
attacks, due to the large amount of reused open source components and
the limited engineering resources available to most organizations
building on these open source components.

In the case of open-source software, attackers can exploit
vulnerabilities that are widely known---for example, by browsing the bug
repository of a software project. Increasingly, however, adversaries
purposefully insert vulnerabilities they later exploit---at times, years
after the software dependency is formed. This gives adversaries
significant control over the nature and specifics of the attack, which
if hidden well can be lurking in the dependency chain for years and
affect a very large number of projects.

**Event-stream incident** In 2018, such a supply-chain attack involved a
library called `event-stream` and meant to simplify working with
data-streams. At the time of the incident `event-stream` was used
by thousands of applications and averaged about two million downloads
per week. The attack was highly targeted, focusing on stealing the
wallet credentials of users with wallets of certain amount of Bitcoin or
Bitcoin cash. The attack succeeded, directly affecting several users,
and caused a significant outcry in both the JavaScript and
crypto-currency communities. In this work, we report the
results of a thorough independent analysis on the `event-stream`
incident. As the incident is symptomatic of much deeper, more insidious
problems across the entire ecosystem, a broader incident analysis
highlights several factors that need to be addressed in order to prevent
further accidents of a similar kind in the future.

### Quick Jump

[Full Paper Text](https://es-incident.github.io/paper) - [Payloads](https://es-incident.github.io/payloads) 
