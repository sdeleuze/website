+++
author = "Sébastien Deleuze"
title = "The huge potential of Kotlin/Wasm"
date = "2023-02-13"
tags = ["kotlin", "webassembly"]
+++

Last week, a preview release of Kotlin/Wasm was announced as part of [Kotlin 1.8.20-Beta](https://kotl.in/1-8-20-Beta#new-kotlin-wasm-target). For me who has been nudging the Kotlin team to work on WebAssembly support [since June 2016](https://discuss.kotlinlang.org/t/webassembly-support/1722), that's a huge step forward even if providing WebAssembly first class support for Kotlin will be a long journey.

I also decided recently to contribute actively by creating [KoWasm](https://github.com/sdeleuze/kowasm), an experimental side project intended to provide WebAssembly Component Model and WASI support for Kotlin/Wasm, with the goal to see those features later supported out of the box.

![Kotlin and WebAssembly logos](/images/kotlin-webassembly.png)

## The upcoming WasmGC revolution

WebAssembly has been initially designed as a compilation target for low level languages like C, C++ or Rust, or to implement Virtual Machines capable of interpreting dynamic languages like Python, Ruby or even JavaScript.

But be aware that contributors to the WebAssembly Garbage Collection (GC) proposal are working to make WebAssembly a suitable target for garbage collected languages like Java, Kotlin or Dart and stop using JavaScript as the Web bytecode and give a chance to other languages to be considered as first class options for frontend development without having to compile to JavaScript like TypeScript does. Notice it is already possible for those languages to target WebAssembly by shipping their own custom Garbage Collector with each application, but the generated artifacts are bigger and I am not sure this way of handling things will be competitive when WasmGC will be out.

WasmGC implementation is an ongoing effort in the 4 major browsers Chrome, Firefox, Edge and Safari, and currently requires a flag to enable it (for example with Chrome or Edge you need to use `--js-flags=--experimental-wasm-gc` command line argument), so it is not yet available out of the box and that's what prevents any wide adoption of this technology today. But when WasmGC will reach [phase 4](https://github.com/WebAssembly/proposals#phase-4---standardize-the-feature-wg) and be available out of the box in most browsers, languages leveraging WasmGC will have a significant competitive advantage.

Kotlin/Wasm has made an early bet on WasmGC, as well as Google which works on [compiling Java to WasmGC in Google Sheets](https://twitter.com/matt_wilkinsonn/status/1619004037193682953) with [J2CL](https://github.com/google/j2cl) and on [Dart](https://medium.com/dartlang/experimenting-with-dart-and-wasm-ef7f1c065577).

## Frontend (and full stack) development

While I have never been super convinced by Kotlin/JS which requires the developer to know both Kotlin and JavaScript ecosystems, I think Kotlin/Wasm is the opportunity to reimagine what can be frontend development. Sure Kotlin/Wasm needs to provide good JavaScript interoperability (it does), but this should be optional.

For now, Kotlin/Wasm provides DOM API, and we will maybe see some Kotlin/Wasm frontend frameworks making it a suitable option for frontend development. I will likely explore how Kotlin/Wasm can be used for frontend combined with a Spring Boot Kotlin/JVM backend to perform full stack development in Kotlin.

But WebAssembly becomes even more interesting when you take another perspective. What if [Compose for Web](https://github.com/JetBrains/compose-jb), the multiplatform incarnation of Jetpack Compose used on Android, would leverage Kotlin/Wasm instead of Kotlin/JS to perform pixel-perfect Canvas based rendering?

What if Kotlin/Wasm could power a new kind of [Jamstack architecture](https://jamstack.org/) that would use WebAssembly instead of JavaScript (see [Kobweb](https://github.com/varabyte/kobweb) currently based on Kotlin/JS for a glimpse of what I mean)?

## WebAssembly Component Model

Another important topic to have in mind to fully appreciate Kotlin/Wasm potential is the [WebAssembly Component Model](https://github.com/WebAssembly/component-model) which is going to make it possible to publish components for WebAssembly from any language to any language supporting WebAssembly. The cornerstone of this effort is the [WIT format](https://github.com/WebAssembly/component-model/blob/main/design/mvp/WIT.md) which allows to describe imports and exports and to generate language-specific bindings. You can learn more in Luke Wagner keynote [The path to Components](https://www.youtube.com/watch?v=phodPLY8zNE).

I did the exercise to see [how WIT translates to Kotlin](https://github.com/sdeleuze/kowasm/blob/999944fef36b5f488e900c170d735236c7b72387/component-model/src/wasmTest/kotlin/org/kowasm/componentmodel/WitToKotlin.kt), and the result looks pretty good. For example, WIT `variant` defined with:

```
variant filter {
    all,
    none,
    some(list<string>),
 }
 ```
Translate to the following Kotlin code:
 ```kotlin
 sealed interface Filter {
    object All : Filter
    object None : Filter
    class Some(val value: List<String>): Filter
}
```

WIT `option<string>` translates nicely to Kotlin idiomatic handling of option values leveraging [null-safety](https://kotlinlang.org/docs/null-safety.html) with `String?`.

I also think that [Kotlin Coroutines](https://kotlinlang.org/docs/coroutines-basics.html) are going to be a pretty strong competitive advantage to bind [Component Model async support](https://github.com/WebAssembly/meetings/blob/main/wasi/2022/presentations/2022-06-16-luke-async.pdf).

You can vote for [KT-56605](https://youtrack.jetbrains.com/issue/KT-56605) if you are interested in Wasm Component support in Kotlin/Wasm.

## WebAssembly package management with warg

You may wonder how WebAssembly components will be published and consumed. An emerging response has been shared recently in [this great talk](https://www.youtube.com/watch?v=lihQEVhOR58) by Bailey Hayes and Kyle Brown. Warg is an ongoing effort to standardize WebAssembly package management, and various instances of WebAssembly package repositories will likely be available. I am not sure what amount of decentralization there will be, but the biggest public instances like the upcoming [Bytecode Alliance](https://bytecodealliance.org/) one will potentially look a bit like Maven Central for the JVM world or NPM for the JavaScript world.

A key point is that those WebAssembly package repositories will be polyglot places. You may argue that Maven Central or NPM are used by other languages than Java and JavaScript, but in practice other languages remain in the shadow of the platform language both on producer and consumer side. Warg and WebAssembly are going to bring polyglot components to the next level.

As shared in my previous blog post [WebAssembly impact on programming languages](https://seb.deleuze.fr/webassembly-impact-on-programming-languages/), I tend to think languages like Rust/C/C++ will be mainly used to produce Wasm components (efficiency, no or very small runtime suitable for the share nothing approach), while languages leveraging WasmGC like Kotlin/Wasm will be mainly used to build applications that will consume those components. Of course, Rust will also be used to create Wasm applications, but my guess is that long term WasmGC languages will dominate for this kind of use case.

You can vote for [KT-56607](https://youtrack.jetbrains.com/issue/KT-56607) to communicate to the Kotlin team that you are interested in fetching WebAssembly component dependencies from warg repositories.

## WASI

To understand what is WASI, I highly recommend reading those 2 amazing blog posts from Lin Clark : [Standardizing WASI: A system interface to run WebAssembly outside the web](https://hacks.mozilla.org/2019/03/standardizing-wasi-a-webassembly-system-interface/) and [Announcing the Bytecode Alliance](https://bytecodealliance.org/articles/announcing-the-bytecode-alliance).

In a nutshell, WASI purpose is to standardize how system interfaces like filesystem, clock, environment variables, command line arguments or standard input/output can be exposed to Wasm applications. There are also other use cases like ML, AI or Cloud storage that could be standardized via WASI. See the list of [WASI proposals](https://github.com/WebAssembly/WASI/blob/main/Proposals.md) for more details.

Kotlin/Wasm does not support yet WASI, but the Kotlin/Wasm team implemented [low level APIs](https://youtrack.jetbrains.com/issue/KT-55589/Basic-support-of-WASI) that allow me to progress on an experimental support incubated in the [KoWasm](https://github.com/sdeleuze/kowasm) project mentioned previously, with the end-goal of getting WASI support out-of-the-box by Kotlin/Wasm. You can vote for [KT-36172](https://youtrack.jetbrains.com/issue/KT-36172) related issue.

Interestingly, it is possible to leverage [Kotlin multiplatform](https://kotlinlang.org/docs/multiplatform.html) libraries like [kotlinx-datetime](https://github.com/Kotlin/kotlinx-datetime) or [Okio](https://github.com/square/okio) by just providing the platform specific part with WASI.

The potential of Kotlin/Wasm + WASI is huge. It could provide an alternative to Kotlin/JVM for deploying applications to the Cloud, the Edge or even Serverless functions. It is increasingly used as an alternative to container images, with nano-second instantiation time, increased security and flexible deployment not tide to a specific hardware or OS. That will for sure remind to some of you "Write once, run anywhere" (WORA) the Java slogan from 1995. If you want to dig deeper into the differences between Java and WebAssembly, you can read the excellent blog post [WebAssembly for the Java geek](https://www.javaadvent.com/2022/12/webassembly-for-the-java-geek.html) written by Edoardo Vacchi.

The main blocker to realize that vision is the lack of support for WasmGC in pure WASI runtimes like Wasmtime, see [wasmtime#5032](https://github.com/bytecodealliance/wasmtime/issues/5032) related issue. For now, the main path to run Kotlin/Wasm WASI application is to leverage [Node WASI support](https://nodejs.org/api/wasi.html).

An updated WASI roadmap has been recently shared by Dan Gohman:
- Preview1: Support existing users, portability
- Preview2: Rebase WASI on Wit (we are here)
- Preview3: Level up Async: future and stream
- WASI 1.0: Standardization

If you want to dig more into WASI, I highly recommend [Dan's Youtube channel](https://www.youtube.com/@sunfishcode/streams).

## Follow up

If you are interested in learning more about Kotlin/Wasm, come to the [Wasm I/O conference](https://wasmio.tech) where Zalim (the Kotlin/Wasm lead) and myself will present a talk introducing [Kotlin/Wasm](https://wasmio.tech/sessions/introducing-kotlin-wasm/). You can also follow me on [Twitter](https://twitter.com/sdeleuze) or [Mastodon](https://mastodon.online/@sdeleuze) for more news on WebAssembly and Kotlin, and [star the KoWasm repository on GitHub](https://github.com/sdeleuze/kowasm).