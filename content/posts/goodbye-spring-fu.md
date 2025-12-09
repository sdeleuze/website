+++
author = "Sébastien Deleuze"
title = "Goodbye Spring Fu"
date = "2025-12-08"
tags = ["spring","kotlin"]
+++

Goodbye Spring Fu, the experimental project I created several years ago to explore more efficient Spring applications configured with DSLs [has been archived](https://github.com/spring-attic/spring-fu). We learnt a lot from it and it has contributed directly or indirectly to the introduction of multiple Spring features.

The first reason why we archive it now is the release of Spring Boot 4 and the introduction of [modular auto-configurations](https://spring.io/blog/2025/10/28/modularizing-spring-boot), which are more efficient, allow enabling features more explicitly with less unwanted side effects.

The second reason is that Spring Framework 7 introduces BeanRegistrar (Java) and BeanRegistrarDsl (Kotlin) for [programmatic bean registration](https://docs.spring.io/spring-framework/reference/core/beans/java/programmatic-bean-registration.html), with full compatibility with Spring AOT optimizations. 


The hidden superpower of BeanRegistrar and BeanRegistrarDsl is that they allow creating custom configuration DSLs for Spring Boot applications. [KHTMX for Spring](https://wakingrufus.github.io/khtmx/spring.html) is a great example of that. Don't be shy, write your own!

Spring Fu has also been a great source of inspiration for bringing the Kotlin support in Spring to the next level, to show it was possible to compile Spring applications to GraalVM native images as early as June 2018, and to design the first incarnation of Spring AOT!

We may explore in the future how to make programmatic bean registration even more flexible, you can follow [spring-framework#21497](https://github.com/spring-projects/spring-framework/issues/21497) related issue. I hope the Spring portfolio and ecosystem will use it more and more.

The unexpected huge wave of positive feedback from the community when I announced it remains one of the most memorable moments of my career.

![Spring Fu announcement tweet](/images/spring-fu.png)

Thanks for that!