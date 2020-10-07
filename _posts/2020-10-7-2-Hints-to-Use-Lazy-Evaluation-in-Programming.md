---
layout: post
title: 2 Hints to Use Lazy Evaluation in Programming
---

#### - What:
1. Used particularly in functional programming languages.
2. Chances to increase performance by not doing the computation until needed.
3. Has book-keeping overhead.
4. May cause deadlock when two lazy value cross referenced.

#### - When:
1. You want something to be initialized only once (exmaple below)
2. Partial accessing a large set of data
    * ex: iterator.next() instead of getAll().get(n)

#### - Examples:
* ##### You want something to be initialized once: - [[test it online]](https://scalafiddle.io/sf/hah3GWH/0)
```scala
class Doncic {
    def doInit() : Int = {
        println("Hello Basketball")
        77
    }
    lazy val x = doInit()
    def dribbling() {
        println(x)
    }
}
val luka = new Doncic
luka.dribbling()
luka.dribbling()
luka.dribbling()
```

Outputs:
```bash
Hello Basketball
77
77
77
```
You can see the "Hello Basketball" will be called only once, 
so when it's something heavy computation, 
you know it's going to save sometime when it's applicable.

* ##### Delay of evaluation - [[test it online]](https://scalafiddle.io/sf/vTKxL29/0)
```scala
val x = { println ("doncic") ; 77 }
println ("luka")
println (x)
```
prints: doncic, then luka then 77.
``` scala
lazy val y = { println ("doncic") ; 77 }
println ("luka")
println (y)
```
prints: luka, then doncic then 77.

* ##### Deadlock : [To-Do] Have an example for the deadlock

---

##### - References:
[http://matt.might.net/articles/implementing-laziness/](http://matt.might.net/articles/implementing-laziness/)
[https://en.wikipedia.org/wiki/Lazy_evaluation](https://en.wikipedia.org/wiki/Lazy_evaluation)
[https://stackoverflow.com/questions/19955995/when-to-use-lazy-values-in-scala](https://stackoverflow.com/questions/19955995/when-to-use-lazy-values-in-scala)
[https://stackoverflow.com/questions/19469464/do-something-only-once-without-state](https://stackoverflow.com/questions/19469464/do-something-only-once-without-state)
[http://twitter.github.io/effectivescala/#Functional%20programming-Laziness](http://twitter.github.io/effectivescala/#Functional%20programming-Laziness)