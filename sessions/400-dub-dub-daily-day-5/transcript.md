---
title: Dub Dub Daily: Day 5
source: https://developer.apple.com/videos/play/wwdc2026/400/
session: 400
collection: wwdc2026
duration: 00:07:35
fetched: 2026-06-12
via: whisper.cpp ggml-large-v3 (session SD video audio)
transcription: machine-generated, no speaker diarization
---

> Machine transcription of the **Dub Dub Daily: Day 5** SD video via whisper.cpp (`ggml-large-v3`).
> Group labs ship no Apple transcript; this is generated from the audio. Cues are timestamped segments, **not** speaker-attributed turns.

## Transcript

1
00:00:00 --> 00:00:05
This is it. Welcome to DubDubDaily, the final day of WWDC 26.

2
00:00:05 --> 00:00:07
What a week it has been.

3
00:00:07 --> 00:00:10
We've got two more group labs this morning, and they're good ones.

4
00:00:10 --> 00:00:16
As a reminder, all group labs will be available on demand, so you can come back and watch them whenever you like.

5
00:00:16 --> 00:00:19
Before we wrap, though, there's one more conversation you need to hear.

6
00:00:20 --> 00:00:25
Swift has been a through line all week, and Holly Borla is just the right person to close us out.

7
00:00:25 --> 00:00:28
My colleague Jeff sat down with her earlier this week. Here they are.

8
00:00:29 --> 00:00:32
Holly Borla, Engineering Manager on the Swift team.

9
00:00:32 --> 00:00:33
A lot's going on for you this year.

10
00:00:34 --> 00:00:44
You say in so too this year with slides that I believe to be your actual memoji, that Swift this year is designed entirely to help developers get back to doing the most important thing, which is having fun.

11
00:00:44 --> 00:00:45
Tell me a little bit more about that.

12
00:00:45 --> 00:00:45
Yeah.

13
00:00:45 --> 00:00:47
To me, that means two things.

14
00:00:48 --> 00:00:52
The first is writing really clear and beautiful code.

15
00:00:52 --> 00:01:00
Oftentimes when I'm writing code in Swift, by the time I'm done, And I'm really proud of the way that the code I've written reads.

16
00:01:01 --> 00:01:05
And then the other part of it is the confidence that the code you wrote is correct.

17
00:01:05 --> 00:01:10
And the compiler being there to help catch your mistakes is a really key piece of that.

18
00:01:11 --> 00:01:20
So in order to write clear and beautiful code, one of our goals is to eliminate friction points that come up when you're writing code on a day-to-day basis.

19
00:01:20 --> 00:01:41
So a lot of the updates that you'll see in Swift 6.4 are removing things that are friction point so that those things are out of your way and you can really focus on expressing your ideas in a really clear and beautiful way. What are some of the first things developers will notice this year? I actually think there are a lot of updates that developers might not notice until they're

20
00:01:41 --> 00:02:04
pointed out that they're no longer issues. For example, there's a much more streamlined way to use some and any types in Swift together with optionals. Previously, you had to write a little bit more syntax with parentheses and now what people naturally try to write will just work so it's something that um smooths over that uh developer experience of writing swift code but

21
00:02:04 --> 00:02:25
it might not be something that you notice but all these little things add up to really improve your your productivity when writing day-to-day swift code same thing with uh in in concurrent code calling an async method in a defer block previously there was a limitation where the compiler would prevent you from doing that. And now that also just works in the way that you would expect you

22
00:02:25 --> 00:02:43
can call async code in the same way that you can in the rest of that function body. And again, unless you knew that was a limitation before, it might not be something that you notice. It's just something that you're not prevented from doing. So the code you naturally write works, you move on, and it's not something that you have to deal with. So addition by subtraction. And the best

23
00:02:43 --> 00:02:46
thing when things just work and you didn't notice that they didn't work before. That's fantastic.

24
00:02:46 --> 00:02:48
Lots going on this year for you that people can see.

25
00:02:48 --> 00:02:54
What's something maybe happening under the hood, something that's maybe not as readily apparent, but that you and your team are very proud of?

26
00:02:55 --> 00:03:02
One of the areas of compiler development that really excites me that's not like a flashy new language feature is diagnostic improvements.

27
00:03:02 --> 00:03:05
So improving the error messages that come from the compiler.

28
00:03:05 --> 00:03:12
And I think this is a really important area because it's the main feedback that you get when you're writing Swift code.

29
00:03:12 --> 00:03:17
It helps you internalize the language features that you're working with, understand what works and what doesn't.

30
00:03:18 --> 00:03:29
And it can also be really frustrating if you get an error message that isn't easily understandable or doesn't make it immediately clear what you need to do in order to fix your code.

31
00:03:29 --> 00:03:34
So there's a lot of different kinds of diagnostic improvements this year in Swift 6.4.

32
00:03:34 --> 00:03:42
Some of them are more infrastructural to improve diagnostics both now and ongoing in the future.

33
00:03:43 --> 00:03:46
But there's also more ways to control diagnostics in your code.

34
00:03:46 --> 00:03:54
One of my favorite new features is the at diagnose attribute, which lets you control behavior of warnings in your code.

35
00:03:54 --> 00:04:09
This lets you do things like suppress deprecations, but it also lets you do things like opt into strict memory safety or strict concurrency diagnostics on a more granular basis, which helps with, for example, the migration to the Swift 6 language mode.

36
00:04:10 --> 00:04:16
So there's a lot across the board that gives you more control and also just general diagnostic improvements.

37
00:04:17 --> 00:04:22
There's a fun little moment in SO2 where you mentioned a specific issue that has been improved.

38
00:04:22 --> 00:04:24
Can you tell me about what that issue is?

39
00:04:24 --> 00:04:29
Yeah, the error message is the compiler is unable to type check this expression in reasonable time.

40
00:04:29 --> 00:04:35
So if you've written some, you know, significant Swift code before, you've surely seen this error message.

41
00:04:35 --> 00:04:37
It's a fallback in the compiler's type checker.

42
00:04:37 --> 00:04:50
So the thing that the subsystem that does things like type inference and overload resolution, it determines when you call a function, which function of the many overloads that might be available you actually meant to call.

43
00:04:50 --> 00:04:57
And that has some performance thresholds, both with an approximation of time as well as memory that's used on your system.

44
00:04:57 --> 00:05:12
And if it goes over those thresholds, you'll get this fallback error message, which can be really frustrating because sometimes you're not even sure if the code was correct and the compiler timed out, or if you had a mistake somewhere in your code and the compiler timed out trying to provide you with a more precise diagnostic.

45
00:05:13 --> 00:05:22
So this is something that we've been continuously working on the developer experience of to try to mitigate the, you know, instances where you will see this error message in practice.

46
00:05:23 --> 00:05:44
This year, we focused a lot on mitigating that error in nested closures and Swift UI view bodies, which is a really common place to see it. There's still some more work to be done. And you can follow along with that through the open source Swift project, if you're interested, all of the work for that is done there. And we post, you know, roadmaps and updates when we make progress.

47
00:05:45 --> 00:05:57
But we know that that's a really important part of the developer experience to improve on, because that is something that when you get that, it can be really difficult to determine what you need to do in order to fix it.

48
00:05:57 --> 00:05:59
Lots of things for people to try out this year.

49
00:05:59 --> 00:06:00
What are you excited for people to get their hands on?

50
00:06:00 --> 00:06:08
Yeah, I'm really excited for people to try out the new features for controlling the behavior of diagnostics in their code.

51
00:06:08 --> 00:06:23
I think this has the potential to lead to some more improved workflows, especially for migrating to enabling certain kinds of diagnostics like strict concurrency checking, which will help with, you know, migration strategies to the Swiss six language mode.

52
00:06:24 --> 00:06:30
So I'm really excited to see how developers are making use of that in their migration.

53
00:06:30 --> 00:06:46
One of my favorite things to do is to read articles, blog posts, or watch conference talks where people are sharing different strategies for migration or making most effective use of language features in the context of their code base.

54
00:06:46 --> 00:06:52
That's really valuable for me to understand, and I also love that people are sharing those tips with the broader community.

55
00:06:53 --> 00:06:55
Thank you so much, Holly and Jeff.

56
00:06:55 --> 00:06:57
Really wonderful way to close out the week.

57
00:06:57 --> 00:06:59
And the learning doesn't stop here.

58
00:06:59 --> 00:07:07
Join us for WWDC recap activities all around the world to find out how all of these new announcements can benefit your apps and games.

59
00:07:08 --> 00:07:22
Visit developer.apple.com slash events for the latest sessions, workshops, and labs, both online and in person, including at our developer centers in Bengaluru, Cupertino, Shanghai, Singapore, and soon in Berlin, opening later this year.

60
00:07:22 --> 00:07:24
And follow Apple Developer for more details.

61
00:07:25 --> 00:07:28
Time to go check in on what my agent built in Xcode.

62
00:07:28 --> 00:07:35
Thank you for being a part of WWDC. We can't wait to see what you make next. See you out there.
