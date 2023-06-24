---
layout: post
title: "What if we do exhaustive automation testing?"
date: 2017-11-01
categories: testing
css: /assets/css/style.css
subtitle: Will the benefit of covering every possible case using automation tests justify the cost of maintaining such test suites?
share-img: /assets/images/exhaustive-automation-testing/test-pyramid.png
---
![Tests Pyramid. Source: Martin Fowler’s blog](/assets/images/exhaustive-automation-testing/test-pyramid.png){: .center-image }
<p align="center" style="font-size:13px">Tests Pyramid. Source: Martin Fowler’s blog</p>

You must have heard about test pyramid in context of agile methodology.

Test pyramid is a representation of the different type of testing methodology you can use and how should you be using them. It’s recommended that you have a lot of unit tests, fewer integrations and even fewer UI tests. Generally, in the ratio of 70:20:10. This is recommended as unit test cost less to maintain and are fast to run. As you move up the pyramid the test get slower and cost more to maintain.

This article is advocating having lots of functional/UI tests. Don’t get me wrong, I’m not suggesting an anti-pattern like Hourglass or Inverted pyramid.  I’m suggesting using the same pattern but having more functional/UI tests, as many as you can have.

>But, aren’t you writing redundant tests?

Well, not actually, unit tests are good but they don’t really reflect what a user will see. They do provide you coverage but things can still mess up.

![No integration](/assets/images/exhaustive-automation-testing/no_integration_test.gif){: .center-image }
<p align="center" style="font-size:13px">Exhibit A</p>

>Doesn’t the picture say a lot?

One problem that I have with unit testing is that everything is mocked, so when you change some code in one class you can get away with not changing the test of its consumers. As consumers will have this class mocked so the new behaviour is not being handled in the consumer. This can land you in trouble at times. As this happens more and more you’ll end with code that works great alone but fails when put together.

Such scenarios can be prevented with integration test and functional/UI tests.

<p align="center">. . .</p>

Even though things can be handled with integration tests I’d still say that we should be writing functional/UI tests because integration tests are dependent on application code as they test a certain part of code coupled together, some mocking is still required. This is the same problem as unit testing but instead of between classes now between modules.

On the other hand, UI or functional test are isolated from application code and they will never be affected by any change in code unless the functionality changes.  
Even in case of complete application rewrite or a major code refactoring, these test won’t require change as they mock only external dependency. It gives you more confidence when your existing tests are passing.

Assume, you have a web application having exhaustive UI tests that have every user flow covered as part of automation.

Consider this web application to be a huge monolithic application which is deployed on a single server and turns out your company is booming. You have more and more users coming in every day. You are having the time of your life. But, your servers aren’t, they can’t handle all the load.

>What do you do?

You either throw more hardware at it or you change the code to handle such load. But there is only so much you can do without any radical change.

Microservices can help you scale better. But that would be a rewrite of application and also require a lot of time to build and test. You can save the time to test all this if you have exhaustive functional/UI tests in place to verify all the user flows.

Make changes in the application code all you want. Your functional test suite is there to cover you. Now you can develop that microservices architecture for your backend and be sure that existing functionalities will not break.

*Also you android developer, trying to modularise your code into feature modules.*

Try doing all this with your puny unit tests. No offence, unit tests have their place in the testing world but in this case, I’d give the trophy to functional tests.

Point being, the closer a test is to the application code the less reliable it is from a functional perspective. Your class might handle null better but it doesn’t know a users perspective. UI tests simply put are automating manual testing. And nothing can beat looking at the actual thing.

*Do you see why exhaustive functional/UI tests are not redundant?*

 <p align="center">. . .</p>

>What you are saying requires a lot of time and effort.

On a small scale, shoestring budget project this makes little sense. The cost of creating all these tests and maintaining them is far greater than the benefits that it comes with.

But a company whose bread and butter is the software that they make or their business is somehow dependent on their website/app/software to bring in revenue. In such cases, a customer-facing application/website must be top notch or you lose customers.

*My personal opinion, user experience is very important. It’s user experience that makes you stand out in a crowd of companies that are trying to be different but are doing almost the same thing.*

These companies or project need to stay competitive and for that, they do radical changes frequently. They follow agile practices like CI/CD ([Continuous Integration](https://en.wikipedia.org/wiki/Continuous_integration){:target="_blank"} & [Continuous Delivery](https://en.wikipedia.org/wiki/Continuous_delivery){:target="_blank"}).

If you are aiming for CD (Continuous Delivery) but are struggling to do so then this approach just might help you. I’ve generally seen people doing CI but not CD. The problem I’ve seen is the confidence in code and tests.

A feature requirement comes, you analyse it, your team develops it and then it goes for testing. All your unit and integration tests are passing. But still, a bug comes up. Won’t be the first, I’m sure almost everyone would have seen this in the past. Incidents like this destroy the confidence in a test suite. Which is why people shy away from CD. But if you have exhaustive functional tests you can lower such incident to almost zero.

A typical project has a low amount of functional coverage. With every release, they have some amount of manual testing where they test new and old feature are not broken.

I’m proposing that we remove all the manual effort and only do manual what can’t be automated. You write functional/UI automation test for everything possible. This looks a lot of effort. But for a project that lives long, this is a bargain. You’d never have to manual check any functionality again and if that test is passing you’d be sure that nothing will break.

Some companies follow true CD, where on commits all the tests are executed and it automatically goes to production unless stopped manually. These companies have great confidence on their automation. This helps them stay on top of their game. CD also help you with lower  time to market. And having an exhaustive test suite helps achieve CD.

*So in all, if you have a business critical application, you’d be actually lowering your cost of testing and gaining opportunity cost thanks to lower time to market. And in case of any architecture change or even an application rewrite you will have a test suite that you can rely on.*

<p align="center">. . .</p>

#### A few more things
**Behaviour-driven development**  
If you choose to practise what I explained. You can try practices like BDD ([Behaviour driven development](https://en.wikipedia.org/wiki/Behavior-driven_development){:target="_blank"}) to further optimise the throughput of your team. In this, you’d be creating all the possible scenarios as functional tests before development starts and the development objective is to pass these tests. So, you end up with an even shorter delivery cycle.

**Consider this exhaustive test suite as your regression test suite**  
Think of this test suite as regression because they won’t be very useful with minor changes. But will definitely save you when the stakes are higher. These tests take a lot of time to run and having lots of them makes feedback even longer. I’d suggest you don’t run them for every commit. But only when your unit and integration pass and try to parallelise them as much as possible.

**Pick your tool carefully**  
UI tests have a reputation for being flaky and I agree to that. Pick a tool that is not flaky or is the least flaky.

**No more documentation**  
Your shiny new  test suite is the only documentation you’d ever need. Just refer to this to know any functional detail about the software.

**Keep your test suite separate from application code**  
Keeping test and app code separate will help you in case of any major change/refactoring. Changing the code of your application should not affect your tests unless any functional change comes in.

<p align="center">. . .</p>

![Now get back to work and start writing tests](/assets/images/exhaustive-automation-testing/getbacktotests.jpg){: .center-image }

Do you agree with my views? Comment below.

Share this article if you like it.