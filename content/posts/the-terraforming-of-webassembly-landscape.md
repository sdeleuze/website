+++
author = "Sébastien Deleuze"
title = "The terraforming of the WebAssembly landscape"
date = "2024-03-26"
tags = ["webassembly"]
+++

Last week, I took a few days off to go to Barcelona to attend [Wasm I/O](https://wasmio.tech/) 2024 conference. Like last year, the conference was amazing and was still more an event for companies and individuals building the Wasm ecosystem rather than using it, but my feeling is that we are approaching a point where the technology reaches the wider crowd of application and library developers. While WebAssembly can potentially be used with any language, any hardware, for any workload, I would like to share the trends and patterns I see in the ecosystem based on the various talks I saw and discussions I had at the conference.

## WebAssembly Components

While WebAssembly can already run everywhere and everything (including various [Operating Systems](https://copy.sh/v86/)), what is needed right now to reach the huge adoption it deserves, is to allow developers to use WebAssembly as a target to publish/consume their libraries and deploy their applications. Said otherwise, the WebAssembly community needs to specify and implement application-level standards that then will be used by the various programming language ecosystems.

The first related building block is WebAssembly Components, which are portable, lightweight, finely-sandboxed, cross-language and compositional modules as described by Luke Wagner during his talk. In practice, those components will allow library developers to target all programming langages, not just a single one like today, with higher security and better performance.

But those components are not enough, the WebAssembly ecosystem also needs a standard library to access the underlying platform and to share some common low-level building blocks. That's the purpose of WASI. Until recently, the only version available was WASI Preview 1, a monolithic subset of POSIX based on CloudABI. But recently, WASI Preview 2 based on Wasm Components, has been released and provides a much more sane and future-proof foundation. Most Wasm runtimes and programming languages supporting Wasm are working hard to implement Wasm Components and WASI Preview 2 specifications to make those usable by application developers. It is also great to see platforms like [wasmCloud 1.0](https://wasmcloud.com/blog/wasmcloud-1-brings-components-to-enterprise) or [Fermyon Spin](https://www.fermyon.com/spin) embracing those technologies.

In parallel, Luke and team move forward by working WASI Preview 3 which will bring asynchronous and streaming support with a clean designs that avoids the usual traps like [colored functions](https://journal.stuffwithstuff.com/2015/02/01/what-color-is-your-function/) and providing first class streaming and backpressure support. What is remarkable is the combination of both a clean design and a big focus on enabling high-performance (to be verified with the implementations of course) via a deep knowledge of the low-level parts of the stack. If you want to know more, watch Luke Wagner talk [A Stream<Consciousness> on the Future<async> in the component model](https://www.youtube.com/watch?v=y3x4-nQeXxc&feature=youtu.be). 

The last key building block is the WebAssembly Registry (Warg) protocol, to be able to publish those components to registries, regardless of if they are libraries (think npm, Crates.io, Maven Central, etc.) or deployment artifacts (OCI, DockerHub, etc.). To make that more contrete, you can for example browse the WASI namespace on https://wa.dev/wasi. I also highly recommend watching this brillant related talk "Filling a registry-shaped hole in the Wasm Component Ecosystem" by [Danny Macovei](https://2024.wasmio.tech/speakers/danny-macovei/) and [Oscar Spencer](https://2024.wasmio.tech/speakers/oscar-spencer/):

{{< youtube 2_-10mRN30s >}}

This talk illustrates pretty well that Wasm is much more that just a faster, more versatile and more secured compilation target. With Wasm Components and WASI emerging standards, It is going to:
 - Break the silos between technologies
 - Provide a clean separation at component level between public API and implementation
 - Allow to perform dependency injection at platform level
 - Allow full composability and virtualization of components which is just impossible with containers

## Blurry the lines between Cloud and Edge

But in order to succeed, WebAssembly first needs to meet developers and operators where they are. [SpinKube](https://www.spinkube.dev/), announced at Kubecon just after Wasm I/O, is a good example of that. The goal of this project is to provide an hyper-efficient serverless platform on Kubernetes powered by WebAssembly. It is great to see hyperscalers like Microsoft Azure and Wasm companies like [Fermyon](https://www.fermyon.com/) collaborating on such project.

In my opinion, WebAssembly is going to both allow Cloud vendors (like Microsoft or Google) to support more "edgy" workloads and Edge actors (like Fastly or NGINX) to run more "cloudy" workloads. I am curious to see which actors will be agile enough to have an holistic view of how to handle workloads that will be deployed either in data centers, IoT/mobile devices or browsers. And also curious to see what can of revenue model can arrise for those workloads that will not necessarily be deployed to datacenters.

Another area where WebAssembly is likely going to shine more and more is it's capability to target ARM and RISC-V architectures with the same tiny efficient artifact than for x86.

## Edge AI

As explained in this [Llama 2 in the browser with WebAssembly](https://inference.wasmlabs.dev/) article, you can run inference in a regular computer, and Wasm can allow to target any device, especially when leveraging advanced CPU capabilities with the [relaxed SIMD proposal](https://github.com/WebAssembly/relaxed-simd/blob/main/proposals/relaxed-simd/Overview.md), or even leveraging GPU capabilities like [LlamaEdge](https://llamaedge.com/) does.

But the most advanced use case demonstrated at Wasm I/O was probably this [Computer Vision at the Edge with WebAssembly](https://speakerdeck.com/angelmmiguel/computer-vision-at-the-edge-with-webassembly) talk where WebAssembly is used to deploy [Pipeless AI](https://www.pipeless.ai/) to any device.

Google also mentioned that TensorFlow Wasm backend allows much better performances that WebGL, especially when leveraging SIMD and threads support. If you want to have a comprehesive overview of all Google use cases, see [the slides of their presentation](https://docs.google.com/presentation/d/1bnYntCeekIev8hZnizixvLPUV7ljfJ2hNO6vBOcnXlE/edit?usp=sharing).

## Languages

In addition to Rust first-class support for Wasm, it was great to see a lot of progresses on supporting other languages. Both [Kotlin](https://docs.google.com/presentation/d/1oOSllgZ94tyMMmYbaxM2Ri7Kg1iekQCFngnDCqo-5H4/edit?usp=sharing) and [Dart](https://docs.google.com/presentation/d/1AOaMuhbrBG8uynJSMRZU4xdl5e1t_fTqBeupPVAmYs4/edit#slide=id.p) are progressing very fast towards making WebAssembly a first class target leveraging WasmGC now available in all major browsers except Safari. If you add to that some signals on Swift side like [official Wasm example](https://github.com/apple/swift-for-wasm-examples) or [CI](https://ci.swift.org/job/oss-swift-pr-test-crosscompile-wasm-ubuntu-20_04/), it looks like the 3 major languages used by mobile developers are going to have Wasm support. IMO we could see in short-term mobile developers deploying their backend using WebAssembly.

It is also great to see [componentize-py](https://github.com/bytecodealliance/componentize-py) and [ComponentizeJS](https://github.com/bytecodealliance/ComponentizeJS) to ease interroperabilty respectively with Python or Javascript.

Also notice that the debugging DevXP makes huge progresses thanks to related work by Microsoft, JetBrains and Google.

## Conclusion

I think Wasm Components and WASI Preview 2+ are going to unlock a lot more use cases for developers. A lot more needs to happen to unlock adoption, like more Wasm runtimes and languages implementing those emerging standards and WasmGC support, but I am excited about the progresses I see. The Wasm community is composed of amazing individuals, and I feel lucky to have been able to discuss with a lot of those poeple at Barcelona. Sergi Almar, the organizer of the conference, has once again done an amazing job organizing this conference, and I look forward seeing you next year 27/28 March at Wasm I/O 2025.

