---
layout: post
title: "Concurrencia != Paralelismo"
date: 2014-11-05 17:55
comments: true
categories: opinión concurrencia paralelismo
---

Hace días los amigos de la [escuelaweb](http://escuelaweb.net) publicaron un buen articulo relacionado con los lenguages  *scripting* mas populares (o menos peores) del mercado en dicho [post](http://blog.escuelaweb.net/php-python-o-ruby-para-que-sirve-cada-uno/) se hacia una buena descripción de cada uno mostrando sus casos de uso, pros y contras, si aun no lo leen le recomiendo que lo hagan.

Sin embargo encontré referencia a un termino usado y que puede generar algo de confusión, entiendo también que el post fue creado para un publico menos tecnico y quizas entrar en detalles no era necesario. Acá trataré de aclarar el punto en termas bien práctico.

Concurrencia != Paralelismo, son dos formas como se ejecuta el código en principio todos los lenguajes mencionado en dicho post soportan **Concurrencia**.

Empecemos con una pregunta simple:

> Multiples hilos (Threads) van a ejecutar mi código en paralelo? 

Esta pregunta esta mal formulada e interpretada por quien la formula. De hecho se pudiera dividir en dos posible preguntas mas adecuadas.

1) Multiples hilos (threads) pueden ejecutar mi código concurrentemente? Definitivamente Si.

2) Multiples hilos (threads) pueden ejecutar mi código en paralelo? Quizás.

Ahora veamos un poco mas a fondo lo anterior. Dado que somos desarrolladores voy a usar un ejemplo de referencia donde nosotros somos los CPU :).

## Un Ejemplo
Imagina tu eres un desarrollador trabajando para una pequeña compañíay dicha compañia tiene dos proyectos, lo vamos a llamar A y B. Ambos necesitan ser completados y requieren un día completo de un desarrollador. Hay al menos 3 formas de lograr este objetivo.

1) Tu terminas el proyecto A hoy y el proyecto B mañana.

2) Tu trabajas en la mañana en el proyecto A, luego te cambias al proyecto B en la tarde. Repites esto mismo mañana y ambos proyectos estarán listo en dos días.

3) El último y mas actractivo es que la empresa contrate a otro desarrollador. Tu trabajas en el proyecto A y Él trabaja en el proyecto B, al final del primer día ambos proyectos estarán listos.

Veamos como actuan los escenarios anteriores en un esquema de concurrencia y paralelismo.

**La primera opción** representa la forma común como se ejecuta un código en _single-thread_, dada dos tareas va a ejecutar una y cuando termine ejecuta la otra.

**La segunda opción** es como se ejecuta un código concurrentemente, multiples hilos (multi-thread) ejecutando código en un solo (y único) CPU. Dada las dos tareas pueden ejecutarlas al mismo tiempo. Tomando este ejemplo tenemos un desarrollador ó CPU y las tareas compiten por tener acceso a dicho recurso valioso :) en el caso de no logran acceso deben esperar.

En otras palabras este escenario representa concurrencia y **multi-threaded** código no significa que el código va a ejecutarse mucho mas rápido que un **single-thread**. En todo caso, las tareas siempre se van a completar en el mismo tiempo (Dos días).

Ahora **el tercer escenario** representa Paralelismo, es decir multi-threaded código ejecutandose en multiples core CPU. Dada las dos tareas ambas se ejecutan de forma simultánea completandose en la mitad del tiempo (1 día).

En resumen el caso #1 y #2 ejecutan código concurremente pero el #3 en Paralelo.

## No puedes garantizar que todo será Paralelo.

Para terminar, vamos ilustrar un caso que tu código puede ser ejecutado Concurrentemente sin ser Paralelo necesariamente. Lo único que tu puedes hacer es organizar tu código para ser Concurrente, usando multiples *threads*. Pero hacer que se ejecute de forma paralela esta fuera de tu alcance, porque esa responsabilidad es dejada al *thread scheduler*.

Por ejemplo si tú código esta siendo ejecutado ó corriendo en 4-core CPU, y tu haces **spawn** de 4 threads, es posible aunque poco probable, que tú código sea ejecutado en solo un CPU. Esto porque el *thread scheduler* decidió agendarlo a un CPU.
