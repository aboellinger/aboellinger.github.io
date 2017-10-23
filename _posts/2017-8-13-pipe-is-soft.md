---
layout: post
title: Pipeline development is software development
---

Your company makes *movies*, not *software*, so why should you care about
**development** best practices ?

Development efforts in animation studios
========================================

In my (acknowledgedly limited) experience, developers and technical directors
in small and medium animated studio don't use automation servers.
As a developper in an animation studio, I never asked to use one, and I did not
even know they existed until I started worrying about stabilizing
the production environment and delved into test-driven development.

There is a huge irony there, as most of the development effort in an animation
studio goes towards building an automated and collaborative environment
for artists (implementing sanity checks, publish scripts,
production tracking tools, etc).

Yet the technical teams tend to forget to build an automated and collaborative
environment for themselves. Especially when they are writing python code in a
small structure, where they can write code directly in production and ask
people to check if things work correctly just after they made a change.


The bloated pipeline...
-----------------------

But as the company grows, the codebase expands, some developers leave, other
join, and every project brings its own specific problems.
Quickly, the pipeline becomes an awful patchwork of tools and scripts
that no one fully understands, and are too difficult to maintain or evolve.
Whenever you make a change, you deal with unexpected side effects in
production, and people usually don't appreciate data loss and bugs
where they are under pressure to meet a deadline.

Once you get there, you might want to throw everything and start over again,
and you end up rewriting a new pipeline for every project you make.
This is a huge waste of time and energy (and money), as you don't capitalize on
previous efforts (especially if the original developpers left,
and the new ones start all over again, doing the same mistakes).


Redefining goals
----------------

To avoid this pitfall, you should redefine your technical team's mission.
See it as a separate entity from the production team,
whose product is not your actual project (an animated feature or TV-show),
but a reliable, adaptable and modular technical environment.
If you are a developer, the pipeline **is** your product.

The technical team is a mini-company delivering to the rest of your studio:
* The **pipeline**: a software suite intended to glue together third-party
  ([Maya][maya], [Nuke][nuke], [Guerilla Render][guerilla],
  [Shotgun][shotgun]...) and in-house solutions.
* **Automations** and **specific tools** that meet the needs of the production,
  developed with an agile approach, because production needs can evolve
  very quickly.
* A **stable technical environment** for the artists to work in.

This is getting closer to what a software editor does,
so why not take a look into software editing tools and best-practices ?


Tools and best practices you should consider
============================================


Version Control
---------------

If you are getting serious about your codebase, the first step you should take
is to do **version control**.
Tracking every change made to your codebase will allow you:
* to **act faster** when something is broken
  (you can instantly roll back to a working version
  *before* you start debugging)
* to **find who** wrote the failing code and ~slap him~
  give him positive feedback on how to improve it.
* to backup unused code without polluting your codebase
  (just delete it, you can always restore it from the version control manager)
* collaborative work on the same codebase.
* more...

I won't talk about different options. Just get [git][git].


Linting
-------

Linters are softwares that analyse code, and detect poor coding practices.
Using a linter helps you to:

* **Detect errors** (algorithmic errors, compatibility issues)
* **Detect bad habits** that could result in errors or maintainability issues
  at some point
* **Remove useless code** (unused variables and imports, code duplication)
* Write **homogeneous** code
* Write **readable** code

For python, I suggest you use [PyLint][pylint].
You can run it locally, or on an automation server.
A good start is to use a linting tool in your IDE
(I use [Anaconda][anaconda] in [Sublime Text 3][sublime]).

Note that linting tools also offer nice metrics to monitor your code quality.


Code reviews
------------

Code review is the process of having developers read and comment each others
code.
It has several benefits:

* It **keeps coders aware** of what their teammates are working on
* It **triggers discussions** about best practices and existing code, improving
  code quality, reusability, an homogeneity
* It allows junior developers to **learn faster** through mentoring

At [Studio Hari][hari] we installed Review Board.
GitLab and GitHub include reviewing tools.


Autodocumented code
-------------------

Have you ever tried to read someone else's code ?
Have you ever tried to read your *own* code one year later ?
Then you probably know how hard it is to decipher things when you don't
even know what ther are supposed to do !

A solution is writing comments[^1], that will help future readers to understand
what is going on. But you can do even better and generate a documentation
site from your comments.

You will improve code reusability, as other developpers will easily figure out
what functions he can call from your code and how to use them.

And since the site is generated from the comments, you don't need to write
things twice: just make sure your comments are consistent with your code!

You can build a documentation site from Python docstrings with [Sphinx][sphinx].


Tests
-----

I won't get into the details of [Test Driven Development][TDD].
Tests come in all kind of flavour (
[*unit*],
[*integration*](https://jamescrisp.org/category/testing/),
[*functional*](https://en.wikipedia.org/wiki/Functional_testing), ...),
but the idea is always the same:

1. You know how your code should behave on a given scenario
   (runing a function `a` with input `b` should produce output `c`)
2. You can write a small program (a *test*) that calls your code with defined
   inputs and checks that we get the expected output.
3. If the test fails (unexpected results), then either the code either the test
   is buggy: and you should fix that.

The good part is that you learned your code was buggy
**before** it got into production!

It might sound like doing twice the work, as you will have to write and maintain
tests as well. However, if your tests are strong enough to be trusted they will
boost your future productivity, as their failure will save you precious
debugging time, and their success be enough to proove that your code is ready
for production.

Python tests can be written with [nose][nose].
You can run your tests locally as you develop, but also remotely on an
automation server such as [Jenkins][jenkins].



Continuous integration & Continuous deployment
----------------------------------------------

[Continuous Integration][CI] is the process of getting all the code written by
developers together on a regular (several times a day) basis.
It generally involves centralizing everyones code on an automation server,
building the software and running tests on the result.
If anything fails, let the developers know and fix thing quickly.

If you stick to plain python code, you can probably skip the build part and
focus on testing.

[Continous Delivery][CD] is continuous integration pushed one step further.
Not only you merge the code, build and test it continuously, but if the
integration goes right, you ship it in production on the same regular basis.

At [Studio Hari][hari], we only set up a [CI][CI] pipeline,
the deployment still requiring to manually run a deployment script.
It already helped us stabilizing the production environment,
even when doing deep changes to our codebase.



[^1]: Actually, you don't *just* write comments. They also have to be clear
and consistent: a misleading comment is way worse than no comment at all !

[anaconda]: https://anaconda.org/
[CD]: https://en.wikipedia.org/wiki/Continuous_delivery
[CI]: https://en.wikipedia.org/wiki/Continuous_integration
[git]: https://git-scm.com/
[guerilla]: http://guerillarender.com/
[hari]: http://www.studiohari.com/
[jenkins]: https://jenkins.io/
[maya]: https://www.autodesk.eu/products/maya/overview
[nose]: http://nose.readthedocs.io/en/latest/
[nuke]: https://www.foundry.com/products/nuke
[pylint]: https://www.pylint.org/
[shotgun]: https://www.shotgunsoftware.com/
[sphinx]: http://www.sphinx-doc.org/en/stable/
[sublime]: https://www.sublimetext.com/
[TDD]: https://en.wikipedia.org/wiki/Test-driven_development