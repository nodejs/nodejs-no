---
layout: default
title: ES6
---

# ES6 i io.js

io.js er bygget med moderne versjoner av [V8](https://code.google.com/p/v8/).
Ved å holde prosjektet oppdatert med de siste versjonene av denne motoren sørger
vi for at nye funksjoner fra [JavaScript 
ECMA-262 spesifikasjonen](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
er tilgjengelig for io.js utviklere innen rimelig tid. I tillegg vil sikkerhets-
og ytelesesforbedringer komme raskt.

Versjon {{ iojs_version }} av io.js kommer med V8 {{ v8_version }}, som 
inkluderer ES6 funksjoner godt forbi versjon 3.26.33 som vil bli levert med
joyent/node@0.12.x.

## Slutt på --harmony flagget

Med joyent/node@0.12.x (V8 3.26) aktiverte  `--harmony` flagget alle 
**completed**, **staged** og **in progress** ES6 funksjoner sammen
(med unntak av ikke-standard/ikke-harmonisk semantikk for `typeof` som 
ble skjult ved bruk av `--harmony-typeof`). Dette resulterte i at buggy eller 
til og med ødelagte funksjoner som 
[proxies](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)
var like tilgjengelig for utviklere som 
[generators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*),
som hadde veldig få eller ingen kjente problemer. Følgelig var det sikrest
å enten aktivere et fåtall funksjoner ved å bruke spesifikke harmony-flagg (f.eks.
`--harmony-generators`), eller aktivere dem alle og deretter bare bruke
en begrenset delmengde.

Med io.js@1.x (V8 4.1+) forsvinner all denne kompleksiteten. All harmony features
are now logically split into three groups for **shipping**, **staged** and **in
progress** features:

 * All **shipping** features, the ones that V8 has considered stable, like <a
   href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*">generators</a>,
   <a
   href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/template_strings">templates</a>,
   <a
   href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/New_in_JavaScript/ECMAScript_6_support_in_Mozilla#Additions_to_the_String_object">new
   string methods</a> and many others are turned **on by default on io.js** and
   do **NOT** require any kind of runtime flag.
 * Then there are **staged** features which are almost-completed features that
   haven't been completely tested or updated to the latest spec yet and
   therefore are not considered stable by the V8 team (e.g. there might be some
   edge cases left to discover). This is probably the equivalent of the state of
   <a
   href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*">generators</a>
   on 3.26. These are the "use at your own risk" type of features that now
   require a runtime flag: `--es_staging` (or its synonym, `--harmony`).
 * Finally, all **in progress** features can be activated individually by their
   respective harmony flag (e.g. `--harmony_arrow_functions`), although this is
   highly discouraged unless for testing purposes.

## Which ES6 features ship with io.js by default (no runtime flag required)?

 * Block scoping
   * <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let">let</a>
   * <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const">const</a>
   * `function`-in-blocks

As of v8 3.31.74.1, block-scoped declarations are <a
href="https://groups.google.com/forum/#!topic/v8-users/3UXNCkAU8Es">intentionally
implemented with a non-compliant limitation to strict mode code</a>. Developers
should be aware that this will change as v8 continues towards ES6 specification
compliance.

 * Collections
   * <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map">Map</a>
   * <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap">WeakMap</a>
   * <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set">Set</a>
   * <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakSet">WeakSet</a>
 * <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*">Generators</a>
 * <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Lexical_grammar#Numeric_literals">Binary and Octal literals</a>
 * <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise">Promises</a>
 * <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/New_in_JavaScript/ECMAScript_6_support_in_Mozilla#Additions_to_the_String_object">New String methods</a>
 * <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol">Symbols</a>
 * <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/template_strings">Template strings</a>

You can view a more detailed list, including a comparison with other engines, on the <a href="https://kangax.github.io/compat-table/es6/">compat-table</a> project page.

## Which ES6 features are behind the --es_staging flag?

 * <a href="https://github.com/lukehoban/es6features#classes">Classes</a> (strict mode only)
 * <a href="https://github.com/lukehoban/es6features#enhanced-object-literals">Object literal extensions</a></li>
 * <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol">`Symbol.toStringTag`</a> (user-definable results for `Object.prototype.toString`)

## I have my infrastructure set up to leverage the --harmony flag. Should I remove it?

The current behaviour of the `--harmony` flag on io.js is to enable **staged**
features only. After all, it is now a synonym of `--es_staging`. As mentioned
above, these are completed features that have not been considered stable yet. If
you want to play safe, especially on production environments, consider removing
this runtime flag until it ships by default on V8 and, consequently, on io.js.
If you keep this enabled, you should be prepared for further io.js upgrades to
break your code if V8 changes their semantics to more closely follow the
standard.

## How do I find which version of V8 ships with a particular version of io.js?

io.js provides a simple way to list all dependencies and respective versions
that ship with a specific binary through the `process` global object. In case of
the V8 engine, type the following in your terminal to retrieve its version:

```
iojs -p process.versions.v8
```

