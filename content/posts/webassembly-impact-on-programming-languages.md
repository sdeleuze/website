+++
author = "Sébastien Deleuze"
title = "WebAssembly impact on programming languages"
date = "2022-12-20"
tags = ["webassembly"]
aliases = ["/posts/webassembly-impact-on-programming-languages/"]
+++

First some context: WebAssembly Component Model (WIT) is maturing in order to standardize reusable components. When ready, WIT components will likely be published in a repository (like [npm](https://www.npmjs.com/) or [Maven Central](https://search.maven.org/) but for Wasm) in a polyglot fashion targeting any language that supports Wasm.

{{< youtube phodPLY8zNE >}}

Wasi (the Posix of WebAssembly that allows to use it outside of the browser) is currently rebased on top of WIT (tentatively called Wasi Preview 2). With Wasi, Wasm could become a major way to deploy workloads on Edge and Cloud platforms. You will find more information on https://github.com/bytecodealliance/wit-bindgen.

I think it is important to differentiate 2 use cases: languages used to write Wasm components and languages used to consume them. Could be the same language for both, but I think we will see some languages more used for a use case and less for the other.

I think WebAssembly components will typically be implemented with languages like Rust or Zig for new code. Or with C/C++ for the huge ecosystem already developed with those languages. The glue code (the application) where various components are assembled with the business logic will IMO be typically developed with statically-typed garbage-collected languages. WebAssembly Garbage Collector support is making good progresses in order to make Wasm a great target platform for those languages.

It is still unclear which language(s) will lead that space.

About Java, I tend to think it is unlikely to see Oracle investing massively in Wasm because ultimately, it is a JVM killer. Maybe 
the [GraalVM](https://www.graalvm.org/) team will work on native image compiler generating Wasm files instead of native executables. The more serious champion for Java to Wasm these days is Google with [J2CL](https://github.com/google/j2cl) (currently frontend oriented, Wasi is not yet a target). [TeaVM-WASI](https://github.com/fermyon/teavm-wasi), a friendly fork of TeaVM, modified to support WASI and the WebAssembly Component Model, is also worth to follow.

.NET and C# are good candidates with pioneers like [Steve Sanderson](https://twitter.com/stevensanderson) who worked early on Blazor and their recent work on Wasi, but there are limitations (no GC proposal support).

After pivoting to the mobile with [Flutter](https://flutter.dev/), curious to see [Dart upcoming Wasm support](https://medium.com/dartlang/experimenting-with-dart-and-wasm-ef7f1c065577).

[Swift](https://www.swift.org/) could be in theory a great candidate, and [SwiftWasm](https://swiftwasm.org/) community is doing an amazing work, but IMO Apple won't invest too much there.

[Kotlin](https://kotlinlang.org/) with their early but promising Kotlin/Wasm effort is probably my favorite one. Lack of short term plan for Wasi support and JVM-focused tooling are current main limitations IMO.

Wasm is likely to play a big role in Python future with [Pyodide](https://github.com/pyodide/pyodide) and 
[Pyscript](https://pyscript.net/) for data science but footprint and dynamic typing may prevent dominating the application space.

[TypeScript](https://www.typescriptlang.org/) has been designed as a superset of JavaScript, so not sure WebAssembly will become a major target.

[AssemblyScript](https://www.assemblyscript.org/) is a TypeScript-like language compiling to Wasm but it is unclear if it will evolve to become a general purpose language.

[Go](https://go.dev/) compiler generates huge Wasm files, [TinyGo](https://tinygo.org/) optimized ones but it supports only a subset of the use cases and is not "the official one" so not sure how that will evolve.

Notice there are other languages specifically designed for Wasm like [Grain](https://grain-lang.org/), or where Wasm is a key target like [Virgil](https://github.com/titzer/virgil) from [Ben Titzer](https://twitter.com/TitzerBL) and likely many other ones to come.

Middle term, I expect efficiency of compilation to Wasm, WIT and Wasi interoperability to become key differentiators impacting programming languages adoption massively.

PS. This blog post has been created from [this Twitter thread](https://twitter.com/sdeleuze/status/1552695265328496640) I wrote when this website did not exist yet.