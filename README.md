# [The rules for good code examples](https://github.com/dacr/the-rules-for-good-code-examples)

Code examples are very important, each example is most of the time
designed to focus on a particular feature/characteristic of a
programming language, a library or a framework.
They help us to quickly test, experiment and remember how bigger
project or at least some parts of them are working.

Learning or remembering is much quicker when you have at your disposal
your own set of examples. This set of examples is a **knowledge asset**,
we must become aware of its value for us but also from a project team
point of view.

Being aware of this knowledge asset means that you should establish
and follow some simple rules which will help you or your project team to 
valorize this asset.

## Simple rules for good code examples

- [**One purpose**](#one-purpose-rule)
- [**Limited size**](#limited-size-rule)
- [**Runnable**](#runnable-rule)
- [**Exploratory compatible**](#exploratory-compatible-rule)
- [**Self-describing**](#self-describing-rule)
- [**manageable and shareable**](#manageable-and-shareable-rule)

When writing source code examples, try to take into account the maximum
number of those rules in order to implement the best possible examples. 

### One purpose rule

**A good code example has one purpose** such as learning the basics of a
programming language (the typical `println("Hello world")` example) or
experimenting/testing a library or framework.

It can also be used to illustrate how you can achieve one feature, for example
`date -u +"%Y-%m-%dT%H:%M:%S.%3NZ"` is a linux date command usage example
which prints a date/time in the ISO8601 standard date format.

### Limited size rule

**1 example in 1 single file** (when possible) helps to limit the overall
example size as well as it simplifies greatly the sharing.

Try to keep each source code example as short as possible, if over the 
time one example becomes larger and larger, try to split it into several
examples, it may also mean you need to drop the example format and switch
to a dedicated project organization.

A good approach is to always start small through a source code example.
When you want to enhance/refactor your initial example and probably add
some complexities, create a copy of your example and make your changes,
it is very important to keep your first iterations as they will retain
the simplest aspects of what was your successive intentions.   

### Runnable rule

**Your example self-contains all information required to run it**, so
it means that **it must provide** the following information :
- what are the **runtime prerequisites** such as python, jshell, ammonite, bash, julia,...
  - while giving precise release information if required such as python-3, java-15
- what are its required **software dependencies**
  - linux packages required to run this bash script example
  - python modules to install before trying to run the example
  - maven/classpath required artifacts

The minimum thing to do is to add comments in your example which list the
required runtime and dependencies information. You can also use a [shebang][shebang]
to make your example directly executable while giving to the example reader the
information about how it is executed.

It is also important that your example *prints* something, for a function, add
some usage example with visible results.

```python
#!/usr/bin/env python3
def add(x,y):
  return x+y
print('1+2='+str(add(1,2)))
```
 
A very good developer experience is achieved when you use the [ammonite][amm]
REPL as it is able to load software dependencies at runtime !
```scala
#!/usr/bin/env amm
import $ivy.`com.lihaoyi::requests:0.6.5`
requests.get("https://httpbin.org/get").lines().foreach(println)
```
In this small example, the runtime context is given by the shebang, and the dependencies requirements 
is directly given by the executed import instruction.

### Exploratory compatible rule

When a read-eval-print-loop (REPL) is available in your example context, organize your example
in a way that it is **easy to copy/paste the example in a REPL session** for interactive
exploratory live experiments.

For one-liner example this is trivial, with longer examples it may add some constraints
depending of your technological choices, so while writing your example, use a REPL session
interactivly to check how it behaves and also to help you enhance your code example.

For example for python don't forget to add an empty line after each function definition ;) And for
ammonite, it may require either to add some braces within you example or to copy/paste within braces
to avoid any issue.

### Self-describing rule

When it makes sense, design your example to make its execution self-describing, this is quite simple
when you **use a unit test framework** as most of them can generate a report. If you use *specification*
oriented unit tests, it becomes trivial as you can see in the following example output :

```
split
- should be able to split in 2 parts using the last dot thanks to zero-width positive lookahead regexp
- should be able to split on characters while preserving those characters
regexp
- should allow partial matching
- should be possible to use named arguments
```

Which is generated by the following example script, a runnable example with flat specification
output brought by the [scalatest][scalatest] unit test framework :
```scala
#!/usr/bin/env amm
import $ivy.`org.scalatest::scalatest:3.2.2`
import org.scalatest._, flatspec._,  matchers._,  OptionValues._
import java.util.Locale
object StringOperations extends AnyFlatSpec with should.Matchers {
  "split" should "be able to split in 2 parts using the last dot thanks to zero-width positive lookahead regexp" in {
    val aType="ab.cd.de"
    val Array(aPackage, aClassName) = aType.split("[.](?=[^.]*$)", 2)
    aPackage shouldBe "ab.cd"
    aClassName shouldBe "de"
  }

  it should "be able to split on characters while preserving those characters" in {
    val in = "abc. truc? blah, blu."
    in.split("""\s*(?<=[?.,])\s*""").toList shouldBe List("abc.", "truc?", "blah,", "blu.")
  }
  "regexp" should "allow partial matching" in {
    val MyRE="TO(.*)TA".r.unanchored
    "xxTOTUTAxx" match {
      case MyRE(inside)=> inside shouldBe "TU"
      case _ => fail
    }
  }
  it should "be possible to use named arguments" in {
    val MyRE="TO(?<in>.*)TA".r
    val sample = "TOTUTA"
    MyRE.findFirstMatchIn(sample).map(_.group("in")).value shouldBe "TU"
  }
}
StringOperations.execute()
```
This example comes from [a bigger one][strops] I maintain whose purpose is a sheet cheat about advanced operations on strings.

**So when executed, the output describes exactly what it does, and even more, as this is unit tests based it will
fail until you give the right implementation ! Or until you've understood what's going on ;)**

### Manageable and shareable rule

Once you've written dozens of examples, you'll quickly need to industrialize their life cycle. It will mean :
- how to deal with maintenance ?
- how to share them ? Publicly or internally ? 
- how to let people react over them ?
- how to manage their change history ?
- how to write them from your perspective or from the team one ?
- how to search through your examples ?

All of these require some good practices, and tooling. Among the good practices one of the most important one is
to insert within each of your example some meta-data in order to give more insights about it. You can add several meta-data
fields such as a short description, a keywords list, some licensing information, ... which will greatly help your
examples life-cycle management. 

In fact this is quite easy thanks to this simple recipe :
- GIT repository to store all your examples
  - store your examples or your team collective examples inside a GIT repository
  - this repository can be kept private or limited to given set of people
- [Gitlab snippets][snippets]/[Github gists][gists] solutions to share small code example, as they
  - give an easy way to publish and share any numbers of examples
  - provide user interactions for each example (comments, starring, ...)
  - provide a search engine
  - store the history of all examples changes
- And it becomes even easier by using the [code-examples-manager][cem] project
  - it automates everything, and becomes mandatory as soon as you have dozens of examples. 
  - Take a look to my publicly [shared examples knowledge bases][dacr-gists-overview]
    to have a good idea of what it does.  

![](images/cem-keywords.png)

[amm]: https://ammonite.io/
[cem]: https://github.com/dacr/code-examples-manager
[dacr-gists-overview]: https://gist.github.com/dacr/c071a7b7d3de633281cbe84a34be47f1
[dacr-gists]: https://gist.github.com/dacr
[shebang]: https://en.wikipedia.org/wiki/Shebang_(Unix)
[scalatest]: https://www.scalatest.org/
[snippets]: https://gitlab.com/explore/snippets
[gists]: https://gist.github.com/
[strops]: https://gist.github.com/dacr/3b592b9f9ed0b88a7236503f075b8f89
