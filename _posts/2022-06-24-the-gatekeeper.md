---
layout: post
title:  "The Gatekeeper"
excerpt: "A rant on why C++ should not be taught as a first language"
date: 2022-06-24 02:15:20 -0500
author: "Jake Gallow"
---

&nbsp;&nbsp;&nbsp; When I was college the very first thing my intro to computer science teacher did was turn off the light, walk to the front of the class, then asked us to tell him how to go turn the lights back on.

&nbsp;&nbsp;&nbsp; It sounded simple, someone just raised their hand and said "just walk over to the switch and turn it on".

&nbsp;&nbsp;&nbsp; "Well how do I walk? What's a switch? How do I turn it on?" he responded.

&nbsp;&nbsp;&nbsp; Everyone in the class was befuddled, and what followed was the most asinine 10 minutes of my freshman year.
 We had explain to the TA how to lift his foot and move him inch by inch closer to the lights, tell him how to lift his hand and flip the switch in order to start class.
 This was however the most accurate explanation of coding I have ever received.
 Computers are stupid, you have to teach them how to do everything.
 Thankfully though, a lot of these details have been abstracted over the years, and people typically aren't creating punch cards or writing assembly.
 They're coding in some high level programming language to solve problems.

&nbsp;&nbsp;&nbsp; Unfortunately though, universities have confused the idea of teaching problem solving with teaching a programming language and to make it worse they've chosen the worst possible language to do that with: C++.
 I think C++ is a amazing language, but it's typically introduced to students as a first language, which gets in the way of learning important problem solving skills.
 **Instead of teaching C++ as a first language to weed out inexperienced problems solvers universities should choose a more expressive language like Python or Java.**

# Problem solving
&nbsp;&nbsp;&nbsp; At one point in our recent history, I do think C and C++ were the best language to learn for engineers, and that was before software engineering was a typical career path.
 In the 80's, 90's, and even early 2000's programming was in it's toddler ages, and most people entering the software engineer field were already either degreed in electrical engineering or computer engineering and had already spent several years on engineering problems.
 They already knew how to solve problems, then when C came along it was a simple tool to help in that endeavor.
 They knew about computing components, what they did and why, so adding a little bit of abstraction on top of that was no big deal.
 Nowadays in the computer science degree path the first problem people see is how to write code; no particular reason, just write some code, and the problem will come later.
 This is the logical equivalent of giving someone a hammer without showing them the nail first.
 Then when we do show students the problem finally they don't know what to do.

&nbsp;&nbsp;&nbsp; Let's be honest, the vast majority of 18 year old college freshman don't know shit about solving problems.
 High school math and science is all about seeing a problem on a piece of paper then regurgitating an equation to get a check mark that says you got it right.
 That's not to say that this isn't problem solving, it is, it just doesn't require a single original thought.
 Computer Science problems, at least in introductory classes are presented as stories, akin to word problems like you might see on some high school math problems, but a without a clear formula to plug things into.
 These open ended questions do require some more creative thinking, which is a very important skill to develop, and is a prerequisite to debugging both code issues and platform issues rampant in C++ development.

# Opaque Syntax
&nbsp;&nbsp;&nbsp; When it comes to C++ syntax there's a weirdly high base level of understanding in writing even basic statements.
 Lets take just a right aligned `cout` statement with a fixed width for example (side note: In my opinion aligning terminal output is and incredibly asinine task that every university intro class uses just to piss of kids who have never even seen a terminal before).
```c++
#include <iostream>
#include <iomanip>

using namespace std;
int main()
{
    cout << setw(7) << right << setprecision(3) << 3.14159 << endl;

    return 0;
}
```
&nbsp;&nbsp;&nbsp; To an experienced programmer (even who who does not understand C++), it should be fairly obvious what is going on here.
 But let's play a game and break this down as if we were speaking to a beginner.

> Teacher: This prints out 3.14 using a width of seven characters to the console
>
> Student: Why isn't the 3.14159 before the `setw`, right, and `setprecision`?
>
> Teacher: Because the `stdout` output stream needs these arguments pushed to them in in a specific order.
>
> Student: What is `stdout` and why do I need to push these in that order?

&nbsp;&nbsp;&nbsp; Now we may applaud this student as an inquisitive learner (in this case we probably should), but all they want to know is how do put something on the screen so they know the program is working.
 If we really wanna explain everything here we need to explain what the `io` libraries we're including are, what is a library, what a `namespace` is, what it means to use the std `namespace`, what output streams are, and why arguments a pushed to it in a specific order.
 These aren't strictly requisite explanations; we could just say "you need these things to output this way, just memorize it," which is also a pretty horrible answer because it implies that everything here is boilerplate.
 Furthermore, these explanations do nothing to help people learn how to solve problems, its just more information that at its onset just feels like historical trivia to people who've only ever used MacOS or Windows.
 This extremely narrow example is just the tip of the iceberg when it comes to C++ syntax, so hopefully its not too much of a problem when they get to pointers and dynamically allocated memory.

# Now run your code

&nbsp;&nbsp;&nbsp; The student has just written their first homework assignment's code, and like any beginner they wrote the entire thing and saved compiling and running for last.
 Congratulations to them, because they've just entered the fucking casino that is C++ compilers.

&nbsp;&nbsp;&nbsp; Chances are they are still on MacOS or Windows, and they are just taking whatever the first compiler they can install is, but that provides them 0 guarantees that their assignment will run for whomever is grading their homework.
 If they want to guarantee that they need to make sure they are using the same compiler and version, which may not even be available on their platform, make sure they are on the same language, and be sure their target is runnable for whatever OS the grader is running.
 Now for most intro to CS courses, most people probably aren't going to be writing code that is platform specific, but its an unnecessary doubt for them, and a doubt that the software engineering industry has spent years creating various solutions like VMs and Docker to achieve some level of uniformity.

&nbsp;&nbsp;&nbsp; Recently I was working with a person on Mac running an old version of clang, and had to get into this shit about why I could run some code using GCC but they could not run the same code on their machine.
 It's not a super common issue, but it definitely crops up from time to time.

&nbsp;&nbsp;&nbsp; There are a myriad of more issues with learning C++, such as trying to determine which decade of documentation provides a good answer to your problem, **odd behaviour from a lack of memory safety**, trying to install external libraries (like boost), and I think each of those may be worth a blog post of their own.
 This is just scratching the surface of why C++ hurts beginners.

# Why use the alternatives

&nbsp;&nbsp;&nbsp; A short list of languages I think should be taught to beginners instead include Python, Go, or Javascript (through nodejs).
 There's a few reasons why:
1. Garbage collection allows new programmers to think about algorithmic problem solving issues

&nbsp;&nbsp;&nbsp; Manual memory management is hard, even for seasoned professionals.
 There's a reason why 70% of security issues in C++ stem from a lack of memory safety and multiple languages are built to allow direct memory management safely (like Rust and Ada).
 Garbage collection is another way to make it easier to handle memory issues in a way that abstracts it away from the user.
 That's not to say that garbage collection inherently makes a language completely safe, but it does make it easier to reason about in many cases.
 This frees up the developer to think more about the problem at hand rather than the minutia of every memory issue they would run into in C++.

2. The documentation around these languages (particularly Go) I find much more accessible than C++

&nbsp;&nbsp;&nbsp; Javascript and Python are both pretty long in the tooth, but their documentation I still find way easier to follow than C++.
 It's mostly centrally located (for python) and there are many up to date classes and videos online.
 Go has the advantage of still being relatively young, so finding up to date documentation is almost the only documentation you can find.

3. The learning curb for accomplishing basic tasks is easier to work past.
```
print('This is a string')
```
&nbsp;&nbsp;&nbsp; That's all it takes to print a string in Python.
 There's no importing libraries or namespaces for the most simple things, and most of the data structures beginners need are already imported (like dicts and such).

4. Creating useful applications is much easier.

 &nbsp;&nbsp;&nbsp; When beginners are writing their first programs they usually want something visual, or useful.
  Thankfully each of these languages comes with their own package managers that make it easy to build websites, make games, or write [T|G]UIs.
  It makes the language more immediately useful for solving the problems that they actually have, instead of making up toy projects like you are almost forced to when learning C++.
  Installing packages in C++ is much, much more difficult without the built in package manager.

# When should you introduce C++
&nbsp;&nbsp;&nbsp; I think C++ still needs to be taught at universities, but not as a first class.
 It's incredibly important for understanding data structures, so I'd think it should be taught as the start of that class.
 Implementing a basic linked list is not that difficult in C++ once you are able to reason about problem solving and debugging, so once you have those basic skill from the other languages it's fine to get started on this behemoth.

# Conclusion
&nbsp;&nbsp;&nbsp; Stop teaching C++ as first language.
 There are much better options that make computer science more accessible and enjoyable.


_End Rant_
