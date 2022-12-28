+++
author = "Sébastien Deleuze"
title = "My call for Kotlin as a major frontend language"
date = "2018-10-16"
tags = ["kotlin", "webassembly"]
+++

I try to push for quite a long time for [first class support for WebAssembly in Kotlin](https://discuss.kotlinlang.org/t/webassembly-support/1722) because I really believe that frontend development is a domain where Kotlin can be as strong as in mobile, and because this is something that would also help to increase even more the adoption on server-side.

I truly appreciate all the work already done by Kotlin/JS and Kotlin/Native teams. The [dead code elimination tool](https://kotlinlang.org/docs/reference/javascript-dce.html) and the initial WebAssembly support in Kotlin/Native are important steps in the right direction. But I believe that Kotlin needs now to make frontend a real priority to take it to the next level.

### Need for a consistent and unified web frontend strategy

The first point I would like to raise is that what Kotlin needs IMO is a consistent strategy about web frontend wich includes both Javascript and WebAssembly related efforts. I can understand why it started focused on JavaScript and before multiplatform was a thing, but as I repeat it for more than 3 years now, Kotlin will never win on frontend with a JavaScript based strategy but it can win with a proper native frontend effort. What I mean is that if the main strategy is to leverage JavaScript ecosystem from Kotlin, the war is already lost, TypeScript has won. The lack of serious Kotlin/JS ecosystem is IMO a pretty clear confirmation of that.

What I would like to see is a strong Kotlin frontend strategy that would leverage both Kotlin/JS and Kotlin/Native assets via multiplatform APIs that could allow to leverage JavaScript or WebAssembly targets and ecosystems. We are living exciting time where [WebAssembly](https://hacks.mozilla.org/2017/02/a-cartoon-intro-to-webassembly/) provides a real web bytecode (JavaScript is not anymore the web bytecode, this is and will remain a hack) which is suitable compilation target and is supported by all major browsers. WebAssembly opens the web to new languages and technologies where previously being based on JS was required (TypeScript) to be successful.

### Taking inspiration from Rust community

The feedback I hear for 3 years when I say that is that for now Web and DOM API are not available, and we will see when that will be the case. I can understand why frontend was maybe not the biggest priority at that time. But watching what happens on Mozilla side with Rust and Firefox lets me think this is not a real blocking point anymore. I strongly believe that Kotlin frontend effort should take inspiration about the work done on [web-sys crate](https://rustwasm.github.io/2018/09/26/announcing-web-sys.html) from Rust. Please also read [Making WebAssembly better for Rust & for all languages](https://hacks.mozilla.org/2018/03/making-webassembly-better-for-rust-for-all-languages/) and [Calls between JavaScript and WebAssembly are finally fast](https://hacks.mozilla.org/2018/10/calls-between-javascript-and-webassembly-are-finally-fast-%f0%9f%8e%89/) where Lin Clark explains in a very clear way how things should be managed to bring new languages to WebAssembly.

These articles show IMO that the first key step if we want to make Kotlin a real frontend language is to provide a real statically typed Web and DOM API to Kotlin/Native WebAssembly backend, and the good news for Kotlin is that it could be also reused in Kotlin/JS thanks to Kotlin multiplatform capabilties. Kotlin has been successful on the JVM because it provides an awesome standard library that leverages the platform APIs perfectly. I deeply respect all the work done on Kotlin/JS, but the quality of the API generated from the Web IDL is bad (mostly because the source Web IDL is bad), and is a blocking point that prevents a healthy Kotlin frontend to arise. See [KT-20743](https://youtrack.jetbrains.net/issue/KT-20743) and [KT-22635](https://youtrack.jetbrains.net/issue/KT-22635) for a concrete example of what I mean. Watch also [Building a Browser Extension with Kotlin](https://www.youtube.com/watch?v=QaPMKZay_Ig) amazing talk at Kotlinconf 2018 by [Kirill Rakhman](https://twitter.com/Cypressious) on that topic.

### Building a multiplatform Web API

Here again, I think we should just follow what Rust team is doing: leverage a high quality Web IDL (in their case, the one from Firefox source code I think) to build a high quality Web API for Kotlin. And since frontend concerns both Kotlin/JS and Kotlin/Native, I think this is a very nice opportunity to leverage Kotlin unique multiplatform capabilities to build a multipatform Web API that could be used by both Kotlin/JS and Kotlin/Native.

For Kotlin/Native, the same API could work underneath in 2 ways:
 - A compatibility mode where JavaScript calls are used to invoke the Web and DOM API of the browsers
 - A native mode using reference types (already available in Firefox via `javascript.options.wasm_gc` preference) in order to avoid to go through JavaScript. [Host bindings](https://github.com/WebAssembly/host-bindings) will also help when available.
 
The message I would like to share is that we don't have to wait the final version of WASM Web API to begin building this first class Web multiplatform API, we can move forward right now, by providing the right API to the Kotlin frontend ecosystem and adapt gradually when these pure WebAssembly Web API will be widely available.

### Benefits

In that world:
 - Developpers would build a Kotlin/frontend ecosystem where most of the code could be shared between JavaScript and WebAssembly platforms
 - JavaScript would be an interrop concern, not the mandatory building block
 - Frontend frameworks natively developed in Kotlin would arise from the community, like Vue, React or Angular style developed in Kotlin
 - Wrapping JS frameworks with Kotlin would not be the main way to implement a Kotlin frontend
 - We would be able to build frontend libraries and applications without using the complex and time consuming JavaScript toolchain, we would just use Gradle and nothing else
 - We need to provide a way to expose Kotlin/frontend libraries for consumption by WebAssembly based applications without using JavaScript in a mandatory way, like Rust do with its frontend oriented crates
 - Multiplatform Web API can even be used on Kotlin/JVM and Kotlin/Native servers to build [isomorphic applications](https://medium.com/airbnb-engineering/isomorphic-javascript-the-future-of-web-apps-10882b7a2ebc) when renering can be done on server or client side.

### Conclusion

Kotlin is the perfect language for the WebAssembly revolution. Rust is well suited for building high peformance WebAssembly libraries, but Kotlin is the only modern language (with Swift maybe) that is high-level and compatible with the native world.

Kotlin/JS as it is now has been built before multiplatform support, before WebAssembly, using a bad Web IDL. Putting more effort with the current approach will not make Kotlin successful on frontend. There is a need for major shift in Kotlin strategy about frontend that would make it ready for this new context, and multiplatform support gives the opportunity to leverage that in both Kotlin/JS and Kotlin/native worlds.

The key challenge in WebAssembly world is that it requires both native and web skills, and that's usually different people and/or different teams. The technologies successful in this WebAssembly revolution will be those where native and web teams work together or even better, are working in the same team. That's why Mozilla work with Rust and Firefox is visionary and something to take as a source of inspiration. If Kotlin is ambitious enough to adapt to this new context, I am truly convinced it could become a major frontend language.

PS. This blog post has been initially published [as a gist](https://gist.github.com/sdeleuze/0da8c3d6a43c659977a16e017020503b) when this website did not exist yet.