---
title: Swift Group Lab
source: https://developer.apple.com/videos/play/wwdc2026/8001/
session: 8001
collection: wwdc2026
duration: 01:01:10
fetched: 2026-06-12
via: whisper.cpp ggml-large-v3 (session SD video audio)
transcription: machine-generated, no speaker diarization
---

> Machine transcription of the **Swift Group Lab** SD video via whisper.cpp (`ggml-large-v3`).
> Group labs ship no Apple transcript; this is generated from the audio. Cues are timestamped segments, **not** speaker-attributed turns.

## Transcript

1
00:00:03 --> 00:00:05
- Hello, and good afternoon from Cupertino.

2
00:00:05 --> 00:00:07
Welcome to the Swift Group Lab.

3
00:00:07 --> 00:00:13
I'm Angelica from Developer Relations, and it is my privilege to be here with some great panelists today.

4
00:00:13 --> 00:00:17
We're also excited to hear all the questions you have for us.

5
00:00:17 --> 00:00:25
Now, there are quite a few of you who are joining us today, so we're going to be focusing on the most popular questions, as well as questions that will benefit the broader audience.

6
00:00:25 --> 00:00:35
Now, if we aren't able to answer your question in the time we have, don't fret, 'cause we could continue the conversation in the Swift forums at forums.swift.org.

7
00:00:35 --> 00:00:45
And if you have specific issues, bug reports, feature requests, you can of course submit something in Feedback Assistant, but you can also submit a GitHub issue in the projects directly.

8
00:00:45 --> 00:00:57
Without further ado, let's get to know our panelists, starting across the table with Holly, someone you may recognize from Platform State of the Union, in fact, I think wearing the exact same shirt.

9
00:00:57 --> 00:01:00
- Yes, hi, I'm Holly, I work on the Swift team.

10
00:01:00 --> 00:01:07
I have a particular focus on generics, type inference, compiler diagnostics, and concurrency.

11
00:01:07 --> 00:01:11
I also sit on the open source language steering group and the Swift core team.

12
00:01:12 --> 00:01:13
I'm Corey.

13
00:01:13 --> 00:01:24
I work on the Swift server networking team at Apple, and I have a particular focus on shuffling packets around reliably and quickly to keep everybody's services up and running.

14
00:01:25 --> 00:01:26
Hi, I'm Tony.

15
00:01:26 --> 00:01:47
I work on Foundation, the Swift standard library, and some of our Swift packages like algorithms collections atomics and so forth i'm also on the foundation work group and hello i'm doug i work on the swift language team i've been working on swift since it started so i've worked on a number of features and now i sit on the language steering group well thank you again

16
00:01:47 --> 00:02:11
for joining me today for this q a before we get started with developer questions uh there are tons of updates in swift 6.3 and 6.4 maybe name what which one is your favorite ollie you're nodding over there. Yeah, so I mentioned one of my focus areas is compiler diagnostics, and I'm especially excited about the general diagnostic improvements that are there in Swift 6.4, but also there's a

17
00:02:11 --> 00:02:31
feature that gives you more precise control over warnings in your project. It's called the at-diagnose attribute, and you can use it to do things like suppress deprecation warnings in specific parts of your code, but you can also use it to opt into warnings that are off by default in specific areas of your code where that might be especially important,

18
00:02:31 --> 00:02:34
things like strict memory safety or strict concurrency.

19
00:02:34 --> 00:02:39
And that might provide some finer-grained ways for you to do migration to the Swift 6 language mode.

20
00:02:40 --> 00:02:41
So I'm excited about that.

21
00:02:41 --> 00:02:41
Great.

22
00:02:42 --> 00:02:42
Nice.

23
00:02:43 --> 00:02:51
For me, I think one of the common bugbears for the server community and the networking community is wanting to continue to push structured concurrency.

24
00:02:51 --> 00:03:01
We've got a bunch of great features in that area, particularly around resource cleanup, which is just one of the most challenging pieces to consistently get right, but the big win that structured concurrency gives you.

25
00:03:01 --> 00:03:07
So for me, I think async, defer, and cancellation shields are the two sort of really big winners for me.

26
00:03:07 --> 00:03:08
They're going to clean up a lot of code.

27
00:03:08 --> 00:03:11
It can remove some of the custom cancellation shields we've put in.

28
00:03:11 --> 00:03:12
I'm very excited to take advantage of some of those.

29
00:03:13 --> 00:03:13
That's great.

30
00:03:14 --> 00:03:17
Yeah, for me, do I have to say just one, or can I do more than one?

31
00:03:18 --> 00:03:20
I mean, if we did more than one, we might be here all day.

32
00:03:20 --> 00:03:23
I'll try, but there's so many to pick from.

33
00:03:23 --> 00:03:26
One big one is we have a new subprocess package coming out.

34
00:03:26 --> 00:03:27
It's entirely open source.

35
00:03:27 --> 00:03:28
It's cross-platform.

36
00:03:28 --> 00:03:29
I think it really fits in with Swift's mission.

37
00:03:30 --> 00:03:47
We've done so many improvements to align with some of the performance goals of the overall release and data and URL and many other types and new APIs that are exciting, at least to me, like Progress Manager to make some of the most mundane tasks just easy and accessible in Swift, and we make those APIs for you.

38
00:03:48 --> 00:03:49
That's awesome. And Doug?

39
00:03:49 --> 00:03:50
Fantastic. I'm excited.

40
00:03:51 --> 00:03:53
I mean, all the improvements we've been making to Embedded Swift.

41
00:03:53 --> 00:03:59
So Embedded Swift is about running Swift in tiny places, It was bare metal firmware, small devices.

42
00:03:59 --> 00:04:03
And we've really made the language just much easier to use.

43
00:04:03 --> 00:04:06
So the Swift code you write everywhere else, you can now use in Embedded Swift.

44
00:04:07 --> 00:04:12
And so it's really fun to see what cool things people can build with it now that you can target anything really easily.

45
00:04:12 --> 00:04:20
Yeah, highly recommend also checking out the sample projects and things like that in the GitHub repo because it's so much fun to just explore how Embedded Swift can be used.

46
00:04:21 --> 00:04:24
All right, we've got some great questions from developers.

47
00:04:24 --> 00:04:26
First up, we have one from Syracusa.

48
00:04:27 --> 00:04:32
What's the best way to transfer ownership of data from one isolation domain to another?

49
00:04:33 --> 00:04:44
For example, an actor that generates a large amount of non-sendable data and then wants to pass this data off to another actor without making a copy or relinquishing its own ability to access the data.

50
00:04:45 --> 00:04:48
Holly, do you have any ideas on how you could approach this?

51
00:04:48 --> 00:05:07
Yeah, so the Swift concurrency model has this concept called region-based isolation, where you are allowed to transfer non-sendable data from one actor to another, as long as the original actor can no longer access that non-sendable data after that point of transfer.

52
00:05:07 --> 00:05:14
So there are some cases where the compiler can just know that this is safe because of your use of those values.

53
00:05:14 --> 00:05:30
If you want to deliberately annotate in parameters or return types, there is a keyword called sending that indicates that, you know, it's some non-sendable value that you're going to transfer off or return from an actor to be used somewhere else safely.

54
00:05:31 --> 00:05:39
And then if you need to store those values, that's something that you can only accomplish with some of the unsafe opt-outs today.

55
00:05:39 --> 00:05:40
But there's an active pitch on the forums.

56
00:05:40 --> 00:05:53
I think right now it's called disconnected, but it, you know, is a data type that's meant to preserve that property through, like, actually storing those values if you need to store it and then later on transfer that somewhere else.

57
00:05:53 --> 00:05:55
So that's, you know, an active area of language evolution.

58
00:05:55 --> 00:06:03
I was going to say, if you have any feedback for that, definitely join the conversation in forums because it's still being designed and discussed.

59
00:06:03 --> 00:06:04
All right.

60
00:06:04 --> 00:06:07
Following up on another, there's another concurrency question.

61
00:06:07 --> 00:06:09
So, Holly, this might be for you again.

62
00:06:10 --> 00:06:16
From Ashrafi, what is some advice you give when using Swift structured concurrency?

63
00:06:16 --> 00:06:18
Actually, this also might be a great Corey question.

64
00:06:18 --> 00:06:22
So what are some of the best practices or some pitfalls to keep in mind?

65
00:06:23 --> 00:06:25
Actually, Corey, I would love to hear your perspective.

66
00:06:25 --> 00:06:25
Sure.

67
00:06:26 --> 00:06:33
I mean, structured concurrency works best if you lean into it as sort of assertively as you can.

68
00:06:34 --> 00:06:38
Once you start adding in little escape patches from structured concurrency, you can get into trouble.

69
00:06:38 --> 00:07:01
So it actually really helps to find sort of smaller parts of your code base, which you can refactor as a single unit The way to think about this is you should avoid at almost all costs creating unstructured tasks task dot detached or regular task in it unstructured tasks Unless you are trying to send some work elsewhere to be done, but you should not expect to do that in the mainline flow

70
00:07:01 --> 00:07:11
So task groups become your friend and where you can you want to be making sure you've got objects whose life cycles fit those task groups with that lexical scope.

71
00:07:11 --> 00:07:16
So create an object, use it in that lexical scope, stop using it again.

72
00:07:16 --> 00:07:18
You can help yourself do this.

73
00:07:18 --> 00:07:19
There are a lot of with-style functions in Swift.

74
00:07:19 --> 00:07:25
It's got a bit of an idiom about using with-style functions, and this can really help you with that because it gives you a nice sort of lexical spelling.

75
00:07:25 --> 00:07:27
But you don't have to.

76
00:07:27 --> 00:07:32
You can, for example, use DNit-based cleanup in places if that's going to work for you.

77
00:07:32 --> 00:07:36
You do want to be trying to focus on doing things that way.

78
00:07:36 --> 00:07:58
Once you've made those initial steps, you then, the subsequent trick tends to be don't fan out too much, write linear asynchronous code in your task groups. Each task shouldn't be trying to do too many things in parallel. It should be a recipe. It should be a series of steps, A, then B, then C, then D. And when you have things you need to do in parallel, then you can use your task groups. There are a bunch of

79
00:07:58 --> 00:08:18
patterns that are like this in different parts of the programming ecosystem. If you've ever thought about sort of fork join models, or if you've considered the sort of fan out, fan in, scatter gather programming patterns, those are really useful for doing this kind of structured concurrency work when you've got a lot of sort of repetitive work you have to do in that way. I do think that

80
00:08:18 --> 00:08:37
there are probably some other suggestions on the panel, though, for other ways to try to take advantage of structured concurrency beyond just what I have. Although cancellation shields is the other one. This is the other thing that is important. So when I say we have this, you want to do the scoped resource gathering, one of Structured Concurrency's big wins is the automated

81
00:08:37 --> 00:08:56
cancellation propagation. When you want to cancel the whole task, you want to say, oh, everything I'm doing in service of this is no longer necessary. Maybe the view's been dismissed or on the server side, it's maybe this connection got torn down, the client went away. I don't need to finish this operation I'm doing. That's great. But you might have open a resource or a handle to something that

82
00:08:56 --> 00:09:04
needs to be cleaned up. And that cleanup itself is asynchronous. A really common version for this would be file I/O. You might have partway written a file.

83
00:09:04 --> 00:09:07
You need to flush the data out to a known good state.

84
00:09:07 --> 00:09:10
Or maybe you have a database transaction you'd like to cancel.

85
00:09:10 --> 00:09:18
These pieces of asynchronous cleanup can become problematic if you are not using cancellation shields because you're executing in a canceled context.

86
00:09:18 --> 00:09:22
Most Swift code will sort of refuse to execute in a canceled context.

87
00:09:22 --> 00:09:25
It assumes it should be trying to unwind as rapidly as possible.

88
00:09:25 --> 00:09:32
So in those cases, remembering to have your cleanup use cancellation shields is a really important pattern.

89
00:09:32 --> 00:09:34
And it's a great companion to async defer.

90
00:09:34 --> 00:09:40
You can sort of look at your async defer blocks and say, well, anything I put in here, is that actually going to correctly execute?

91
00:09:40 --> 00:09:43
Are there any await calls in here that I think really will suspend?

92
00:09:44 --> 00:09:45
Maybe I should put this in a cancellation shield as well.

93
00:09:46 --> 00:09:49
That can be a really good sort of one-two punch for getting your resources cleaned up really nicely.

94
00:09:51 --> 00:09:55
I feel like this is an area where non-sendable types can help you.

95
00:09:55 --> 00:09:56
Yeah, 100%.

96
00:09:56 --> 00:10:00
Because, you know, a non-sendable type can't cross concurrency domains.

97
00:10:00 --> 00:10:04
And so in a lot of structured concurrency, you have not really concurrency.

98
00:10:05 --> 00:10:06
You just have asynchronous code.

99
00:10:07 --> 00:10:13
And within that asynchronous control flow, a non-sendable type, you can use it freely, but you can't accidentally escape it out.

100
00:10:13 --> 00:10:20
And so when you're embracing structured concurrency, these non-sendable types help you reason about the fact that this thing is just going to be treated linearly.

101
00:10:21 --> 00:10:22
You can easily reason about it.

102
00:10:22 --> 00:10:23
There's no concurrency there.

103
00:10:24 --> 00:10:27
So that when you do go concurrent, you have a smaller set of things to think about.

104
00:10:28 --> 00:10:47
Yeah, I think that's actually a really interesting additional point about thinking about particularly sendability. I often talk to people who are constantly asking the question, how do I make my data type sendable? And I think it's often not considered enough that maybe you should be making your data types non-sendable. You can actually achieve a lot, especially for sort of ephemeral

105
00:10:47 --> 00:11:00
data types that are used as part of a computation. Making them explicitly non-sendable can actually help you fit your data model much more cleanly, making sure you've got a really clear delineation about what objects you are expecting to pass around and which ones you're not.

106
00:11:01 --> 00:11:10
This can also really help with performance because objects you pass around, you want to make sure that that act of passing them around is as cheap as it can be, which may not be what you need for your intermediate types.

107
00:11:10 --> 00:11:15
Just as a nice little bonus, if you did want to make that actually explicit, there's a nicer syntax for doing that in Swift 6.4.

108
00:11:16 --> 00:11:18
It's tilde sendable, similar to tilde copyable.

109
00:11:18 --> 00:11:22
Instead of previously, you always had to write like an unavailable conformance to sendable.

110
00:11:23 --> 00:11:24
So nice little...

111
00:11:24 --> 00:11:28
One more thing on that too, that I've talked to some people who are really in a rush to adopt Swift 6 mode.

112
00:11:28 --> 00:11:39
And while we think that's a great idea, don't go so far that you use unchecked too much to make it so, because then you're just depriving yourself of the benefit the compiler can give you to know that it's safe.

113
00:11:39 --> 00:11:48
So I think leaning on the compiler and using real logic to make sure that it's truly sendable is important as well.

114
00:11:49 --> 00:11:49
Yeah, absolutely agree.

115
00:11:50 --> 00:11:51
All right.

116
00:11:52 --> 00:11:56
Moving away from that, though, we did talk a little bit about touched on sendable here.

117
00:11:56 --> 00:12:00
The next question we have from a developer, sjk27.

118
00:12:00 --> 00:12:15
Is there an overhead cost to unused or unnecessary conformances, such as sendable, equatable, hashable, identifiable, comparable, et cetera, to every struct just out of easier compiling habits?

119
00:12:15 --> 00:12:18
What actually happens under the hood when such is called upon?

120
00:12:18 --> 00:12:21
Please advise on best practices and common pitfalls.

121
00:12:21 --> 00:12:22
Doug, this might be for you.

122
00:12:22 --> 00:12:23
Sure.

123
00:12:23 --> 00:12:26
So there is some cost to having extra conformances.

124
00:12:26 --> 00:12:28
to things like equatable and hashable.

125
00:12:28 --> 00:12:36
So in these cases, you have the code that's associated with the equality function or the hashing function that's needed to support that conformance.

126
00:12:36 --> 00:12:42
And the conformance itself, even if you don't use it in your code directly, it could be found later.

127
00:12:42 --> 00:12:52
Like if you do an as question mark cast to an any type, like in any equatable type or in any hashable type, it can then discover that code at runtime in that conformance.

128
00:12:52 --> 00:12:59
And so for that reason, the compiler will actually keep those around even if you're not using them directly in your own code.

129
00:12:59 --> 00:13:01
Now, something like sendable is a little bit different.

130
00:13:01 --> 00:13:05
Sendable is essentially just a tag that says, hey, this type is sendable.

131
00:13:05 --> 00:13:09
It doesn't have a runtime representation, so it doesn't have a cost anywhere in the system.

132
00:13:10 --> 00:13:12
Can I ask an extension of this, Doug?

133
00:13:12 --> 00:13:13
Because I've wondered about this a lot.

134
00:13:14 --> 00:13:22
Does this come up with some of the time-to-check expressions as well with some generic overloads on operators?

135
00:13:23 --> 00:13:41
I've seen plenty of people write generic overloads on operators that take a protocol object on their generic on one side or the other, it seems to me like that could also increase your compilation times in those areas. It's possible that it could increase your compilation time. So it widens the set of types that can satisfy that particular overload.

136
00:13:41 --> 00:13:58
And so if you have many overloads around that can satisfy this particular type check problem, it can cause the type checking to get a little slower because it has to sort through which of those overloads actually make sense in the larger context, and what is the best of those.

137
00:13:58 --> 00:14:07
I'd like to add one more thing, too, which is that from an API design point of view, I think it's important to make sure that there's a meaningful conformance to those protocols, right?

138
00:14:08 --> 00:14:12
Don't feel like you have to just conform it because it's there.

139
00:14:13 --> 00:14:18
If it's equatable, make sure it actually can be equated, and the same for the others as well.

140
00:14:18 --> 00:14:26
So I think that helps prevent mistakes later when maybe it's not actually meant to be equatable or sendable or whatever.

141
00:14:27 --> 00:14:30
All right. Thanks so much.

142
00:14:30 --> 00:14:35
We have a question from a developer about AtMainActor.

143
00:14:37 --> 00:14:39
So applying AtMainActor often triggers...

144
00:14:39 --> 00:14:41
Oh, one second. Yes.

145
00:14:42 --> 00:14:47
Applying AtMainActor often triggers a massive chain reaction of async refactoring across the code base.

146
00:14:48 --> 00:15:12
what is the cleanest architectural pattern to stop this concurrency contagion in legacy apps without sacrificing Swift 6 safety? Holly? Yeah, so I think what we're talking about here is when you start to add main actor, you know, to a particular type in your code, you can then only use that type from another main actor context. So anywhere that you're then using that type

147
00:15:12 --> 00:15:35
throughout your code base, you have to go out and propagate that main actor annotation or make that code async so that if that code does end up running off the main actor, it can switch back to the main actor to use the things that are safe. I think there are two general approaches you can use to kind of minimize the amount of propagation that you have to do. One is to...

148
00:15:35 --> 00:15:54
So actually, if everything in your module should be on the main actor, you can go ahead and switch on main actor by default mode. That's just going to make everything on the main actor. And if you have parts of your code that are offloading work or you need to be able to use from off the main actor those then are the ones that you can go and annotate explicitly but otherwise I would say

149
00:15:54 --> 00:16:17
start with the sort of like leaf types in your project and go outward from there similarly it might be the case that only parts of that class that you're trying to make main actor actually need to use mutable state and there might be other things within that type where you can go and selectively mark a certain method as non-isolated if it's not actually touching any of the class's

150
00:16:17 --> 00:16:40
mutable state. And that will make it much easier because then not all uses of that type need to be on the main actor. And so you can be a lot more precise about which code actually needs to be on the main actor versus what doesn't. You should also take a look at, I've seen a lot of cases where there's a static variable somewhere in the code and it was written as static var. Sometimes it's

151
00:16:40 --> 00:16:57
the case that it used to be a computed property that was then later changed to be stored and that var it's actually never mutated anywhere in the code so you should actually take that as an opportunity to make some of your state immutable if it's the case that it's not actually mutated anywhere and then you don't have to mark that as main actor and that will you know if it can stay

152
00:16:57 --> 00:17:23
non-isolated then then it's a bit easier you can just use it from anywhere. Yeah I think some of this is also covered in a session from 2024 I believe. Migrate your app to Swift 6. It's a code a long kind of video where this type of practice is done with a sample project and definitely follow along there. All right. Moving on to the next question from our developer, Yingzu.

153
00:17:23 --> 00:17:50
What are the most essential modern Swift features or official resources you recommend I adopt first to ensure high efficiency and great performance? Thank you all so much. Tony, do you have any Um, I like any, any, uh, an answer for you here. Yeah. Yes. Uh, I did open by saying that we've worked on performance. So yes, absolutely. Um, first of all, I think it's important to profile,

154
00:17:50 --> 00:18:08
right? We have great tools and instruments to actually show you exactly where your time is going. There's a really cool flame graph view now that shows you a breakdown of where everything is happening. And that is what you really should start with to guide the rest of your optimization so that, you know, you're optimizing the code that is actually costing you time and memory.

155
00:18:08 --> 00:18:14
Um, we had, um, a really great talk, uh, last year, uh, in WWC, I can't remember the exact name.

156
00:18:14 --> 00:18:15
It was about performance and Swift.

157
00:18:15 --> 00:18:24
So if you search for it, you can absolutely find it, um, where we took a sample app that did some image processing through a series of steps.

158
00:18:24 --> 00:18:29
And each one we used instruments to show where the slowdown was.

159
00:18:29 --> 00:18:38
And then we applied some of the techniques from modern Swift to, um, to, to make that performance overhead sort of magically disappear.

160
00:18:38 --> 00:19:01
um so uh that's uh definitely a huge part of it and something to check into we've had a really big focus on performance in swift especially in embedded swift this year um with a focus on span on a unique array which is a new type that um is i think maybe just recently accepted into um into swift but it's also available in the swift collections package which has some early

161
00:19:01 --> 00:19:27
prototypes of this and many more pieces of work that are about efficient uh use of data structures so I want to call out as well Nate was the speaker for that swift performance using instruments session new this year on top of being able to use the flame graph is actually top functions so you could actually filter out to the ones that are costing more or spending more time and you could

162
00:19:27 --> 00:19:45
actually use that to do the same practice that Nate does and filter out the flame graph a little bit easier one more thing actually which is worth calling out too it's not all just about which APIs you use, but don't forget all of your basic computer science classes and algorithms and making sure that you're using the correct algorithms in general and looking at sort of the big up

163
00:19:45 --> 00:19:57
performance as well. Okay, great. Let's take a look at the next question. This is from M. Traversoni.

164
00:19:58 --> 00:20:22
I really hope I pronounced that correctly. For teams that did the full Swift 6 strict concurrency migration and annotated everything by hand. What's the recommended path now that the isolation model reduces the required annotations? Should we be tearing out annotations that are now redundant or leave them and let them become no ops? Holly? Yeah, I love talking about stuff like this.

165
00:20:23 --> 00:20:42
I think it's completely fine to have annotations that are redundant in your code. Some people actually prefer to make things be more explicit in certain cases, especially if it's inferred in a way that might not be obvious or inferred in a way that could change with a later modification to the code.

166
00:20:43 --> 00:20:46
I actually have a fun anecdote that's not about concurrency at all.

167
00:20:47 --> 00:20:54
There used to be a compiler warning when you were writing generic code if you stated something like a conformance requirement on a type parameter that was implied by something else.

168
00:20:55 --> 00:21:15
We later fixed a bug that made that warning a lot more accurate, so people were seeing it a lot more in code, and people complained that, actually, I like restating that conformance requirement because it's helpful for me, you know, in documentation or in, you know, reading the signature of the function to know explicitly and to not have to work through mentally this

169
00:21:15 --> 00:21:19
was implied, you know, by some other conformance requirements that we later then just got rid of the warning.

170
00:21:20 --> 00:21:23
You know, it's fine to restate things that are redundant.

171
00:21:24 --> 00:21:35
So if it's something that, you know, is evident from elsewhere in the code, like, for example, if you have non-isolated on a bunch of methods in an extension, you used to not be able to write non-isolated on an extension itself.

172
00:21:35 --> 00:21:39
I think that was new in the Swift 6.0 or Swift 6.2 release.

173
00:21:39 --> 00:21:44
Now, you can just write it directly on the extension, and you can go ahead and remove those non-isolated modifiers if that's your preference.

174
00:21:45 --> 00:21:45
But they're not harmful.

175
00:21:46 --> 00:21:48
It's not harmful to have those explicit annotations there.

176
00:21:49 --> 00:21:51
Yeah, so don't necessarily rip it out.

177
00:21:52 --> 00:21:53
It's just a practice of ripping it out.

178
00:21:53 --> 00:21:55
If you want to stay explicit, you can.

179
00:21:55 --> 00:21:57
Sometimes they provide value.

180
00:21:57 --> 00:21:59
It means someone thought about this.

181
00:21:59 --> 00:22:02
This type absolutely should not be sendable, for example.

182
00:22:02 --> 00:22:03
That's intentional.

183
00:22:04 --> 00:22:05
Or this type absolutely must be.

184
00:22:05 --> 00:22:07
Or this type absolutely must be sendable.

185
00:22:08 --> 00:22:09
And that's documentation.

186
00:22:09 --> 00:22:14
So if someone goes and changes the type later in a way that breaks it, they understand, no, this was deliberate.

187
00:22:14 --> 00:22:16
I shouldn't change this fundamental property.

188
00:22:17 --> 00:22:18
Yeah, that's for sure true.

189
00:22:18 --> 00:22:28
Though I will add that any time you find yourself doing that, it's often a good idea to write in a little comment above it why you thought that, because sometimes you can come back to it and go, well, I clearly meant something by this, but I don't know what it was.

190
00:22:29 --> 00:22:29
Who knows?

191
00:22:29 --> 00:22:45
I'll add on top of that as well, when working on, particularly when you start working on very big projects, you will often end up finding you need to write yourself little helpers, and behaving as though these little helpers have a little API contract that you're going to hold yourself to, even though they don't, is a really good idea,

192
00:22:45 --> 00:22:52
defining what you think the surface of this should be, and to that point it can really help to behave as though you have to apply a bunch of annotations.

193
00:22:52 --> 00:22:55
Actually, the compiler infers, and those are there.

194
00:22:55 --> 00:22:57
Those can be extremely helpful.

195
00:22:57 --> 00:23:02
And sometimes you just get a little bit ADHD about it and want to write equatable and hashable twice.

196
00:23:02 --> 00:23:03
I sometimes do.

197
00:23:03 --> 00:23:05
I find it helps.

198
00:23:05 --> 00:23:07
It works.

199
00:23:07 --> 00:23:08
All right.

200
00:23:08 --> 00:23:13
Next question, and this is about sendable.

201
00:23:13 --> 00:23:23
From our developer Peter Belakonski, why is user defaults not sendable, given that the doc says it is thread safe?

202
00:23:23 --> 00:23:25
Is this a preliminary step?

203
00:23:26 --> 00:23:28
Yeah, great question.

204
00:23:28 --> 00:23:38
So a few years ago when Swift introduced sendable in general, we did an audit of everything in Foundation and went through and said these things are sendable for sure.

205
00:23:39 --> 00:23:41
These things are absolutely not sendable.

206
00:23:41 --> 00:23:44
And then we were left with this sort of third category of things where they're classes.

207
00:23:45 --> 00:23:48
So maybe the superclass is sendable, but a subclass is not.

208
00:23:48 --> 00:24:14
And an easy example to understand how this could happen is NS string is immutable and sendable, but NS mutable string is a subclass and obviously mutable and not sendable. And so for classes where it's generally not subclassed often, but it could be, we had this sort of middle state of, well, it's not safe. And again, like I said earlier, we didn't want to be in a rush to mark something as sendable if it actually

209
00:24:14 --> 00:24:38
wasn't true because it defeats the whole purpose of the exercise. And so for some of those cases, we chose to mark them not sendable. Now that said, I believe we have a new feature in Swift 6.4, right, Holly, to allow us to more accurately annotate those types. And so that's something that we're in the process of doing now. Yep, that's the tilde sendable feature I mentioned

210
00:24:38 --> 00:24:51
earlier. It's actually not quite the same thing as an unavailable conformance to sendable. That says this type and all of its subclasses are definitely not sendable. Whereas if you have tilde sendable, That's just a lack of a conformance to sendable.

211
00:24:51 --> 00:24:56
So subclasses could have that unavailable conformance to sendable if they have mutable state.

212
00:24:56 --> 00:25:03
But other subclasses, if they don't add any mutable state and they're perfectly thread safe, you can use those.

213
00:25:03 --> 00:25:05
Those can add a sendable conformance.

214
00:25:05 --> 00:25:13
So it allows more flexibility where subclasses can be either or instead of saying this is definitely not sendable and that's propagated throughout the entire class hierarchy.

215
00:25:14 --> 00:25:21
Yeah. And that said, I believe that the standard global user defaults will give you ascendable conformance, so that is also something to check into.

216
00:25:22 --> 00:25:29
Great. All right. Moving on to the next developer question, another one from sjk27.

217
00:25:31 --> 00:25:56
It was mentioned in What's New in Swift this year. Would it be good practice to migrate to using borrow and mutate instead of get and set altogether? In what cases would you not recommend migrating to this. Doug? I can take that, sure. So borrow and mutate are part of the ownership model that we've been fleshing out in Swift. And so what borrow says is you're essentially

218
00:25:56 --> 00:26:17
getting a reference to some data that is held elsewhere. Like for example, in the struct where you put the property that is getting borrowed. Mutate is like a reference, but it's a mutable reference. So it can be used when you want to change that. That's a little bit different from what get and set are. Get is producing a new value. So it could be referencing just a value

219
00:26:17 --> 00:26:27
that's inside your struct, or it could have been computed on the fly. Similarly, set can go through any amount of code to go ahead and make that change because you're updating at that point.

220
00:26:28 --> 00:26:47
So borrow and mutate can be more efficient because you don't have to create copies. You're not running extra code. You're just referencing something that's there. However, it only works when there is something there and especially in the case of mutate where when you want to mutate something you have to be the only person that can refer to that data and the compiler will make sure

221
00:26:47 --> 00:27:11
that you are the only person modifying that data and so it's a little more restrictive model to get that better performance so i would say where you are performance sensitive and you're essentially sharing out data that you're already storing borrow and mutate are better for all the other cases, get set. Okay. Hope that answers the question, SJK27. Moving on to a great question

222
00:27:11 --> 00:27:37
from Parai Pan. Again, I hope I said that correctly. With a lot of additions like mutex, inline array, span, type throws, non-copyable types, etc., how do we as app developers know what's meant for us versus systems or embedded developers? And how do you recommend keeping up with the pace? Many developers find them very overwhelming. This is a great question. Anyone

223
00:27:37 --> 00:28:00
have any thoughts about this? I can start too. So we've actually in foundation done a lot of this work too. And I think it's important to remember that in general, Swift is designed to be a language around aggressive disclosure, right? You shouldn't have to learn all of the features if you're just getting started. And I think all the people on these work groups and panels and everything have

224
00:28:00 --> 00:28:04
been trying really hard to keep these features with that in mind.

225
00:28:05 --> 00:28:15
And so going back to what I answered earlier about performance, it doesn't make sense to just jump into using non-copyable types if they don't make any sense for your app.

226
00:28:15 --> 00:28:18
So you should absolutely measure first and see if there's an issue.

227
00:28:18 --> 00:28:30
And Nate's talk from last year talks about non-copyable types a little bit and describes what patterns you will see in instruments that indicate that you may have a need for a non-copyable type.

228
00:28:30 --> 00:28:40
So I wouldn't start with that because, as noted here fairly, they do add a complexity to using the language that you may not need from the start.

229
00:28:41 --> 00:28:44
So I recommend absolutely measuring first and then moving into those things.

230
00:28:45 --> 00:28:48
That said, we're trying to make all of these things also work really well together.

231
00:28:48 --> 00:28:55
So unique types and spans and the sendable analysis, all of these things should complement each other.

232
00:28:55 --> 00:29:03
And hopefully when you're in that space, they all can work together well and you don't feel like you're having to get into mixing and matching.

233
00:29:04 --> 00:29:18
Yeah, and the intent here is that when you encounter a problem, like a performance problem, there's a tool that can help you that is similar to the tool you've been using but has these greater restrictions that allow us to compile to faster code or use less memory.

234
00:29:19 --> 00:29:21
You mentioned Unique Array, Tony.

235
00:29:21 --> 00:29:23
And so Unique Array is similar to Array.

236
00:29:23 --> 00:29:24
So we use arrays everywhere.

237
00:29:25 --> 00:29:32
Under the hood, these have copy-on-write semantics, which are great for general optimization and very easy to use.

238
00:29:32 --> 00:29:45
If you do end up with a performance problem where you're seeing these extra copies, you're seeing retain-release traffic show up in your instruments trace, then you can replace your array with a unique array.

239
00:29:45 --> 00:29:46
That's going to take a little bit of work.

240
00:29:46 --> 00:29:50
The compiler is going to tell you, well, it's unique, but you're trying to modify it in two places.

241
00:29:50 --> 00:29:53
You're trying to share it in a few different places where you can't do that.

242
00:29:54 --> 00:30:01
But then the compiler will guide you into these tighter restrictions around the type that allow it to perform better at runtime.

243
00:30:02 --> 00:30:08
And so you shouldn't feel the need to go and learn every Swift feature that there is.

244
00:30:09 --> 00:30:11
Instead, look for where you're having trouble.

245
00:30:12 --> 00:30:21
And then there will be a tool there in Swift to help you in that part of your code go and improve performance or get some extra expressivity.

246
00:30:22 --> 00:30:24
And that's the time to learn about the tool.

247
00:30:24 --> 00:30:42
Yeah. A friend of mine once said to me that language features aren't collectibles. You don't get a prize if you've got one of all of them. And I think that that's a really useful takeaway. I also think it's important to remember that as an engineer, you've got a limited number of hours in the day and you've got things you need to get done. And it is easy to end up spending way too

248
00:30:42 --> 00:31:02
much time trying to get some of the more advanced performance features to work in a setting where they were never needed. So again, to Tony's point about profiling, it's useful to try to bear in mind what problem you're actually trying to solve, have you already achieved the performance that is needed in your situation? If you have, then your time is better spent elsewhere working on bug fixes

249
00:31:02 --> 00:31:22
or adding new features rather than trying to sort of squeeze every nanosecond out of the system you're building. The counterpoint is sometimes that really is necessary. And usually, it feels like a weird thing to say, but usually you will know these moments when you see them. You will spot them in profiling and you will realize that's where you need to go. They tend to be more like

250
00:31:22 --> 00:31:39
isolated too, right? So when you do adopt these features, don't say, well, I guess my whole project has to convert to unique array now. Absolutely not. Keep those things in areas that are on their own so that you don't have to embark on a giant refactoring project at the same time.

251
00:31:39 --> 00:32:07
Yeah, we've had great success introducing spans and a couple of unique arrays just along the hottest path. And doing that gives you almost all the performance gains for a fairly small change to your actual code that's then supported by the compiler yeah great thanks so much um let's take a look at a developer question from jason chung our project has very slow incremental builds

252
00:32:07 --> 00:32:34
with swift emit module often taking minutes splitting into smaller modules didn't help much can swift features like type inference generics associated types affect incremental build performance. How would you diagnose the root cause? So these features can affect build performance in the extreme. Generally, it doesn't affect a project's build performance overall. Once in a

253
00:32:34 --> 00:32:56
while, you will hit some particular expression that takes a long time. If it's specifically the module emission phase that's slow, I would actually expect that it's more related to all the other modules that are being imported. And so with explicit module builds, which is something that we've been rolling out in Xcode and Swift over the last couple years

254
00:32:56 --> 00:33:13
and also the build timeline that you can see in Xcode, you can start to look at that to see where the compiler is actually spending its time. And you may have excess dependencies that are causing these rebuilds or these large rebuilds that would be easier to prune when looking at that particular data. In a sense, it's like performance

255
00:33:13 --> 00:33:16
tuning your code, right? Performance tuning your build, you have to go and see what's going on.

256
00:33:16 --> 00:33:19
to find the places to actually optimize it.

257
00:33:19 --> 00:33:29
- If you wanna learn more about explicit build modules, there is a session on it from, I believe, two years ago, where you could explore a little bit more about that.

258
00:33:29 --> 00:33:31
It is on by default now, right?

259
00:33:31 --> 00:33:34
- I believe so. - Yes, okay, great.

260
00:33:34 --> 00:33:39
Okay, let's move on to, let's take a break from performance questions.

261
00:33:39 --> 00:33:42
We have a question here, back to concurrency.

262
00:33:42 --> 00:33:52
Now that Swift 6 concurrency has been out long enough to see how developers actually adopted it, Is there anything the team would approach differently if designing it today?

263
00:33:52 --> 00:33:54
- Yes, certainly.

264
00:33:54 --> 00:34:07
There was actually two changes throughout the evolution of Swift concurrency that I think if we could go back from the very start, we would just make the latest change that we made, and that's to the behavior of async functions.

265
00:34:08 --> 00:34:36
So particularly in where a non-isolated async function actually runs, so those of you who follow along in Swift Evolution. There were two different proposals that made changes to the behavior of non-isolated async functions. One was to make them always switch to the global concurrent thread pool in order to run there. And then there was a later proposal in Swift 6.2 to actually make them stay

266
00:34:36 --> 00:35:01
on whatever context they were called from. So if you called it from the main actor, the function would stay there. And this was based on our real-world experience and looking at feedback from people using async functions in the context of real-world code. And in particular, people who had adopted Swift's early worn concurrency flag, later complete concurrency

267
00:35:01 --> 00:35:26
checking in the context of apps and services and libraries found that they were actually passing a lot of non-sendable types back and forth between an actor isolated context and these non-isolated async functions. And that was causing a lot of data race safety errors because the original actor isolated context would still have access to those non-sendable values at the same

268
00:35:26 --> 00:35:46
time that that async function was running on the global concurrent thread pool. So we found that a much better default for that was actually keeping them running in the context where they are called from. That means there's no issue with having possible concurrent access to those non-sendable values. And that behavior is still useful, the behavior where you offload it to the global

269
00:35:46 --> 00:36:09
concurrent thread pool, but that's the one that should be explicit and opt-in. So I held on to the belief for a very long time that, you know, those functions running on the global concurrent thread pool was the right model for the long-term, you know, for the long-term benefits. But I was convinced over time and seeing how, you know, issues that that was causing in real world projects.

270
00:36:09 --> 00:36:29
But yeah, I think that's some insight we would not have gotten without real world adoption of those features. But yeah, if we could go back, it would be nice to just have had that insight from the beginning and not have to have any kind of transition where, you know, you adjust the annotations in your code in order to adopt the new behavior. I think it's an interesting trade-off

271
00:36:29 --> 00:36:36
because, you know, back when we had to make a decision of where do these things run? Do they Do they stay on the current task, or do they go concurrent?

272
00:36:37 --> 00:36:45
And if you look at it from a whole systems perspective, well, having more concurrency available lets you get more parallelism out of your system, can be better for performance.

273
00:36:45 --> 00:36:53
But what it ended up doing when we really thought through and people actually tried using the full safety model is that it pushes a lot more types toward being sendable.

274
00:36:54 --> 00:36:57
And that's not the natural way to express all of these ideas.

275
00:36:57 --> 00:37:04
And so, you know, we ended up changing the direction here because of the feedback and came to a better solution.

276
00:37:04 --> 00:37:06
I think it's easier to start with.

277
00:37:06 --> 00:37:09
So it's a much more approachable model of concurrency.

278
00:37:09 --> 00:37:11
It's more like how things work when they're non-concurrent.

279
00:37:12 --> 00:37:15
And it's very explicit the points at which I need to go concurrent.

280
00:37:15 --> 00:37:18
I'm going to introduce concurrency and unlock the parallelism in my system.

281
00:37:18 --> 00:37:23
Yeah, it's like actually opting in or choosing when to introduce that complexity in the project.

282
00:37:24 --> 00:37:24
Yeah, absolutely.

283
00:37:25 --> 00:37:30
Okay, switching over, actually, to Swift Package Manager.

284
00:37:31 --> 00:37:33
We have a question from Mirko KG.

285
00:37:34 --> 00:37:36
Again, sorry if I totally butchered that.

286
00:37:37 --> 00:37:48
What are the notable Swift Package Manager improvements in the latest Xcode and Swift release, especially around build and dependency resolution, or build and dependency resolution performance?

287
00:37:49 --> 00:37:55
Are there changes I should adopt to speed up builds in a large multi-package project?

288
00:37:56 --> 00:37:57
Anyone want to take this?

289
00:37:57 --> 00:38:06
Sure. I think the biggest change for Swift Package Manager in Swift 6.4 release is one that you might not notice because it's not something that you have to opt into.

290
00:38:07 --> 00:38:31
But previously, Swift Package Manager in Xcode and Swift Package Manager that you use in other IDEs like VS Code from the open source Swift.org toolchains used different build system implementations. But now both of them are unified using the Swift build package. And that brings a lot more consistency between these two build systems. There's, you know, a single point of

291
00:38:31 --> 00:38:52
maintenance for bug fixes and future improvements that then you will see in Xcode and anywhere else that you might be using Swift Package Manager. And that, you know, there was a preview of that in the Swift 6.3 release, and in Swift 6.4, that's now on by default. So it's not something you have to opt into, but it's more like under-the-hood infrastructure improvements for Swift Package

292
00:38:52 --> 00:39:11
Manager. And there's a number of performance optimizations that were in the Swift build system that now come to your normal package builds because of that. We talked about explicit modules. That's something that was implemented in Swift build and is now available. There's also other improvements where the build system can be much better about breaking apart the build of a

293
00:39:11 --> 00:39:16
module into separate pieces, so you get more parallelism out of your build from this unification.

294
00:39:18 --> 00:39:28
Great. All right. Now, another highly upvoted question we have from Florentine F, and this - This one's a great one.

295
00:39:28 --> 00:39:35
What's the one Swift feature most developers don't know exists but should know?

296
00:39:35 --> 00:39:39
All the thinking faces.

297
00:39:39 --> 00:39:41
- I don't know if developers should know this exists.

298
00:39:41 --> 00:39:43
This is my favorite.

299
00:39:43 --> 00:39:47
This is the one I like to spring on people and it's not one feature, it's a combination of two.

300
00:39:49 --> 00:40:01
So there are some annotations that you can apply to functions to control the visibility of the function body across modules and the optimizer's decisions about inlining.

301
00:40:02 --> 00:40:05
So for a long time, there's been the inlineable attribute.

302
00:40:06 --> 00:40:11
And there has been, for a long time, there was the at underscore inline, which I believe we recently stabilized under a different name.

303
00:40:12 --> 00:40:13
We have inline always now.

304
00:40:13 --> 00:40:13
Inline always.

305
00:40:13 --> 00:40:14
Excellent.

306
00:40:14 --> 00:40:14
Great.

307
00:40:14 --> 00:40:16
Because that's the opposite of the one I want.

308
00:40:16 --> 00:40:21
So inlineable is incredibly powerful across module boundaries.

309
00:40:21 --> 00:40:25
Inlineable unlocks a huge range of optimization opportunities.

310
00:40:26 --> 00:40:32
But its name can slightly mislead you into thinking the only one that it unlocks is inlineability.

311
00:40:32 --> 00:40:33
It does unlock that one.

312
00:40:33 --> 00:40:41
But it also unlocks things like generic specialization or effects propagation that can really help you optimize or figure out exactly what's going on in your code.

313
00:40:42 --> 00:40:49
Interestingly, you can combine inlineable with the inline never annotation, which says under no circumstances inline this.

314
00:40:50 --> 00:40:55
And that turns out to be an incredibly useful performance tool for generic code with cold paths.

315
00:40:56 --> 00:40:58
This doesn't happen much.

316
00:40:58 --> 00:41:10
I mean, in the entire sort of Swift networking portfolio I can think of, I think we've used it three times, where we have sort of copy functions that end up falling back to a very laborious and slow byte-wise copy operation.

317
00:41:10 --> 00:41:15
And in those cases, it's very helpful to hint the compiler to say, all of that is going to generate a lot of code.

318
00:41:15 --> 00:41:16
I do not want it inlined.

319
00:41:16 --> 00:41:21
I want the first path inlined, because we're going to hit that all the time.

320
00:41:21 --> 00:41:24
Inlineable plus inline never unlocks that benefit.

321
00:41:24 --> 00:41:34
And that is a very powerful trick to have in your back pocket for certain somewhat unusual performance problems when you just cannot get the compiler to reliably inline the thing you wanted.

322
00:41:34 --> 00:41:36
- I've got a very simple one.

323
00:41:36 --> 00:41:49
It's one that I'm sure a lot of people know about, but I remember this took me a while to learn when I, or to realize when I was learning Swift, but writing type annotations with as is super useful.

324
00:41:49 --> 00:42:08
And if there's that, like if I've written a large expression and I've gotten some ambiguity error message from the compiler because there are many overloads, It didn't know which one I meant, and there's a specific type in there that I knew what the type was, and maybe I just need to write a type annotation in that specific part of

325
00:42:08 --> 00:42:10
the expression to influence overload resolution.

326
00:42:10 --> 00:42:30
You can just stick in the as with the type that you expect, and then it could be the case that that influences overload resolution in the right way, or it causes it to infer a type parameter with a concrete type that wasn't otherwise available in the expression, Or sometimes I'll just then get a more precise error message at that point because I was more explicit about what I meant in that part of the expression.

327
00:42:31 --> 00:42:39
But, yeah, I didn't realize how useful just the as, like, plain as was when I was first learning Swift until a while later.

328
00:42:41 --> 00:42:42
I've got sort of a meta one, actually.

329
00:42:43 --> 00:42:46
I think I want to talk about Swift as an open source project.

330
00:42:46 --> 00:42:56
I think that's, you know, some people, I think, may have the impression that the open source part of Swift only matters for, you know, other platforms, not Darwin.

331
00:42:56 --> 00:43:06
But for all the Swift APIs we're talking about today that are part of the language, the standard library, foundation, our networking APIs, all of these things are discussed on the forums.

332
00:43:07 --> 00:43:30
we, I think we get a lot of really valuable, we could get a lot more valuable feedback from people who maybe never think about Swift on server at all, but use Swift on iOS. That's a perspective that is super valuable, and open to everybody. And they may think that it's not for that. But actually, it's the same language and the same libraries everywhere. So I think the feature I

333
00:43:30 --> 00:43:42
would recommend is that the development is happening in the open. And even if you only target one platform, you can still have an impact on the project overall by participating in those discussions.

334
00:43:42 --> 00:43:52
There's all kinds of really interesting APIs that go through, especially when you get a little bit higher up the stack in testing or foundation, that maybe don't get a lot of feedback on the forums today.

335
00:43:52 --> 00:43:56
But I know there's people out there that would find them really useful and interesting.

336
00:43:56 --> 00:43:59
And I encourage people to jump in and participate.

337
00:43:59 --> 00:44:00
RICK VISCOMI: Swift testing's got loads of cool tricks.

338
00:44:00 --> 00:44:01
Yeah.

339
00:44:01 --> 00:44:06
Swift testing's-- I was going to-- there's also a great session on Swift testing, migrating to Swift testing this year.

340
00:44:06 --> 00:44:17
I wanted to add to that as well, another feature to add to that is you don't have to contribute like a PR to participate in this community.

341
00:44:17 --> 00:44:37
I think a lot of people think, oh, I don't know anything about the Swift compiler, I can't possibly participate in this, but there was a conversation I think last year where Seema talked about updating diagnostics, just like what is written in them, or other projects with syntax is a great one to start with, Or, again, just participating in the conversation and forums.

342
00:44:37 --> 00:44:42
You don't have to necessarily be the one submitting something for the evolution proposals.

343
00:44:43 --> 00:45:00
Even for diagnostics, I think one of the best ways to contribute, it really is contributing and shape improvements, is to ask questions about a compiler error message and explain what you found confusing about it or what you tried based on that error message that didn't work out.

344
00:45:00 --> 00:45:04
And that can lead to improvements to make those more actionable, for example.

345
00:45:05 --> 00:45:08
That applies to APIs as well, I think, on the API designer front.

346
00:45:09 --> 00:45:14
When you're designing APIs, the same as when you're writing error messages, you've got every piece of context about the problem in your head.

347
00:45:14 --> 00:45:23
And so things will be very obvious to the API designer that are, it turns out, completely non-obvious in the API, no matter how much review you go through to try to polish out all the edges of that.

348
00:45:23 --> 00:45:26
It gets in front of real users, and they say, that makes no sense to me at all.

349
00:45:27 --> 00:45:29
And that is really valuable feedback.

350
00:45:29 --> 00:45:29
Absolutely.

351
00:45:29 --> 00:45:42
Because then we can go back and think about what is the appropriate way to explain this So that, you know, people that don't have the background that I have necessarily or only have the background of using related APIs nearby, that we make sense for them.

352
00:45:42 --> 00:45:46
And often, you know, sometimes there's radical shifts in the way we want to present things.

353
00:45:46 --> 00:45:53
I do want to add to you, though, that even if you see an API proposal and it looks completely fine to you, feel free to jump in and say so.

354
00:45:54 --> 00:45:55
That's valuable feedback, too.

355
00:45:57 --> 00:46:01
And, of course, like our cross-platform SWIFT is also, you know, super important.

356
00:46:01 --> 00:46:07
And I just want to call out specifically that I think we could actually use more involvement from iOS developers too.

357
00:46:07 --> 00:46:07
100%.

358
00:46:07 --> 00:46:09
Can I add a trailing one other feature?

359
00:46:09 --> 00:46:11
I just want to steal one more.

360
00:46:11 --> 00:46:17
The integer overflow APIs seem to be a real surprise to people that they're there.

361
00:46:17 --> 00:46:24
Actually really helpful if you are working with integers that you have a real suspicion are going to end up big fast.

362
00:46:24 --> 00:46:31
Swift has a bunch of APIs that will let you carry the overflow flag around so that you can run your whole straight line computation and then at the end just figure out, Oh, did any of that go wrong?

363
00:46:31 --> 00:46:32
Very helpful.

364
00:46:32 --> 00:46:33
Very, very helpful.

365
00:46:33 --> 00:46:34
Easy to miss.

366
00:46:34 --> 00:46:38
I almost only ever remember them after I have overflowed the integer before.

367
00:46:38 --> 00:46:39
Makes sense.

368
00:46:39 --> 00:46:42
That's in the class of APIs where when you need them, you really need them.

369
00:46:43 --> 00:46:43
I'm glad they're there.

370
00:46:44 --> 00:46:45
I did have a feature.

371
00:46:45 --> 00:46:46
It's a language feature.

372
00:46:46 --> 00:46:50
Because it keeps coming up, and I don't think people notice that keypads exist sometimes.

373
00:46:51 --> 00:46:52
It's interesting.

374
00:46:52 --> 00:46:57
It's, you know, people come and say, well, I need to, you know, go ahead and abstract over these different properties.

375
00:46:57 --> 00:46:58
I don't know how to do it.

376
00:46:58 --> 00:47:00
And they'll build up big piles of closures or something.

377
00:47:00 --> 00:47:03
And I'll point out, you know, there's a key path here.

378
00:47:03 --> 00:47:06
You can just refer to a property in the abstract without the instance.

379
00:47:07 --> 00:47:09
And they say, oh, I remember hearing about that.

380
00:47:09 --> 00:47:10
And then they can go and build it.

381
00:47:10 --> 00:47:11
This keeps happening.

382
00:47:12 --> 00:47:13
And so it's there.

383
00:47:14 --> 00:47:14
It's been there for years.

384
00:47:15 --> 00:47:16
But somehow it gets missed.

385
00:47:16 --> 00:47:30
It's an interesting part of what Tony just mentioned as well, which is this is very one of the things that helps with sort of trying to mix the different engineering cultures in the community a bit more, which is that I feel like the server ecosystem, particularly around things like database drivers, lives and breathes keypads.

386
00:47:30 --> 00:47:35
They're all over the API surface, because they let you write these very elegant API constructions.

387
00:47:35 --> 00:47:38
And so it's very funny when I bump into someone who has never worked with them at all.

388
00:47:38 --> 00:47:45
I'm like, oh, I don't think you could have gone five minutes writing a web app without using Kibbutz.

389
00:47:45 --> 00:47:47
I actually have one as well.

390
00:47:47 --> 00:47:49
Swiftly.

391
00:47:49 --> 00:47:56
I think a lot of-- again, if you're using something like VS Code, you might be more familiar with this or CLI.

392
00:47:56 --> 00:47:59
But you can actually do this from Xcode directly as well.

393
00:47:59 --> 00:48:05
So if you want to play around with a newer tool chain and try out some experimental features, check out Swiftly.

394
00:48:05 --> 00:48:07
It's a really easy way to install those tool chains.

395
00:48:07 --> 00:48:10
But yeah, that was mine.

396
00:48:10 --> 00:48:14
All right, next, let's move on to another question.

397
00:48:14 --> 00:48:30
Going back to equatable, hashable, comparable, has a question on what's left in language evolution to get tuples to finally conform to equatable, hashable, comparable, et cetera, conditionally?

398
00:48:30 --> 00:48:34
- Yep, so this is an evolution of parameter packs.

399
00:48:34 --> 00:48:36
So now we have the base concept in the language.

400
00:48:36 --> 00:48:55
What we need in order to support conditional conformances of tuples to equatable hashable is the ability to write an extension over a tuple type where its element types are represented with a parameter pack because they each could be different, you know, concrete types, it doesn't matter what they are.

401
00:48:55 --> 00:49:02
We just need the ability to write a where clause that has that condition of each of those elements in the parameter pack.

402
00:49:02 --> 00:49:05
If you're familiar with parameter pack, the keyword is literally each.

403
00:49:06 --> 00:49:12
To say where each T conforms to the protocol that you're looking to conform to.

404
00:49:12 --> 00:49:17
So we need a syntax to write a tuple with a parameter pack.

405
00:49:18 --> 00:49:26
Similarly for the extension syntax to write a parameterized extension where the extension itself has a parameter pack.

406
00:49:27 --> 00:49:29
And then that's it.

407
00:49:29 --> 00:49:33
So it's a fairly small evolution to Parameter Packs.

408
00:49:33 --> 00:49:42
I actually think there's an experimental implementation that lives in the compiler repository right now that's not completely there, but is almost all the way there.

409
00:49:42 --> 00:49:52
And so once that's done, just revisiting the older proposal that used more bespoke conformances for those three protocols specifically.

410
00:49:52 --> 00:50:15
But I think what we want is now that we have Parameter Packs offering that as a general feature so you could add conformances to your own protocols to tuples where that makes sense as well. Great. Hopefully that answers the question. Moving into the next question. This is on strict concurrency. So this might be for you, Corey. Let's see. Charlie

411
00:50:15 --> 00:50:34
Polly is asking, with Swift 6 strict concurrency enforcements, what is the recommended pattern to ingest high-frequency sensor data on a background actor and safely stream those updates to an at-observable data model on the main actor without blocking the UI, this also might be good for you, Tony.

412
00:50:34 --> 00:50:36
Yeah, I think it's a combo platter.

413
00:50:36 --> 00:50:38
This one might be me, Tony, and Holly, actually.

414
00:50:38 --> 00:50:39
Possibly everybody.

415
00:50:39 --> 00:50:40
We can include Doug.

416
00:50:43 --> 00:50:50
The question is going to boil down to what does high frequency mean in this context, exactly how frequency is high.

417
00:50:50 --> 00:50:54
there are different domains where your answers will change.

418
00:50:54 --> 00:51:02
If high frequency is still substantially lower frequency than the rest of your UI updates, then you might actually have no work to do.

419
00:51:02 --> 00:51:05
This might be very, very straightforward indeed.

420
00:51:05 --> 00:51:11
But assuming it is really high, one of the things you're gonna wanna do is avoid context switching excessively.

421
00:51:11 --> 00:51:19
So you wanna make these switches from your background actor to your observable data model on the UI as infrequently as possible.

422
00:51:19 --> 00:51:22
So there are two paths we can go down to.

423
00:51:22 --> 00:51:24
One of them we can talk about debouncing.

424
00:51:24 --> 00:51:29
To leverage debouncing, one of the other things you're going to want to try to do is accumulate data.

425
00:51:29 --> 00:51:38
So rather than sending one update for every data change, can you coalesce these down into a smaller number of events and bring them all over?

426
00:51:38 --> 00:51:40
There's also a question here about data loss.

427
00:51:40 --> 00:51:46
How much data loss is acceptable for your background sensor, for example, if that might be a-- let's say it's a voltmeter.

428
00:51:46 --> 00:51:48
For example, we're measuring electrical voltage.

429
00:51:48 --> 00:51:51
do you need an update every nanosecond rendered to the screen?

430
00:51:51 --> 00:51:53
The answer is probably no.

431
00:51:53 --> 00:51:56
So you can probably naturally debounce with data loss.

432
00:51:56 --> 00:51:58
It's fine if you don't print the intermediate values.

433
00:51:58 --> 00:52:04
Or you can maybe save the data elsewhere, but your UI may not need to actually show every update.

434
00:52:04 --> 00:52:11
SwiftUI, of course, an observable work to naturally coalesce updates as well for efficiency.

435
00:52:11 --> 00:52:14
So that's something you just get for free with SwiftUI.

436
00:52:14 --> 00:52:19
But I would really consider what the requirements of what the UI has to show are.

437
00:52:19 --> 00:52:21
And the question mentions blocking the UI.

438
00:52:21 --> 00:52:34
I think it is important that if there is a lot of work happening, to make sure it is happening asynchronously in the first place, and then keeping the UI updates just for what the user needs to see, that might be a slightly different problem than having to stream everything.

439
00:52:34 --> 00:52:37
So think about maybe splitting the problem in half, perhaps.

440
00:52:37 --> 00:52:38
100%.

441
00:52:38 --> 00:52:51
If you are needing to do some debouncing, I think Swift Async Algorithms has a debounce implementation that you can fit on top of your standard async sequence model, which is a great way to sort of start doing some of that as well.

442
00:52:52 --> 00:52:52
Great.

443
00:52:52 --> 00:52:53
Nothing to add.

444
00:52:53 --> 00:52:56
Yeah, Holly looked like a you-got-everything kind of face.

445
00:52:56 --> 00:52:57
All right.

446
00:52:57 --> 00:53:01
Let's move on to the next question, and this is around performance.

447
00:53:03 --> 00:53:14
Why is Tuple more expensive than Struct when returned from a method, or is it a custom case and there's actually no difference?

448
00:53:14 --> 00:53:16
This is a surprise to me.

449
00:53:17 --> 00:53:17
Yeah.

450
00:53:18 --> 00:53:18
News to me.

451
00:53:18 --> 00:53:45
Surprise. Okay. So they are handled differently. And so this is getting down into the guts of the compiler. But generally when you're dealing with structs, you'll pass the whole struct as an entity, single entity. When you have a tuple, we'll often break that apart. And so if you're passing a tuple into a function, it'll be as if you had sent each element to the tuple as a

452
00:53:45 --> 00:54:07
separate parameter. We call this exploding the tuple inside the compiler. It is plausible that if you had a very large tuple, that this would be less performant than just keeping it all together as you might do with a large structure. Maybe that's what we're seeing, but I think we'd really have to go and look at the actual code and what the optimizer produces to understand why, you know,

453
00:54:07 --> 00:54:11
we're all puzzled by this. Would you like a GitHub issue, Doug? I would love a GitHub issue, yes.

454
00:54:12 --> 00:54:12
GitHub issue.

455
00:54:12 --> 00:54:13
Action, yes.

456
00:54:13 --> 00:54:14
Sample projects.

457
00:54:14 --> 00:54:16
GitHub issue with a sample project, please.

458
00:54:16 --> 00:54:17
Yes.

459
00:54:17 --> 00:54:18
All right.

460
00:54:18 --> 00:54:21
Moving on to the next question.

461
00:54:23 --> 00:54:28
What's your favorite quality of life or quality of code feature in Swift?

462
00:54:28 --> 00:54:34
Not the obvious ones like structured concurrency, but neat lesser-known things that make Swift especially fun to write.

463
00:54:34 --> 00:54:35
This is a great question.

464
00:54:38 --> 00:54:40
I'm going to use sort of a forward-looking one.

465
00:54:41 --> 00:54:46
We mentioned in What's New in Swift this year this work on a new set of protocols called Iterable.

466
00:54:46 --> 00:54:52
And this ties in with some of the performance questions that have been going on, non-copyable, non-escapable.

467
00:54:54 --> 00:55:03
And the reason I find it so interesting is that part of the goal there is to take what might be a pretty complex topic but make it feel like natural SWIFT.

468
00:55:03 --> 00:55:06
Like I want to call four in and I want to four in over this span.

469
00:55:08 --> 00:55:25
And so I think things like that feel like a really simple problem statement, But then when we get down to the details of how to actually implement that in a way that preserves SWIFT's guarantees around safety, lifetime safety and memory safety, it becomes a really challenging design problem.

470
00:55:25 --> 00:55:27
So this is an area, I think, to watch.

471
00:55:27 --> 00:55:41
And we've mentioned in the future directions of that proposal that this is something that we anticipate will start to build up a whole bunch of new container types in SWIFT and new ways to think about how your data is structured in SWIFT.

472
00:55:42 --> 00:55:47
And so maybe that's a different definition of fun than other people have.

473
00:55:47 --> 00:55:59
That's my idea of fun, is being able to design some of these things from the beginning, but doing them in a way that preserves what I mentioned earlier about progressive disclosure and making Swift easy to use from the start.

474
00:55:59 --> 00:56:03
So I think there's going to be a lot of really interesting stuff happening there soon.

475
00:56:03 --> 00:56:10
I think this is sort of often almost taken for granted for people who have been writing Swift for a very long time.

476
00:56:10 --> 00:56:28
But when I first started learning Swift, what I was most amazed by is both the bidirectional type inference and how little type information I had to write explicitly, as well as the diagnostics you get from Swift's generics model of actually being able to diagnose mistake.

477
00:56:28 --> 00:56:49
If there's a mistake in the implementation of the generic code, that's where you see the error message instead of at the use site, which in some other languages that use generic specialization and they monomorphize all of the generics, you don't know that you have a mistake until you try to use that generic API with a type that doesn't work.

478
00:56:50 --> 00:57:14
But I think a lot of those things, especially bidirectional type inference, it's kind of taken as a given in Swift and it's something that you don't realize until you go back to another I mean we write a lot of the compiler code in C++ and when going back and forth between Swift and C++ I you know I have such a great appreciation for how much I don't have to write in my Swift

479
00:57:14 --> 00:57:38
code when I'm using just like basic standard library APIs in my code so I remember being really taken aback by the way the actual implementation of Swift generics worked as well, which is sort of unlike everything else, having come from, again, a services world with a lot of Java, which does sort of type erasure generics. And it's not that, but it's also not

480
00:57:38 --> 00:57:59
global monomorphization. It is its own special thing I thought was really interesting. For me, I'm going to say, I mean, fun to write. I have a different one. I have the thing that I appreciate the most and like holly has stuck with me the entire time i've been writing swift uh because i think swift got it right from the beginning and it's just added to it which is swift's tools for

481
00:57:59 --> 00:58:20
handling avoiding mutable shared state uh that was early on this was the copy on right feature which i still remember being taken aback when i discovered that copy on right was not a compiler trick it was just a feature you could use to build your own types being able to build your own copy on right types is still great i still love doing it um but even without that uh once

482
00:58:20 --> 00:58:22
We've added in some of the ownership model and other pieces.

483
00:58:22 --> 00:58:26
You can still do all of these things in performance-sensitive code as well.

484
00:58:26 --> 00:58:27
And that's really great.

485
00:58:27 --> 00:58:34
I mean, the ability to write code and know confidently that there is no spooky action at a distance that will make this code behave unexpectedly.

486
00:58:35 --> 00:58:37
It is purely a function of its inputs and outputs.

487
00:58:37 --> 00:58:39
It makes writing complex code so much easier.

488
00:58:39 --> 00:58:42
You know what's really fun is taking some C code and replacing it with Swift.

489
00:58:45 --> 00:58:52
And just to your point, right, like, you know, we've been doing that in a lot of projects because we want Swiss guarantees around safety, memory safety, et cetera.

490
00:58:53 --> 00:58:54
I don't know.

491
00:58:54 --> 00:59:04
I find it very satisfying to be able to write what I feel is a very, you know, fluent language for these kinds of things without all of the very sharp edges of writing something in C.

492
00:59:05 --> 00:59:06
Absolutely.

493
00:59:06 --> 00:59:06
Yeah.

494
00:59:07 --> 00:59:09
I'm partial to the generic system.

495
00:59:09 --> 00:59:18
To me, the idea that you can extend a protocol and add an operation on there, a method to do something, it is so wonderfully simplifying.

496
00:59:18 --> 00:59:21
So that didn't come with Swift 1.0, right?

497
00:59:21 --> 00:59:21
We added it later.

498
00:59:22 --> 00:59:28
But it was very interesting because, you know, generics are considered a hard feature in many languages.

499
00:59:28 --> 00:59:29
People are afraid of them.

500
00:59:29 --> 00:59:30
They don't want to learn them.

501
00:59:31 --> 00:59:35
And yet, when you extend a protocol, you're building a generic algorithm.

502
00:59:35 --> 00:59:55
them. But we've made it so natural and simple, like the checking that Holly talks about and just the syntax just sort of melts away. You're just building something on a collection or you're just building something on a collection of equatable elements. And it's just as easy as writing non-generic code. I think that leads us to better, more reusable code patterns throughout the

503
00:59:55 --> 01:00:19
language. Yeah, absolutely. 100%. What a great question to end our group lab on. That's all the time we unfortunately have today. Thank you so much to all our panelists, Holly, Corey, Tony, Doug, for all your insightful answers today. And thank you to the folks behind the scenes helping triage questions and keeping this running smoothly. Most of all, thank you to everyone who

504
01:00:19 --> 01:00:41
joined us today, whether you asked the question or just tuned in. If you haven't already, check out swift.org for a ton of resources, documentation, and blog posts. There's also a new generative AI experience in developer.apple.com to get answers about frameworks, design, accounts, everything related to development. It actually includes all the Swift documentation and resources from

505
01:00:41 --> 01:01:01
swift.org. And if you didn't have your question answered today, let's continue the conversation in forums.swift.org. Lastly, check out the Swiftlang organization in GitHub. And like I mentioned earlier, you could file feedback, bug reports, and enhancement requests through GitHub issues. And speaking of feedback, you should receive an email with a survey link to let us

506
01:01:01 --> 01:01:07
know about your experience at WWDC. We would love to incorporate your feedback in future events.

507
01:01:07 --> 01:01:10
Thank you for joining us. Have a great WWDC.
