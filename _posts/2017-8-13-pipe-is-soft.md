---
layout: post
title: Pipeline development is software development
---


Development efforts in animation studios
========================================

In my (acknowledgedly limited) experience, developers and technical directors
in small and medium animated studio don't use automation servers.
I never heard of them before I started worrying about stabilizing
the production environment and delved into test-driven development.

And the irony of it is that most of the development effort in an animation
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
but you might end up rewriting a new pipeline for every project you make.
This is a huge waste of time and energy, as you don't capitalize on previous
efforts (especially if the original developpers left, and the new ones start
all over again, doing the same mistakes).


Redefining goals
----------------

To avoid this pitfall, you should redefine your technical team's mission.
You should see it as a separate entity from the production team,
whose product is not your current animated feature or TV-show,
but a reliable, adaptable and modular pipeline itself.

The technical team is a mini-company delivering to the rest of your studio:
* The pipeline: a software suite intended to glue together third-party
  (Maya, Nuke, Guerilla Render, Shotgun...) and in-house solutions.
* Automations and specific tools that meet the needs of the production,
  developed with an agile approach, because production needs can evolve
  very quickly.
* A stable environment for the artists to work in.

This is getting closer to what a software editor does,
so why not take a look into software editing tools and best-practices ?


Tools and best practices you should consider
============================================


Version Control
---------------

If you are getting serious about your codebase, the first step you should take
is to do version control.
Tracking every change made to your codebase will allow you:
* to act faster when something is broken
  (you can restore a previous working version before you debug,
  so you don't have anyone waiting for your fix)
* to find who wrote the failing code and ~slap him hard~ explain teach him how
  to avoid the same mistake.
* to avoid keeping unused code for the record
  (you can always restore it from the version control manager)
* and many other things such as having several developers working in parallel
  on the same tool with branches...

I won't talk about different options. Just get git.


Linting
-------

Linters are softwares that analyse code, and detect poor coding practices.
Using a linter helps you to:
* Detect errors (algorithmic errors, compatibility issues)
* Detect bad habits that could result in errors at some point
* Remove useless code (unused variables and imports, code duplication)
* Write homogeneous code
* Write readable code

For python, I suggest you use pylint.
You can run it locally, or on an automation server.

Note that linting tools offer a nice metric to monitor code quality.


Code reviews
------------

Code review is the process of having developers read and comment each others
code.
It has several benefits:
* It keeps coders aware of what their teammates are working on
* It triggers discussions about best practices and existing code, improving
  code quality, reusability, an homogeneity
* It allows junior developers to learn faster through mentoring

At Studio Hari we installed Review Board.
GitLab and GitHub include reviewing tools.


Autodocumented code
-------------------

Have you ever tried to read someone else's code ?
Have you ever tried to read your code one year later ?
Then you probably know that it is difficult to decipher things when you don't
know what ther are supposed to do, or what they were intended for.

A solution is writing comments, that will help future readers to understand
what is going on. But you can do even better and generate a documentation
site from your comments.

You will improve code reusability, as another developper will easily figure out
what functions he can call from your code and how to use them.

And since the site is generated from the comments, you don't need to write
things twice: just make sure your comments are consistent with your code!

You can build a documentation site from Python docstings with Sphinx.


Tests
-----

I won't get into the details of test oriented developments.
Tests come in all kind of flavour (unit, functional, deployment...),
but the idea is always the same:
#. You know how your code should behave on a given scenario
   (runing a given function with a given input should return a given output)
#. You can write a small program (test) that calls your code with specified
   inputs and checks the outputs are those expected
#. If the test failed (you get unexpected results) after a change, then your
   code is buggy, and you should reworking it.

The good part is that you learned your code was buggy
before it got into production!

Python tests can be written with nosetests.
You can run your tests locally as you develop, but also remotely on an
automation server such as Jenkins.



Continuous integration & Continuous deployment
----------------------------------------------

Continuous Integration (CI) is the process of getting all the code written by
developers together on a regular (several times a day) basis.
It generally involves centralizing everyones code on an automation server,
building the software and running tests on the result.
If anything fails, let the developers know and fix thing quickly.

If you stick to plain python code, you can probably skip the build part and
focus on testing.

Continous Deployment (CD) is continuous integration pushed one step further.
Not only you merge the code, build and test it continuously, but if the
integration goes right, you ship it in production on the same regular basis.

At Studio Hari, we have only set up a CI pipeline,
the deployment being only partially automated
(done by a script but still manually triggered)
