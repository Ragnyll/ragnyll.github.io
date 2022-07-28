---
layout: post
title:  "Help Me (To Help You) Troubleshoot"
excerpt: "Thoughts on how to make support channels a little less toxic."
date: 2020-07-25 02:15:20 -0500
author: "Jake Gallow"
---

&nbsp;&nbsp;&nbsp;&nbsp;Going to a engineering support channel for troubleshooting help is hardly anyone's idea of a good time.
 As a reporter, you have a problem that you need answers to, and when you decide to enter the channel you'll likely get a response equivalent to "did you check stack overflow?"
 Or you could end up waiting for potentially hours for the support associate to get around to you only after you've `bumped` your query enough finally get their attention.
 From the support associate's perspective you have a never ending queue of people all demanding your attention with the notion that _their_ issue is the most important.
 Beyond that, most questions or issues you're getting have already been answered in some capacity, and the hordes of attention zombies have done nothing to help themselves.
 Finally, when you do get around to helping someone with their issue you have to deal with  bizarre turn around times on responses that plague asynchronous communication.

&nbsp;&nbsp;&nbsp;&nbsp;A good rapport in support channel is very difficult to maintain even under good conditions.
 Thankfully, there are things that both parties can do to prevent an adversarial relationship from emerging.

From the reporter's side they can:
1. Make sure their question is clear and concise with a specific consensus to reach.
2. Be sure some attempt to solve the issue has been made and troubleshooting done during that attempt is communicated.
3. Follow up with any resolutions to the problem that are reached.
4. Be open to understanding the flaws in their troubleshooting process.

From the support associate's side they can:
1. Make sure the reporter understands troubleshooting patterns that were followed.
2. Use the reporter's feedback to improve documentation or the searchability of it.
3. Reach a conclusion to the provided issue.

## The Reporter's Responsibilities
&nbsp;&nbsp;&nbsp;&nbsp;As a reporter, the goal is to have the question/problem answered, so it helps if the question is well written in the first place.
 When I have questions about behavior encountered when troubleshooting software I find following the general template is helpful:
> 1. The question I need answered
 2. A description of the problem that led to this question
  * Provide the inputs causing the problem
  * Provide the resulting outputs (usually) in the form of logs
  * Provide what the expected output/behavior is
> 3. Describe what troubleshooting you have done and list any documentation you accessed in pursuit of an answer.

&nbsp;&nbsp;&nbsp;&nbsp;This model reaches the first two goals I outlined for the reporter.
 The problem is clearly stated for the support associate to complete the query and this also shows an attempt to solve the issue was made.
 Providing logs and pointing out what the expected behavior should be based on what the log does (or does not) say given the input gives the support associate an opportunity to correct misunderstandings about the observed behavior.

&nbsp;&nbsp;&nbsp;&nbsp;It's worth pointing out what a bad question is, and yes there is such a thing despite every third grade teacher stating otherwise.

 > A bad question is a poorly asked one.

&nbsp;&nbsp;&nbsp;&nbsp;Bad questions do not give people helping with it enough information to understand a problem, let alone solve it.
 Bad questions can also just be cries of "can someone explain X to me?" without any pretext.
 Bad questions sometimes aren't even questions (give [nohello.com](https://www.nohello.com/) a read).

&nbsp;&nbsp;&nbsp;&nbsp;It is also the reporter's responsibility to follow up on with any resolutions reached.
 Often, a support associate will respond with "go try X", and not coming back with any answer of whether or not it worked is just rude and doesn't give anyone following the thread help with solving their issue.
 This increases the burden on support associates even more since support channels can be used as, or to build, documentation.
 Any good IM or ticketing tool is searchable, so others with similar questions can find the old query and use it to solve another issue.
 Or the resolution could be used to correct gaps in formal documentation (which would have been nice to have in the first place, right?).

&nbsp;&nbsp;&nbsp;&nbsp;Finally the reporter should learn the flaws in their own troubleshooting process.
 Engineers should aspire to be as self sufficient of possible, and only go to a support channel faced with a problem that exhausts their existing troubleshooting models.
 If they were unable to solve the problem initially there is either some technique or troubleshooting pattern was not known or understood.
 Obviously, there are exceptions to this, like if something will weeks of research when it could be answered in a minute within a support channel then go ask the question, but these scenarios are not as common as we may make them out to be.
 An approach I like to take is to blame myself for everything.
 That may sound defeatist or counterproductive, but in a way I find it empowering.
 By putting the responsibility on myself I effectively tell myself that if I understand the problem I will be able to solve it, or find a real defect that would explain the mismatch in expected behavior.
 If I can get input on where I went wrong in my process then that makes me more efficient at solving any problem.


## The Support Associates' Responsibilities
&nbsp;&nbsp;&nbsp;&nbsp;The ultimate responsibility of the support associate is to answer the issue at hand.
 If that bare minimum requirement is not met then there's point of the "support".
 However, if the support associate only answers the question they are doing themselves and the reporter a disservice.
 This is a textbook example of the "teach a man to fish" adage.
 If the support associate only answers the question, that same person may come back the next day with a very similar, if not the same, question.
 With that kind of support practice backlog of issues will remain consistent.
 That may not be a problem now, but as new features are added or supported the backlog will rise eventually to a point of being unmanageable.

&nbsp;&nbsp;&nbsp;&nbsp;The best way to avoid this is to make sure not only answers to commonly asked questions are documented, but also troubleshooting methodologies so that the reporter can learn and understand them.
 A common support pattern could look like this.

> 1. A reporter asks a question.
> 2. The support associate gives a link to a documenting the troubleshooting pattern to follow.
> 3. The reporter follows documentation and comes back to clarify any misunderstandings.
> 4. Misunderstandings are rectified by the support associate.
> 5. Documentation is written and/or improved.

&nbsp;&nbsp;&nbsp;&nbsp;Following a pattern like this provides people the tools to solve their own problems.
 At this point it would be easy to just blow someone off and say, "this question has already been answered," however that kind of dismissive response doesn't encourage the reporter to do do anything for themselves.
 A more productive response would be to say, "follow this documentation and let me know if that solves your problem."
 This gives the reporter something tangible to attempt, and giving documentation (hopefully) is not too much effort.
 They should come back with a binary response of "yes, it did answer my issue," or "no, it did not help."
 This either ends the responsibilities of the support associate or gives them an opportunity to improve documentation.

&nbsp;&nbsp;&nbsp;&nbsp;Tangentially related, the searchability of documentation is incredibly important.
 If people don't know how to find the tool they are looking for then it's hard to expect them to solve their own problem.
 A good follow up question to ask a reporter if a piece of documentation provided worked is "what did you do to try to find documentation initially?"
 If the path they followed is sensible, perhaps it is a good idea to recategorize the documentation or improve the search tool.

## Conclusions
&nbsp;&nbsp;&nbsp;&nbsp;Engineering support channels may never be fun, but they don't have to painful.
 Following basic etiquette when asking questions and answering them can go a long way when reducing the tension between the reporter and support associate.
 The etiquette discussed should allow both parties to meet in the middle and make real progress in growing everyone's understanding of the problem solving process and deepening documentation.
