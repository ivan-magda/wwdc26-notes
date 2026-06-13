---
title: Power and Performance Group Lab
source: https://developer.apple.com/videos/play/wwdc2026/8003/
session: 8003
collection: wwdc2026
duration: 01:00:58
fetched: 2026-06-12
via: whisper.cpp ggml-large-v3 (session SD video audio)
transcription: machine-generated, no speaker diarization
---

> Machine transcription of the **Power and Performance Group Lab** SD video via whisper.cpp (`ggml-large-v3`).
> Group labs ship no Apple transcript; this is generated from the audio. Cues are timestamped segments, **not** speaker-attributed turns.

## Transcript

1
00:00:03 --> 00:00:06
Welcome to the Power and Performance Group Lab at WWDC.

2
00:00:06 --> 00:00:09
My name is Cole, and I'm a Core Technologies Evangelist here at Apple.

3
00:00:10 --> 00:00:22
Power and Performance is about getting the most out of your app, launching quickly when someone opens it, scrolling smoothly in lists and collection views, and being optimized for battery life and storage.

4
00:00:22 --> 00:00:25
So today, I'm joined by a panel of experts.

5
00:00:25 --> 00:00:34
These are just a few of the folks that work on making Apple's own software fast and efficient and build the tools that make it possible, like Instruments, Metric Kit, and more.

6
00:00:35 --> 00:00:40
And in addition to those on screen, there's a whole team of folks behind the scenes helping with the triage of all of your questions.

7
00:00:41 --> 00:00:43
So let's kick this off with some introductions.

8
00:00:44 --> 00:00:45
Maybe we'll start with Terry.

9
00:00:45 --> 00:00:49
Can you just maybe each talk a little bit about yourself and what you're focused on at Apple?

10
00:00:49 --> 00:00:51
Yeah, so I'm Terry. I work on performance.

11
00:00:52 --> 00:00:58
So I help teams across Apple optimize many of the features that were announced this week at WWDC.

12
00:00:59 --> 00:01:03
Cool. Hi, I'm Yanni. I work on Metric Kit.

13
00:01:03 --> 00:01:09
Metric Kit is a framework that lets developers get metric and diagnostic data on device to improve their power and performance.

14
00:01:10 --> 00:01:16
Hi, my name is Kacper, and I'm part of the Instruments team here at Apple.

15
00:01:16 --> 00:01:24
Instruments is part of Xcode Suite, and it allows you to record, visualize, and diagnose your app's performance and power usage.

16
00:01:25 --> 00:01:32
Excited to be here, and whether you're just starting with profiling or you would like to dive a bit deeper, I would love to hear your questions.

17
00:01:33 --> 00:01:35
Hey folks, I'm Kunal. I work on power.

18
00:01:36 --> 00:01:42
I think about core OS power, improving the power for features, applications, and optimizing them.

19
00:01:42 --> 00:01:46
So if you're thinking about battery life, thermals, that's where my team really helps.

20
00:01:46 --> 00:01:49
And I help out with Vision OS, but also iOS third-party applications.

21
00:01:50 --> 00:01:54
Hi, I'm Marco. I'm a performance engineer here at Apple.

22
00:01:54 --> 00:01:59
My main area of focus is the render pipeline, animations, scrolling, all that type of stuff.

23
00:01:59 --> 00:02:01
Great. Well, thank you for joining me this morning.

24
00:02:01 --> 00:02:04
I see already there's a whole bunch of questions.

25
00:02:04 --> 00:02:11
But before we dive into the questions that we're getting online, I just want to start with talking about the new releases.

26
00:02:11 --> 00:02:14
I'd love to hear-- maybe we can-- I don't know who wants to go first.

27
00:02:14 --> 00:02:15
I'm going to look at Caspar to go first.

28
00:02:15 --> 00:02:19
What are you most excited about for performance in the '27 releases?

29
00:02:19 --> 00:02:25
Yeah, I think that when looking at the instruments specifically, I'm really excited about the new run comparison feature.

30
00:02:25 --> 00:02:34
So maybe you don't know, but in instruments, you're able to record and name multiple runs, depending on some of the experiments you're running.

31
00:02:34 --> 00:02:50
And instead of having to have two windows side by side and comparing them visually, now you have this new diffing feature that allows you to compare call trees and understand at a glance what regressed or improved so that you can make an informed decision and iterate further.

32
00:02:50 --> 00:02:51
FRANCESC CAMPOY: Awesome.

33
00:02:51 --> 00:02:53
How about Kunal?

34
00:02:53 --> 00:03:15
KUNAL KUMAL: I think I'm excited about definitely the tooling support that's coming in, but also some of the metric support that's coming in where we have state reporting and ability to slice metrics based on different application states. So we also have Xcode Organizer support to have metric goals. If you're looking at battery life goals or your historical performance of battery life, foreground

35
00:03:15 --> 00:03:35
energy terrain, you have this ability to now see metric goals that helps you figure out if your application is doing poorly or good as far as battery life is concerned. So yeah, lots of exciting things. And Marco, do you have anything that comes to mind? Yeah, I'm really excited for the combination of the state reporting and the metric kit. I feel like on the performance team,

36
00:03:35 --> 00:03:54
one of the hardest parts is just contextualizing what you're looking at and like where the problem actually is. So being able to get that context of this is what the app was doing when there was a problem kind of cuts out the whole first part of trying to triage what went wrong. Totally. And Yanni, I think everyone's still here because I know you worked a lot on these changes to metric

37
00:03:54 --> 00:04:15
kit but um any anything that comes to mind for you yeah i think i'm obviously very excited to hear that people are excited about the new features um for metric kit um it is a very big year for us so um we have a brand new swift first api and of course like the contextualizing piece is a big um help for developers to understand you know what's happening in their app so

38
00:04:15 --> 00:04:36
um bias vote here but i think metric features are very exciting that's awesome and ter yeah i think I'm really excited about just the range of performance optimizations we've made across all the existing features. I think one in particular I'm really excited about is the improvements to launch time. There are a bunch of people on my team that directly worked on that

39
00:04:36 --> 00:05:01
this year, and it's something that all apps get for free. Like, your app will just launch faster, and I think that's really cool. Yeah, that's awesome. And to go just on the theme of Metricit here. Yanni, so, I mean, we kind of talked about the new Metric Kit, but Metric Kit is kind of getting rebuilt from the ground up. Like a lot of the existing Metric Kit API has been marked as to

40
00:05:01 --> 00:05:23
be deprecated. There's a new, as you said, Swift API for the framework. Can you talk a little bit about like the motivation for this change? Like that's a big change to the framework. What is it that, you know, we're hoping to do with the new metric kit? Yeah, for sure. So the Swift first API, of course, will work very seamlessly with Swift, with Swift concurrency. So you can use

41
00:05:23 --> 00:05:45
a lot of our surface with more ergonomics with modern Swift apps. Most importantly, we designed it to work very well with our new granularity in the metric kit framework. So we're not only are we giving developers, you know, the daily report for metrics, but we're also having smaller breakdowns that developers can get more insights in smaller intervals.

42
00:05:46 --> 00:05:52
So the API is designed to sort of accommodate for these new different structures and data.

43
00:05:53 --> 00:05:58
And as well, there's like new diagnostic and metric types that will be only for this new API.

44
00:05:58 --> 00:06:03
So it's hopefully good motivation for developers to move forward with it.

45
00:06:04 --> 00:06:08
Cool. Well, let's start with our first question here.

46
00:06:09 --> 00:06:19
The question that we're getting from our online audience is, as a beginner in iOS development, what are the main factors that affect app power usage and performance in SwiftUI?

47
00:06:19 --> 00:06:23
And how can I design simple apps that avoid battery drain and lag?

48
00:06:24 --> 00:06:26
Who wants to chat about this one?

49
00:06:26 --> 00:06:28
I could probably step in and start it off at the very least.

50
00:06:29 --> 00:06:41
I feel like for SwiftUI, one of the kind of main things you want to do is really separate your views from their inputs and make sure that when you're updating variables or things are changing, you're not redrawing stuff that doesn't actually rely on it.

51
00:06:41 --> 00:06:48
So making sure that the things that your views are looking at and watching are actually things that care about and affect the view.

52
00:06:48 --> 00:06:52
So you're not doing redraws where you just end up with the same thing you had before and doing unnecessary work.

53
00:06:53 --> 00:07:00
So stuff like the new observable macro is really good for this because you only get updates for the fields that you read on those objects.

54
00:07:00 --> 00:07:02
So you kind of get a separation for free.

55
00:07:03 --> 00:07:04
Yeah, totally.

56
00:07:04 --> 00:07:05
That's a great point.

57
00:07:05 --> 00:07:11
And I would say that you can always understand what kind of dependencies you have using the tooling that we have in Instruments.

58
00:07:11 --> 00:07:15
So developers can profile their app using SwiftUI Instrument.

59
00:07:15 --> 00:07:25
And then for the view updates, they can see cause and effect graph that allows them to understand these dependencies and break them up as necessary.

60
00:07:25 --> 00:07:26
Yeah.

61
00:07:26 --> 00:07:32
And to add on to the whole using instruments and stuff, I highly, highly recommend-- last year, there was a talk.

62
00:07:32 --> 00:07:44
I believe it was like optimized Swift UI using instruments that went through kind of a breakdown of how you can go from having an app, profiling it, kind of looking at the information provided by the instrument, and kind of iterating on that.

63
00:07:44 --> 00:07:51
So I highly recommend checking that out if you're interested in kind of profiling, optimizing, kind of looking at the performance of your Swift UI app.

64
00:07:52 --> 00:07:54
And there's two parts that I think, Kunal, is what you're about to say.

65
00:07:54 --> 00:07:55
There's like two parts to this, right?

66
00:07:55 --> 00:08:00
One part of this is like making sure that your Swift UI usage is efficient.

67
00:08:01 --> 00:08:10
There's another part of this which is actually trying to figure out if there's a power issue and if that's the case, where that power issue is.

68
00:08:10 --> 00:08:12
Kunal, do you have thoughts on, like, where do you start for that?

69
00:08:13 --> 00:08:14
Yeah, I do have thoughts.

70
00:08:14 --> 00:08:22
I think SwiftUI performance is, you know, I would say, like, investing in SwiftUI performance is often going to get you power wins as well, right?

71
00:08:22 --> 00:08:23
Because it's compute-bound.

72
00:08:24 --> 00:08:29
When you have compute-bound operations and workloads, reducing the compute is going to help you not just with performance and latency.

73
00:08:29 --> 00:08:30
It's also going to help you with power.

74
00:08:31 --> 00:08:43
So one of the things I would say is whatever, you know, we said about, like, invalidations, avoiding the invalidations of the views, avoiding redraws, making sure you have not a very deeply nested SwiftUI hierarchy.

75
00:08:44 --> 00:08:49
I think those are all good points to not just improve performance, but also power.

76
00:08:49 --> 00:08:52
And we get a lot of signals for these in our existing tools, right?

77
00:08:52 --> 00:09:12
We have energy logs that come in for Xcode Organizer that will share not just like, you would often see like these deep call stacks of SwiftUI, which could like point to not just a CPU issue and an energy issue, but those same call stacks could also be seen in hangs, which is like, you know, oh, so we are not just seeing a hang because of SwiftUI

78
00:09:12 --> 00:09:15
taking a long time because of your UI hierarchy, but also power.

79
00:09:15 --> 00:09:18
So I would say go for it.

80
00:09:18 --> 00:09:22
You know, any SwiftUI optimization is not just going to help you with performance, but also with power.

81
00:09:22 --> 00:09:27
And there's also, we talked about the SwiftUI instrument, there's also the Power Profiler as well.

82
00:09:27 --> 00:09:28
Absolutely, yeah.

83
00:09:28 --> 00:09:36
Which maybe could indicate if you actually do have like a CPU issue that's landing in SwiftUI code.

84
00:09:36 --> 00:09:37
Very good call out.

85
00:09:38 --> 00:09:44
Power Profiler would definitely show up a bunch of the CPU energy being taken, you know, as SwiftUI rendering is going higher.

86
00:09:45 --> 00:09:47
And you might actually see some GPU energy as well out there.

87
00:09:47 --> 00:09:59
So, it's a great, you know, I would say exercise to also run Power Profiler with your application connected and then look at the SwiftUI instruments and the Power Profiler together to figure out, like, what's happening.

88
00:09:59 --> 00:10:03
What are the views that are, you know, causing most of the drain and the performance issues?

89
00:10:03 --> 00:10:04
Yeah, and I want to add on to that.

90
00:10:04 --> 00:10:09
If you're a beginner in iOS app development, you know, maybe don't focus too much on SwiftUI at first.

91
00:10:09 --> 00:10:11
That might not actually be the problem.

92
00:10:11 --> 00:10:21
You can profile your app using a bunch of the other things in instruments just to see overall what is affecting performance of your app, what is using the power, things like that.

93
00:10:21 --> 00:10:26
If it says Swift UI, then yeah, sure, we can definitely follow all this advice and go down there.

94
00:10:27 --> 00:10:29
But it might turn out it's something completely different.

95
00:10:29 --> 00:10:34
Maybe you have some algorithm that's just churning a bunch of CPU, and that's really where all the power drain is going.

96
00:10:34 --> 00:10:40
So it's always good to just look at the overall picture first really before diving into one specific area.

97
00:10:40 --> 00:10:41
I 100% agree with Terry out there.

98
00:10:42 --> 00:10:47
You have to understand the bigger picture, what are the bottlenecks in your application before you start investing.

99
00:10:47 --> 00:10:55
If you don't do a good job at measurement, at analyzing, you might end up optimizing something that's really not going to get you any wins or that might not be important for end users.

100
00:10:55 --> 00:10:56
So that's a great point.

101
00:10:57 --> 00:11:00
And that's not just in instruments too, right?

102
00:11:00 --> 00:11:09
Like sometimes, and I've seen this in discussions with developers too, where, you know, you kind of think you have a problem because it's showing up when you run it locally in instruments.

103
00:11:09 --> 00:11:17
And then you might look at your data in Xcode Organizer and find that actually the biggest cause of, say, hangs in your app is something else.

104
00:11:17 --> 00:11:20
And it's really hard to reproduce at your desk.

105
00:11:20 --> 00:11:24
So kind of using both sources of data, I think, is important.

106
00:11:24 --> 00:11:26
Local profiling is great for some things.

107
00:11:26 --> 00:11:28
Telemetry is really great for other things.

108
00:11:28 --> 00:11:42
I would also say for the Power Profiler workflows, maybe there's one lesser known flow where developers can actually disconnect from instruments, go and record these traces that can take multiple hours and then analyze them at desk.

109
00:11:42 --> 00:11:50
So that sometimes allows them to, like, hey, let's try it out in real world scenarios and not only at your desk in a very controlled environment.

110
00:11:50 --> 00:11:52
That's the-- Un-tethered mode is awesome.

111
00:11:52 --> 00:11:53
That's the performance trace in the developer settings?

112
00:11:53 --> 00:11:53
Correct.

113
00:11:53 --> 00:12:00
That's the performance trace, so developers can capture that and later airdrop to their Mac and open that in instruments.

114
00:12:00 --> 00:12:18
So yeah, both those modes, right, the tethered mode that you say where you're actually working side by side with instruments and the untethered mode like you said they're both very useful it's great um just speaking more about power um we have a question here which is uh what's the biggest power mistake you see in many apps that developers

115
00:12:18 --> 00:12:45
don't realize they're making this is a i mean there's not one right i mean there could be like several and it really depends i would say one of the most common pitfalls is uh not having the right amount of telemetry not having the right amount of instrumentation right and so you end up focusing on areas that are not giving you the biggest return of investment. So understanding how

116
00:12:45 --> 00:13:10
your application is used, what are the scenarios and features that are most heavily used by our users, and instrumenting them, adding metric telemetry, analyzing them in detail, looking at field analytics, looking at Power Profiler or other profiling tools, is a real benefit to figure out what to invest in. And people underestimate this a lot. Often they might find a customer report

117
00:13:10 --> 00:13:33
that says there's one specific issue and over-index on that. While there could be field analytics that's showing a bigger problem or more larger set of issues. So I would say never underestimate the importance of instrumentation and measurement. You always want to measure stuff before you optimize it. Yeah. I think if I had to think of one thing that comes to my mind, I think it's

118
00:13:34 --> 00:13:41
not accounting for different types of state that might affect the power characteristics of the app.

119
00:13:41 --> 00:13:55
I know we've both seen, like, you know, you go to Xcode Organizer, you look at energy logs, and you'll see things like, why is this database taking so long to write and it's taking so much power and it's so much time to write this database?

120
00:13:55 --> 00:13:58
And it could be because a bug is writing too much data.

121
00:13:58 --> 00:14:02
It could just be your app wasn't really tested on very large sets of data, right?

122
00:14:02 --> 00:14:07
And at your desk, connected to Xcode, you can't necessarily replicate that.

123
00:14:07 --> 00:14:11
But then when you look at different sources of data, you find that that's actually driving a huge power issue.

124
00:14:11 --> 00:14:11
Yeah.

125
00:14:11 --> 00:14:12
Absolutely.

126
00:14:12 --> 00:14:16
And maybe part of it is also just inducing different conditions.

127
00:14:16 --> 00:14:18
Like maybe your device is under thermal state.

128
00:14:18 --> 00:14:32
And with Xcode, you could actually use a condition inducer feature to force your app in that state and then see how it behaves in instruments, like how many more hitches, how many more hanks you have, so you can diagnose these scenarios.

129
00:14:32 --> 00:14:34
FRANCESC CAMPOY: Great call out, right?

130
00:14:34 --> 00:14:37
And even the untethered mode sometimes allows you to induce those conditions.

131
00:14:37 --> 00:14:46
Like when you're moving around, when you're traveling, and you have those cellular connections switching, I think both of those are great ways to have those conditions induced.

132
00:14:46 --> 00:14:47
Oh, go ahead.

133
00:14:47 --> 00:14:53
I was just going to say, I think something else that developers can sometimes take for granted is that everything uses power.

134
00:14:53 --> 00:14:56
Every time you're hitting the file system, you're using a little bit more power.

135
00:14:56 --> 00:14:59
Every time you're sending off network requests, you're using more power.

136
00:14:59 --> 00:15:02
more precise location stuff, using more power.

137
00:15:02 --> 00:15:07
So just kind of knowing what your app actually needs to be doing and how often it needs to be doing.

138
00:15:07 --> 00:15:12
If you can send out network requests half as often, that's a free power win.

139
00:15:12 --> 00:15:14
MARK MANDEL: Totally.

140
00:15:14 --> 00:15:18
We talked a little bit about instruments, so I'm going to pick on this question here.

141
00:15:18 --> 00:15:23
The question from the developer is, what is the best documentation for instruments?

142
00:15:23 --> 00:15:25
Are there any written guides?

143
00:15:25 --> 00:15:26
Maybe I'll look at Kasper.

144
00:15:26 --> 00:15:27
KASPER LUND: That's a great question.

145
00:15:27 --> 00:15:29
There's many written guides.

146
00:15:29 --> 00:15:34
But I would say that the best one, if someone is just starting with instruments, is the Instruments Tutorials.

147
00:15:34 --> 00:15:38
So this was written a couple of years ago actually by an instruments engineer.

148
00:15:38 --> 00:15:46
And it was really designed to feel like you have an instruments engineer by you and getting through the app with you step by step.

149
00:15:46 --> 00:15:59
It comes with an associated project that has some of the performance issues built in so that you can just get started and learn how to detect hanks, like how do they appear visually.

150
00:15:59 --> 00:16:03
and then go through the whole process of profiling, fixing them, and instruments.

151
00:16:03 --> 00:16:05
So that would be the resource I would start with.

152
00:16:05 --> 00:16:10
And then as you are reaching for different tools, there are guides written for them.

153
00:16:10 --> 00:16:19
I would also say if you're missing any documentation, I would encourage folks to reach out to us using Feedback Assistant and let us know.

154
00:16:19 --> 00:16:20
Totally.

155
00:16:20 --> 00:16:40
Is there any-- maybe this is too broad of a question, but anything about instruments or maybe some of the tools that are now available in the 27 releases that are a great place to start for beginners like would you recommend like you know using the time profiler as your first pass or is there something else that you'd recommend like if you've never

156
00:16:40 --> 00:17:02
used instruments before start with this and give it a try i personally think using time profiler with the flame graph view is is a really good way to start i find that the way that flame graphs visually show you like how much a particular operation or call stack is actually costing you it's really easy to see like where the performance is going in your app rather than just looking at

157
00:17:02 --> 00:17:06
like a textual outline or something, where you have to look at numbers.

158
00:17:06 --> 00:17:10
You can just directly see, oh, this is a really big portion of where my time is going.

159
00:17:10 --> 00:17:11
Totally.

160
00:17:11 --> 00:17:13
That's a great point, and we've been investing in this.

161
00:17:13 --> 00:17:18
How do we make this data more approachable with top functions as well?

162
00:17:18 --> 00:17:26
So that's yet another mode of the cult review in instruments, and that allows you to see a flat list of top helper and compiler runtime functions.

163
00:17:26 --> 00:17:30
So it's easier than going through deeply nested outlines.

164
00:17:30 --> 00:17:32
and you can see offenders at a glance.

165
00:17:32 --> 00:17:34
- Yeah.

166
00:17:34 --> 00:17:37
The first time someone explained the flame graph to me, I was very confused about what it meant.

167
00:17:37 --> 00:17:40
And then I clicked it, and I was like, oh, this is neat.

168
00:17:40 --> 00:17:42
It actually makes it pretty simple.

169
00:17:42 --> 00:17:44
Okay, there's a question here.

170
00:17:44 --> 00:17:46
This is kind of related to everything we've been talking about.

171
00:17:46 --> 00:17:56
The question is, our app shows UIKit/SwiftUI screens without much background tasks, but it's still using high battery according to Xcode.

172
00:17:56 --> 00:18:01
I'm assuming this is referring to the energy gauges in Xcode.

173
00:18:01 --> 00:18:06
What are the best practices to know what's going on and why this might be happening?

174
00:18:06 --> 00:18:10
Yeah, this is an interesting one.

175
00:18:10 --> 00:18:20
So the UI-- we're saying that the application shows UI get in Swift UI screens without much background tasks, but the developer is still seeing a high battery usage in the background.

176
00:18:20 --> 00:18:21
Yeah.

177
00:18:21 --> 00:18:24
Yeah, this could get interesting.

178
00:18:24 --> 00:18:46
I would say, like, there's probably modes of your application where you are actually scheduling background tasks. So this may not really be to do with your foreground usage of your application. The Swift UI and the UIKit compute should basically reflect as foreground energy for the application. So if you're seeing background energy drain, what you really want to

179
00:18:46 --> 00:18:54
look at is like, are there more different application where you're scheduling background tasks or doing certain location stuff when the application is not in the foreground?

180
00:18:55 --> 00:19:15
And today, you know, I would say the Power Profiler is a great way to look at that. I would use the untethered mode often to see like, hey, are you accidentally scheduling background tasks when you're not supposed to? And provide that. By untethered, you mean the option in developer settings. So you just like record a power trace on your device, not connected to instruments,

181
00:19:16 --> 00:19:24
and then open that up. Exactly. Move around, yeah. Try to replicate a real life scenario and then see what's really happening. And are you scheduling background workloads?

182
00:19:25 --> 00:19:30
So those should then be visible when you then connect and load that trace in instruments again.

183
00:19:31 --> 00:19:34
Yeah, that also shows you a breakdown of energy used by a subsystem.

184
00:19:35 --> 00:19:38
So you can see, like, how much is CPU, GPU display using.

185
00:19:38 --> 00:19:39
Maybe you're just doing something.

186
00:19:40 --> 00:19:43
Maybe the brightness is very high and you're doing a lot of swaps of colors.

187
00:19:43 --> 00:19:48
And so there's many, many reasons where things can go wrong.

188
00:19:48 --> 00:19:49
Networking also shows up a lot.

189
00:19:50 --> 00:20:09
You know, I think one of the things that you could see in the Power Profiler is, like, network that your application is using when in the background. So yeah, these would be the ways I would actually try to narrow in on the problem. Excellent. Just looking through the questions here, this is a bit of a SwiftUI question, but maybe we'll have some thoughts on it.

190
00:20:10 --> 00:20:23
The question is, a theme object, like colors and tokens, is injected through environment object and read in every atomic component, dozens of nesting levels, hundreds of components on screen.

191
00:20:24 --> 00:20:29
At what scale does this become a bottleneck, and is there a recommended alternative for this use case?

192
00:20:30 --> 00:20:33
I guess I can pitch in on this one.

193
00:20:33 --> 00:20:42
It can be a little bit hard to tell specific numbers, especially because what the actual views are can change their behavior and how much of a performance impact they have.

194
00:20:42 --> 00:20:50
In general, putting things in the environment can be a very useful abstraction for passing things down through your view trees.

195
00:20:52 --> 00:21:04
So I think this is probably a case where using the SwiftUI instrument, you can actually kind of see how much downstream effect these changes are costing you because it is a very case-by-case basis.

196
00:21:04 --> 00:21:12
In some cases, maybe it's fine and it causes no issues, but there might be other kind of configurations of your views and your view tree that it might be problematic.

197
00:21:13 --> 00:21:20
And so really getting a view of what's happening and what the downstream costs of those are is really important for figuring out if it is a problem or if it isn't a problem.

198
00:21:21 --> 00:21:23
Any other thoughts on that?

199
00:21:26 --> 00:21:51
Awesome. Great. Let's move on to another question I'm seeing here. This one, I think it might be for Yanni, but we'll see how the conversation goes. How would you load large data sets, like 50,000, 500,000 records in SwiftUI tables? I don't know. I know you're not the SwiftUI expert in the panel here, but I think the interesting question maybe for you is, how would you record

200
00:21:51 --> 00:22:13
or analyze performance and regressions using metric kits? So let's say you're trying to figure this out? Like, you know, how are you going to sustainably or scalably load this large data set in SwiftUI? How do you use Metric Kit to like kind of analyze the performance? Yeah, I think I guess the first question is with a database that's this large, how like how much of it do you actually

201
00:22:13 --> 00:22:34
need to load at a time for your user experience? So really understanding what kind of experience you're providing your users if your view is not actually showing this amount of data. The first question is, is there a particular reason why you're loading that much to begin with? And if you're just working with a database that's that large, then I think, yeah, the first step of

202
00:22:34 --> 00:22:59
optimization is just to think about what you actually need and just load what you needed so that you don't sort of incur, you know, work that you may not need if your user is not scrolling to that view. The other thing in terms of understanding, you know, identifying regression through metric kit or other tools you could of course use the new state reporting function in

203
00:22:59 --> 00:23:24
metric kit that would sort of allow you to basically get information on what state your app is in so you can better understand what is your user doing and triage if you're seeing a regression what is it that is happening in your app so you can sort of pinpoint what the problem area is um so those would be good starting points and yeah charging this problem actually in your

204
00:23:24 --> 00:23:44
session video you basically talked about this exact example right where you're kind of running an experiment in the app to use like small batches or large batches in an app and you're using different states to get different performance metrics for those states um and then that might inform like where do you invest more of your time or how do you build out the rest of the app using

205
00:23:44 --> 00:23:45
in that model.

206
00:23:45 --> 00:23:46
- Exactly.

207
00:23:46 --> 00:23:52
- Interesting, so would I like log my size of the list using state reporting and that would show up and that's it?

208
00:23:52 --> 00:24:03
- Yeah, so the recommendation is you shouldn't be sort of logging very frequently changing numbers, so like you wouldn't be like the number of items on your list, but you can categorize them.

209
00:24:03 --> 00:24:17
For example, if you have a, you know, if you have, you can define what is it that, like your batch size, if it's a large size, medium, or small, then you can use that information to see, okay, within this range, my performance has been changed.

210
00:24:17 --> 00:24:25
Because sometimes it's like 1,000 item versus 1,001 item may not make a difference, but then now you're incurring a lot of cost just to record that.

211
00:24:25 --> 00:24:31
So defining those boundaries that make sense for you to sort of analyze later will be very helpful.

212
00:24:32 --> 00:24:32
Awesome.

213
00:24:33 --> 00:24:37
Well, actually, to quickly add on to the SwiftUI portion of the beginning part of the question.

214
00:24:37 --> 00:24:38
Sure, yeah, yeah, please.

215
00:24:38 --> 00:24:42
What Yoni was saying about the kind of batching things is really important.

216
00:24:42 --> 00:24:51
SwiftUI provides some lazy versions of like HStacks and VStacks that will kind of manage that for you and only load things as you're kind of scrolling and bringing them onto the screen.

217
00:24:51 --> 00:25:12
So you kind of benefit from SwiftUI handling that for you. Yeah, I think that's a really important point just overall in terms of like batching. And like I know one of the things we think about for app launch time is like, what is the minimal set of information we need to draw the first frame and like try to optimize for that to get really great launch time. And then that kind

218
00:25:12 --> 00:25:30
of trickles through the rest of the app too. As you're scrolling, you're just kind of loading the data that you really need. Great. I'm going to change topics a little bit because there's a question here that I think is really interesting. I'll just read the question, then we can talk about it. The question is, how does iOS 27 prioritize background tasks when the system

219
00:25:30 --> 00:25:54
is under heavy Apple intelligence workloads? I think this question is interesting because I can totally see the concern, which is like, maybe my app does processing with background processing tasks and it's really important that those processing tasks actually happen um maybe my app uh uses app refresh tasks and i don't want those to like go away because the system is doing

220
00:25:54 --> 00:26:12
more work for for other things um maybe i'm looking at terry do you have any thoughts and kind of like how should we as developers kind of like reason about the new world of of more intelligence features on the system when it comes to this stuff yeah i think uh it's a good concern to have definitely i mean it sounds like we're adding all these new things like isn't this going

221
00:26:12 --> 00:26:33
to affect, you know, your apps and background tasks running on the device. I think, you know, in a lot of cases, it's not necessarily going to cause a problem. If you think about, you know, many of the Apple intelligence features, they run on the neural engine, or they're running in private cloud compute. And so if your app is using CPU, while the Apple intelligence workload is

222
00:26:33 --> 00:26:55
using the neural engine, you know, those might be able to run at the same time and not cause any problem. So it kind of depends exactly on what the workload is, and you know, what resources you're actually using. But I think in general, you know, it's important just to make sure that, you know, you configure your background tasks into small chunks, so the system's able to easily like,

223
00:26:56 --> 00:27:17
pause them and resume them when necessary. So that like, if you're doing some really big task, and you have to stop in the middle, and then the next time you have to start the whole thing over again. If you're scheduling it in chunks, then you can kind of still make progress, even though the system might not be running you as frequently as you would hope to. Yeah, I would say from the

224
00:27:17 --> 00:27:39
tooling perspective, we've also added some new things here for the system trace. So I knew you were going to be able to see priorities of your threads. So maybe sometimes you're misassigning priorities on your QoS workloads or in your Swiss concurrency task workflows, and you will be able to see that in instruments, see what tasks preempted your thread.

225
00:27:39 --> 00:27:43
So this gives you more insight to understand the exact problem.

226
00:27:43 --> 00:27:50
I think the general thing to realize is that the system is trying to do its best to run your workloads.

227
00:27:51 --> 00:27:54
Even at Apple, we have a ton of workloads that run in the background.

228
00:27:54 --> 00:27:57
We don't want those to stop making progress either.

229
00:27:57 --> 00:28:03
So we're doing our best to make sure that everything's scheduled automatically for you and giving the best experience for the user.

230
00:28:03 --> 00:28:04
Totally.

231
00:28:04 --> 00:28:05
Cool.

232
00:28:05 --> 00:28:08
This is sort of related, but a little bit different.

233
00:28:09 --> 00:28:14
This is not so much about, I think, background tasks, but kind of doing background work when the app is in the foreground.

234
00:28:15 --> 00:28:20
The question is, to avoid blocking the main thread, I perform expensive tasks on background threads.

235
00:28:21 --> 00:28:24
However, during launch, this method causes a lot of thread hops.

236
00:28:25 --> 00:28:27
And there's kind of two questions here.

237
00:28:27 --> 00:28:32
One is, how expensive is frequent thread hopping as compared to thread blocking?

238
00:28:32 --> 00:28:36
And the second is, is there any better solution overall for performance?

239
00:28:39 --> 00:28:42
We're all looking at each other wondering who wants to talk first.

240
00:28:42 --> 00:28:45
I guess I'll start specifically about the thread hopping.

241
00:28:46 --> 00:28:47
It's certainly a concern.

242
00:28:48 --> 00:28:53
If you're doing it way too frequently, it will add overhead every time it has to switch to a different thread.

243
00:28:53 --> 00:28:58
But in general, if you're not doing it a whole lot, the overhead is pretty much negligible.

244
00:29:00 --> 00:29:07
I don't know of an exact number, but I would say, you know, if you're not doing it like thousands of times per second or something, then it's probably fine.

245
00:29:08 --> 00:29:15
And then, you know, one technique you can use is also if you can wait to do some of that work as well.

246
00:29:15 --> 00:29:18
A lot of that work is not necessarily needed exactly at launch.

247
00:29:18 --> 00:29:20
We just talked about this in one of the previous questions.

248
00:29:21 --> 00:29:28
You know, think about what is the data you need, the minimum amount of data you need to get your app to its loaded state during launch.

249
00:29:28 --> 00:29:33
and then you can kind of, you know, delay everything else until after that has already happened.

250
00:29:34 --> 00:29:41
Also, you know, what Casper mentioned earlier for the background tasks, making sure we're prioritizing things correctly as well, that's really important.

251
00:29:41 --> 00:29:55
So, you know, all those background threads that maybe you're spinning up, as long as they're prioritized, you know, lower than the work that your app is actually doing to load its main content, then it hopefully won't have too much of an impact on, you know, what the user's actually experiencing.

252
00:29:56 --> 00:29:57
Yeah, that's a great point, Terry.

253
00:29:57 --> 00:30:05
I think the lazy deferral of work, the non-critical work, I think that is a key to make sure your launch is really, really optimal.

254
00:30:06 --> 00:30:08
So there's other techniques as well, right?

255
00:30:08 --> 00:30:11
I think there's pre-warming of the application.

256
00:30:11 --> 00:30:21
Like if you have abilities to use BG app refresh tasks to actually fetch the information that's needed during the launch, you're reducing some of that workload when the launch actually happens.

257
00:30:21 --> 00:30:31
So keeping a really close eye on what's really needed and what can be deferred or done earlier is a really good way to minimize those context switches during the app launch.

258
00:30:32 --> 00:30:35
And what would be the approach to kind of see if this is a problem?

259
00:30:36 --> 00:30:41
What's the best way to measure this and see, is this a theoretical problem or is this an actual problem?

260
00:30:42 --> 00:30:49
In instruments, you will see a context switch count being graphed as a histogram, so that can give you an idea on how many context switches are happening.

261
00:30:50 --> 00:30:53
And you can try to later correlate it with your app launch metrics, for example.

262
00:30:53 --> 00:31:00
So maybe you're trying different approaches and one of them context switch count spikes and see how that affects your app launch.

263
00:31:01 --> 00:31:04
But yeah, I definitely agree with the points made that try to defer the work.

264
00:31:04 --> 00:31:12
And I think that sometimes, you know, we see developers trying to fetch kind of like A-B test experiments during launch, things like that.

265
00:31:13 --> 00:31:20
And yeah, I would say maybe you can just cash the last result and change your business logic this way.

266
00:31:20 --> 00:31:23
So try to be creative about this.

267
00:31:24 --> 00:31:36
Yeah, I think you folks can keep me honest on this, But, like, I think the bigger issue that we've seen across apps is not so much spinning off work.

268
00:31:37 --> 00:31:39
I mean, it can be a problem.

269
00:31:39 --> 00:31:40
I'm not saying it's not.

270
00:31:40 --> 00:31:44
But not so much spinning off work that needs to be done on different, like, background cues.

271
00:31:44 --> 00:31:53
But it's often the case where you spin off a whole bunch of work and then you make the main thread wait for all of that to complete before your first frame.

272
00:31:53 --> 00:31:58
Because now the whole app is basically frozen until that background work is complete.

273
00:31:58 --> 00:32:04
So maybe this is like a tangential question.

274
00:32:04 --> 00:32:06
What's the best way to identify that?

275
00:32:06 --> 00:32:16
How do I know if my problem isn't so much that the background work is happening, but it's actually delaying my app launch because of all that work?

276
00:32:16 --> 00:32:20
I mean, if you were actually waiting on that, you would see it in the main thread view in Instruments.

277
00:32:20 --> 00:32:26
So you would use system trace template that allows you to record all of your thread states.

278
00:32:26 --> 00:32:30
And in this case, your thread probably would become blocked.

279
00:32:30 --> 00:32:35
like if someone is actually blocking their main thread.

280
00:32:35 --> 00:32:37
So you would look there.

281
00:32:37 --> 00:32:52
But yeah, I can definitely see, I think a very common case is spawning too much background work during launch, and that is competing, and maybe you have your network request and a couple of other requests happening at the same time, maybe from some framework that you're linking.

282
00:32:52 --> 00:32:56
So really inspecting what's happening and what's on your critical path to launch.

283
00:32:56 --> 00:32:57
- Gotcha.

284
00:32:57 --> 00:32:58
- I think, sorry, go ahead.

285
00:32:58 --> 00:33:07
Sorry, I think that one of the sort of more out of the box sort of like way to think about this is also just if you're not even checking your launch time.

286
00:33:07 --> 00:33:11
I think a good starting point is to even look at your launch time to begin with.

287
00:33:11 --> 00:33:13
Like, what is your launch time?

288
00:33:13 --> 00:33:16
Like, what do you think about like, does that align with what you expect?

289
00:33:16 --> 00:33:22
Like, if you're the user and you're waiting this amount of time for your app to launch, how like, what would you think about that?

290
00:33:22 --> 00:33:30
And then using that sort of metric to compare with different versions of your app to see if that has moved and how, like, is that expected?

291
00:33:30 --> 00:33:34
Or did you make a change that you need to sort of address this issue?

292
00:33:34 --> 00:33:43
Yeah, and then the other thing I wanted to add was if you have already shipped your app out to customers and they're actively using it, you might be able to open up Xcode Organizer.

293
00:33:44 --> 00:33:54
And there's a launch area in there as well, which shows you not only the launch metrics, but also it can show you the top issues of the actual call stacks where users are hitting slow launches.

294
00:33:54 --> 00:34:06
And so in that case, you know, without even profiling with instruments, you'll still get a similar view, and you can see if your app is blocking on other work or just doing too much CPU work during launch.

295
00:34:06 --> 00:34:19
Totally. I actually, just talking about app launch a little bit, I'm remembering a question we got in the labs yesterday at Apple Park around just measuring app launch time.

296
00:34:19 --> 00:34:24
Because I think there's a lot of folks that come up with kind of their own technique.

297
00:34:24 --> 00:34:28
They might be inspecting like kernel APIs to see when the process was created.

298
00:34:28 --> 00:34:32
And they're using that as one of the kind of goalposts for launch.

299
00:34:33 --> 00:34:36
But Metric Kit doesn't do that at all, right?

300
00:34:36 --> 00:34:38
It uses a different technique for actually measuring launch.

301
00:34:38 --> 00:34:40
Do you want to talk a little bit about that?

302
00:34:40 --> 00:34:40
Yeah.

303
00:34:40 --> 00:34:52
Also, we obviously recommend the Apple technologies for folks to measure, launch, and don't try to sort of speculate or inspect and cause actual more overhead in trying to get this information.

304
00:34:53 --> 00:34:57
I think if you use Metricate or the organizer, you would get this information.

305
00:34:57 --> 00:34:58
The system would do that for you.

306
00:34:58 --> 00:35:07
It measures, you know, when the user taps on your app and when your app actually launches where the first sort of like, you know, the first screen is drawn.

307
00:35:07 --> 00:35:13
So all of that is done for you at an efficient way that will not incur extra cost to your app.

308
00:35:13 --> 00:35:15
So definitely recommend using our tools.

309
00:35:16 --> 00:35:18
That's the same thing we use for our own apps, right?

310
00:35:18 --> 00:35:21
When we're measuring launch time for the mail app, for example?

311
00:35:21 --> 00:35:22
Yeah.

312
00:35:22 --> 00:35:33
And I think that what's really cool about that is that it measures something that you can't measure inside your app process, which is when someone actually taps on your app icon on the home screen.

313
00:35:34 --> 00:35:42
Your app is far away from existing yet, and yet Metric Kit and the organizer are able to kind of give you that interval, which is really great.

314
00:35:42 --> 00:35:45
Okay, I'm going to go back to some of the questions we're getting here.

315
00:35:45 --> 00:35:48
This question is kind of going back to battery.

316
00:35:48 --> 00:35:57
I know we touched on this a little bit already, but the question is, besides background tasks, what's the most common silent battery killer in SwiftUI?

317
00:35:57 --> 00:36:02
How much power does view over invalidation actually drain?

318
00:36:02 --> 00:36:05
And what's the best instruments workflow to catch it before release?

319
00:36:07 --> 00:36:10
Yeah, I mean, do you want to say anything about it?

320
00:36:10 --> 00:36:13
I'll hit the SwiftUI side of it, and then you can go for the power stuff.

321
00:36:14 --> 00:36:22
I mean, yeah, view over invalidation can be, like, a very big killer of battery life just because you are just redrawing, doing a lot more work on the CPU.

322
00:36:22 --> 00:36:28
And also it is, like, a very silent thing because, by definition, over invalidation is you are recreating a view that looks exactly the same.

323
00:36:28 --> 00:36:36
So you could be looking at your phone, and, you know, you can see nothing's changing on screen, but in the background, you know, if you're over invalidating, you can have the CPU just churning and churning and churning.

324
00:36:38 --> 00:36:39
Yeah, I completely agree with you.

325
00:36:39 --> 00:36:47
I think for SwiftUI, I would just mainly focus on minimizing the redraws and maybe flattening your UI hierarchy as well.

326
00:36:47 --> 00:36:48
I think that's a big one.

327
00:36:49 --> 00:36:53
These should help minimize your foreground app energy drain quite a lot.

328
00:36:53 --> 00:37:01
And then looking at other work that you're dispatching on the background threads, those could be other aspects that are happening.

329
00:37:01 --> 00:37:22
SwiftUI threads are dispatching workloads in order to get information, fetch data so that they can show it. Keeping a close eye on that is also useful, like caching, you know, using the basic principles of software engineering, cache when possible, you know, minimize repetitive work. I think those are all the things that you should focus on.

330
00:37:22 --> 00:37:44
And I would say as far as our tooling support is concerned, SwiftUI Instruments is a great way to actually focus on those. Gotcha. Just kind of on the topic of SwiftUI, there's a question here, And I know you folks are not the SwiftUI engineers, so maybe this is a question better suited for our SwiftUI group lab, but maybe you folks have thoughts anyway.

331
00:37:45 --> 00:37:51
Are there any performance improvements for very large lists and table views in SwiftUI for macOS 27?

332
00:37:51 --> 00:37:59
What are some best practices for making long SwiftUI lists and tables work well with tens or hundreds or thousands of items?

333
00:38:00 --> 00:38:01
Do you have any thoughts on this, Marco?

334
00:38:01 --> 00:38:02
Yeah.

335
00:38:02 --> 00:38:16
I mean, I think for having like large lists and stuff like that, one of the things is like if you're keeping your list size constant, then SwiftUI can kind of know how to manage it, whereas if you're kind of constantly changing the size of your list, that can change like the height of things.

336
00:38:16 --> 00:38:22
As long as you're keeping things more or less constant, that works pretty well with SwiftUI's model of kind of updating things as needed.

337
00:38:22 --> 00:38:28
And there are some like lazy data structures and stuff that can kind of help you.

338
00:38:28 --> 00:38:34
And then also moving some of that kind of filtering and stuff into the kind of model side of things.

339
00:38:34 --> 00:38:41
So instead of having like an if statement in your forEach that is checking each thing in your list and having to do that calculation.

340
00:38:41 --> 00:38:46
If you do that before you even provide the list to SwiftUI, then SwiftUI doesn't need to kind of chew on that.

341
00:38:46 --> 00:38:50
It can just display what it needs to display and nothing more.

342
00:38:50 --> 00:39:01
- Are there any other kind of sources of hitches that you've seen with very long listen tables apart from just kind of, like you said, batching it in the model?

343
00:39:01 --> 00:39:05
Is there anything else that people should be on the lookout for?

344
00:39:05 --> 00:39:07
I know that's like a broad question.

345
00:39:07 --> 00:39:12
Maybe the answer is no, but-- - It's kind of tough.

346
00:39:12 --> 00:39:24
I mean, I think a lot of the things is like, when people have cells inside of their list that are changing size, it kind of has a cascading effect where if something in the middle of the list changes size, suddenly everything below it has moved.

347
00:39:24 --> 00:39:29
And we have to do recalculations of knowing what would be on screen at this kind of offset in the scroll view.

348
00:39:29 --> 00:39:34
So we kind of have to do more work to figure out like, okay, what state are we in now?

349
00:39:34 --> 00:39:55
Versus keeping things very similar, clean, tidy, constant size, where Swift can kind of utilize its kind of caching capability that it uses behind the scene, I think is very important. Great. And on the kind of similar topic of SwiftUI, this is a fairly advanced SwiftUI question, but the question is, I use AnyView for type erasure

350
00:39:55 --> 00:40:20
in several places. How expensive is it in practice? Is there a concrete threshold, like end views, end redraws, beyond which AnyView becomes a problem, or is avoiding it premature optimization? I'll take this one too. So using AnyView and like kind of using type erasure does add a bit of overhead when you're like creating the view. And I would try to avoid it if you can

351
00:40:20 --> 00:40:38
easily avoid it. However, I do think it's one of those things where you can address it if it becomes a problem. I think trying to kind of bend yourself around not using it, you're kind of missing a return on investment. And if it becomes a problem, you know, you can maybe keep it in mind as something as a, you know, on the roadmap of,

352
00:40:38 --> 00:40:40
you know, maybe this will be a problem.

353
00:40:40 --> 00:40:46
But I think trying to focus on that, I think there are probably time better spent elsewhere.

354
00:40:47 --> 00:40:47
Yeah.

355
00:40:47 --> 00:40:49
Yeah, I think I'm focusing on measurement, not only in SwiftUI.

356
00:40:50 --> 00:41:00
I feel like when I'm working on instruments itself and sometimes I have a need to make something thread safe and add unfair log, for example, I have a perception of like, oh, it's going to be really expensive.

357
00:41:00 --> 00:41:03
But then I add it and it's just so quick.

358
00:41:03 --> 00:41:08
So really base your actions on the measurements.

359
00:41:08 --> 00:41:08
Yeah.

360
00:41:08 --> 00:41:16
I think that's important, too, because we try really hard not to build API that are a rake to step on.

361
00:41:16 --> 00:41:19
Certainly there are patterns that work better than others.

362
00:41:19 --> 00:41:20
I'm not saying otherwise.

363
00:41:20 --> 00:41:31
But for example, I'm thinking about-- there's an accessibility session this year that talks about using any layout to switch between a horizontal layout or a vertical layout, depending on if you have larger text enabled.

364
00:41:31 --> 00:41:32
That's totally fine.

365
00:41:32 --> 00:41:36
You can measure it, but that's probably not going to have this huge performance impact.

366
00:41:37 --> 00:41:41
But you could totally go overboard and do something that might have impact.

367
00:41:41 --> 00:41:42
So I totally agree.

368
00:41:42 --> 00:41:44
Measurement is kind of the best place to start.

369
00:41:45 --> 00:41:45
Yeah.

370
00:41:45 --> 00:41:47
And the features are there for a reason, right?

371
00:41:47 --> 00:41:47
Yeah.

372
00:41:47 --> 00:41:49
If they are more expensive, sure.

373
00:41:50 --> 00:41:53
You might need to take that cost, and that's fine in a lot of cases.

374
00:41:53 --> 00:41:54
Yeah.

375
00:41:54 --> 00:41:55
Great.

376
00:41:56 --> 00:41:57
I'm going to move on.

377
00:41:57 --> 00:42:00
This is kind of switching gears back to Metric Kit and state reporting.

378
00:42:01 --> 00:42:06
The question is, Metric Kit state reporting and crash report extensions are new and noteworthy this year.

379
00:42:07 --> 00:42:13
Are there any other headlining developer tools that didn't make the updates page on the developer site worth looking at?

380
00:42:14 --> 00:42:20
I know we talked a little bit about some of our favorite things this year, but anything come to mind that maybe we haven't talked about yet?

381
00:42:21 --> 00:42:25
Yeah, I think there's a lot going on with Xcode Organizer.

382
00:42:25 --> 00:42:28
So it does provide field data like Metric Kit.

383
00:42:28 --> 00:42:35
It is a different population that is aggregated across all of your users.

384
00:42:35 --> 00:42:41
It does provide like a visualization, but it has less flexibility in terms of how you want to aggregate data compared to Metric Kit.

385
00:42:42 --> 00:42:50
So some of the very exciting things, because it is aggregating across different apps, we do have improvements in metric goals.

386
00:42:50 --> 00:43:01
So developers can actually get a bit more guidance in terms of how you're performing compared to apps that are comparable to your app.

387
00:43:01 --> 00:43:05
So this helps with forming a baseline, which is a very hard thing.

388
00:43:06 --> 00:43:07
If you're just given a number, how am I supposed to know?

389
00:43:08 --> 00:43:10
Is it like doing OK? Should it be better?

390
00:43:11 --> 00:43:21
So I think that is a very helpful way for developers to if you're just starting out and you haven't been measuring out of the box, you kind of already have a guidance line there.

391
00:43:21 --> 00:43:30
I think this is so neat because I think one of the questions we get in the labs all the time is folks open up their Xcode organizer and show it to us and say, I'm a video app.

392
00:43:30 --> 00:43:31
I play video all day long.

393
00:43:31 --> 00:43:34
My power is through the roof, I think, maybe, or is this fine?

394
00:43:34 --> 00:43:35
I don't know, right?

395
00:43:35 --> 00:43:38
Like, what's a video app supposed to use in terms of power?

396
00:43:38 --> 00:43:39
So now you get that.

397
00:43:39 --> 00:43:46
Like, you can actually see, you know, compared to similar apps, what's their performance look like and kind of make decisions based on your stuff.

398
00:43:46 --> 00:43:47
Exactly.

399
00:43:47 --> 00:43:51
Any other kind of tools and thoughts on the new stuff?

400
00:43:51 --> 00:43:55
- Yeah, I think I'd like, I didn't talk about things like foundation models.

401
00:43:55 --> 00:44:02
So for example, foundation models has a new updated tool and instruments, and this year it's extending.

402
00:44:02 --> 00:44:09
So last year we've had just some basic metrics on the tokens, on the token counts.

403
00:44:09 --> 00:44:18
Right now it's a fully debugging tool that allows you to inspect all of your requests, prompts, understand how many tokens are being cashed.

404
00:44:18 --> 00:44:25
So if you want to look into the performance side of things, that's going to be a great tool for that.

405
00:44:25 --> 00:44:33
There is also a tool that's shipped in 26.4 for profiling long-term metal apps.

406
00:44:33 --> 00:44:35
So we actually didn't talk about it.

407
00:44:35 --> 00:44:37
There's a dedicated session.

408
00:44:37 --> 00:44:56
But this tool allows you to record performance of your game over hours and understand FPS metrics, understand the outliers and pairing it, for example, with the state reporting would allow you to maybe see maybe you were dropping frames in a specific level and inspect some of these workflows.

409
00:44:56 --> 00:45:00
So I would recommend Sessions for checking these workflows out.

410
00:45:00 --> 00:45:02
MARK MANDEL: Awesome.

411
00:45:02 --> 00:45:03
What else?

412
00:45:03 --> 00:45:15
I think the other thing that I would point people to is there's a post on the developer forums that is, I believe, in the metric kit tag that talks about kind of all the new stuff.

413
00:45:15 --> 00:45:20
So if you haven't been taking active notes throughout the group lab, you can check out that forum post.

414
00:45:20 --> 00:45:24
It will redirect you to all the new stuff that we have for Perf and Power this year.

415
00:45:25 --> 00:45:27
Great.

416
00:45:27 --> 00:45:28
I guess here's a question.

417
00:45:28 --> 00:45:43
I guess if, you know, knowing the new tools that are out there, if there's kind of like one thing you think, you know, you would encourage developers to like go check out for their app as kind of like a homework assignment from all the new stuff that they can do this year.

418
00:45:43 --> 00:45:50
What's, like, for you the one big thing that you want folks to give it a shot before they wrap up their WWDC work?

419
00:45:51 --> 00:45:53
I can take that one.

420
00:45:53 --> 00:45:54
Go for it.

421
00:45:54 --> 00:46:01
I think definitely looking at metric goals is a big one, right, because now you're going to see the metric goals and compare your metrics, how they're doing with it.

422
00:46:02 --> 00:46:15
So similar applications and having a sense of how similar applications are doing against your metric is a great way to understand what areas you need to invest in, whether it's power, foreground energy, launches, hitches, hangs.

423
00:46:16 --> 00:46:18
So that would be one big point.

424
00:46:19 --> 00:46:22
And then the second one I'm going to give a shout-out to is the state reporting.

425
00:46:23 --> 00:46:32
I think it really allows you to slice the data and figure out in what application states are your metrics doing poorly.

426
00:46:33 --> 00:46:42
So if you're using a combination of metric-at-metrics along with state reporting, I think you can zoom in on the specific areas in your application that need attention.

427
00:46:43 --> 00:46:45
So, yeah, those two things.

428
00:46:45 --> 00:46:46
Awesome.

429
00:46:47 --> 00:46:50
I'm going to take a question that's a little bit different.

430
00:46:50 --> 00:46:58
So the question reads, Swift performance is something I'm genuinely passionate about and want to go deep on.

431
00:46:58 --> 00:47:04
Beyond WWDC sessions and Swift evolution proposals, what does the pathway look like?

432
00:47:04 --> 00:47:13
Specific code bases to study, compiler internals, books, benchmarks for building real deep expertise in this area?

433
00:47:13 --> 00:47:23
I think when it comes to understanding not just Swift and SwiftUI, but any kind of technology, I think it's just experience is kind of king here.

434
00:47:23 --> 00:47:32
And I think that given all the views and perspectives that Instruments tools provides, there's so many different templates like the Time Profiler, SwiftUI, Instrument, and all that stuff.

435
00:47:32 --> 00:47:44
That just building things out and looking at them from that point of view and seeing what's actually happening and going on can kind of give you a better grasp and mental model on what these frameworks are doing behind the scenes.

436
00:47:44 --> 00:47:59
So I think just trying things out, opening it up in instruments, trying out different templates and just seeing what it looks like when you do certain things can be a really good way to just kind of really immerse yourself, basically immersion, right?

437
00:47:59 --> 00:48:00
Immersive learning.

438
00:48:00 --> 00:48:21
Yeah, that's a great point, and I think that from the resources that Apple provides, we have had optimized CPU performance with instruments last year, and that was a fantastic talk that went a bit deeper, and it covered not only time profiler, but also CPU profiler, processor trace, so that you can get the full overview of the suite for CPU usage.

439
00:48:22 --> 00:48:28
And there's also Apple Silicon Optimization Guide that allows you to understand how really things work under the hood.

440
00:48:29 --> 00:48:31
So developers can check that out.

441
00:48:31 --> 00:48:33
Awesome. Anything to add?

442
00:48:34 --> 00:48:37
Yeah, I was just going to echo the Apple Silicon Optimization Guide.

443
00:48:38 --> 00:48:44
I think one really important part is really understanding how our CPUs and our hardware works.

444
00:48:44 --> 00:48:54
If you have a really great understanding of that, then you can really take advantage of actually implementing Swift algorithms and things like that, which will perform really well on Apple hardware.

445
00:48:54 --> 00:48:56
- That's great.

446
00:48:56 --> 00:49:02
Okay, this kind of goes back to SwiftUI a little bit, but let's take a look at this question.

447
00:49:02 --> 00:49:20
So the question is, so this person attended one of our performance events in October and learned to avoid kind of escaping closure-based parameters because closures are hard to compare and that makes SwiftUI do more view invalidations.

448
00:49:20 --> 00:49:29
The question is, it's been limiting because they're having to pass view builder content itself, whereas sometimes passing in an escaping parameter is essential.

449
00:49:30 --> 00:49:43
This also might be a question better suited for the SwiftUI group lab, but any thoughts on kind of like, you know, how to architect SwiftUI in, you know, trying to avoid escaping closures that could cause performance problems?

450
00:49:44 --> 00:49:53
So I think, like, calling the closure in your init rather than, like, in the body of the view so that it doesn't just run every time the view is reevaluated can be a good thing.

451
00:49:54 --> 00:50:08
I don't know a ton about this area, but then I think in general also, like, if you're finding that closures are causing a performance issue, like, maybe there's another API or a different kind of, like, structure that you can use to kind of get the same functionality without the same kind of performance hit of using a closure.

452
00:50:08 --> 00:50:12
But yeah, I'd highly recommend the SwiftUI group lab.

453
00:50:12 --> 00:50:14
I think tomorrow at 9 a.m. is?

454
00:50:14 --> 00:50:15
I think that's right.

455
00:50:16 --> 00:50:17
It's tomorrow.

456
00:50:17 --> 00:50:17
You can check the schedule.

457
00:50:19 --> 00:50:19
Awesome.

458
00:50:19 --> 00:50:20
Anything to add?

459
00:50:21 --> 00:50:22
All right.

460
00:50:23 --> 00:50:24
Okay.

461
00:50:25 --> 00:50:28
This might be for both Marco and Kunal.

462
00:50:29 --> 00:50:34
The question is, this is a beginner developer, a mom of nine, former nurse getting back into tech.

463
00:50:35 --> 00:50:48
When handling dynamic data or heavy assets in SwiftUI, What are the best practices for ensuring view updates don't cause performance stutters like hitches or high CPU spikes on older devices?

464
00:50:50 --> 00:50:58
I mean, I think for stuff like heavy assets, I think there is something to be said about like kind of tailoring what assets you're using depending on the use case.

465
00:50:58 --> 00:51:03
For example, you don't need like a 2000 by 2000 pixel image if it's just going to be a thumbnail.

466
00:51:04 --> 00:51:10
So I think making sure that the assets you're using are properly suited for the use case can be very important.

467
00:51:11 --> 00:51:14
Do you have thoughts on CPU spikes?

468
00:51:15 --> 00:51:21
How do you kind of detect when those things happen and if they're bad and if you need to do something about it?

469
00:51:22 --> 00:51:33
Yeah, I would say assets and in general, loading data, especially during launch times or when you're showing a new page, initializing a view.

470
00:51:34 --> 00:51:42
Paying attention to not just what Marco said, which is like minimizing the resolution or having the most lean weight version of that asset is very useful.

471
00:51:43 --> 00:51:45
But then there's another thing, right?

472
00:51:45 --> 00:51:45
Caching.

473
00:51:45 --> 00:51:46
I think caching is a good one.

474
00:51:46 --> 00:52:01
Once you've actually shown that asset, just making sure that you have a cache that can be accessed again and again would really help with minimizing some of the CPU workloads that can happen by either processing or parsing the network call that fetched the data.

475
00:52:02 --> 00:52:18
So being mindful of making sure that the assets that you're sharing are lean, as well as that you're not refetching them or recomputing aspects of that asset can be very useful.

476
00:52:18 --> 00:52:35
Something else to add, actually, is that oftentimes, if this asset is not load-bearing in your application, then you can do something like put a placeholder image up or a loading placeholder thing so that way you don't have to load the image on the main thread and the rest of your app can kind of continue on and do its work

477
00:52:35 --> 00:52:41
while the image gets loaded in the background, I think can also be very good for kind of-- - Perceived performance.

478
00:52:41 --> 00:52:45
- Yeah, yeah, perceived performance and kind of that responsiveness feel.

479
00:52:45 --> 00:52:50
- Yeah, what about the aspect of this question that's around like older devices?

480
00:52:50 --> 00:53:04
Like if you have a newer device and you're an independent developer and you have a newer device and you wanna make sure performance is good on older devices but you don't have one, like any thoughts on how you would kind of get a feel for if you're having this problem there?

481
00:53:04 --> 00:53:10
I think one really easy thing you can try, even if you have a newer device, is just turn on low power mode.

482
00:53:10 --> 00:53:13
Because that makes the CPUs run a lot slower to save battery.

483
00:53:13 --> 00:53:18
But it also means you might encounter some issues that you wouldn't normally see if you weren't in low power mode.

484
00:53:18 --> 00:53:22
And those might be similar issues that you would encounter on an older device.

485
00:53:22 --> 00:53:25
And it's also just a great way to generally optimize your app.

486
00:53:25 --> 00:53:27
Lots of users love low power mode.

487
00:53:27 --> 00:53:29
And so they're going to have it enabled.

488
00:53:29 --> 00:53:34
And if your app doesn't feel great in that mode, then you should definitely look into optimizing that.

489
00:53:34 --> 00:53:37
- Yeah, and there are also condition inducers that I mentioned.

490
00:53:37 --> 00:53:47
I would also say that we see developers profiling on simulators, and yeah, there's no difference depending on which simulator you use on what performance do you get because it all runs on your Mac.

491
00:53:47 --> 00:53:54
So that's one thing that is, so I would recommend just always use a physical device.

492
00:53:54 --> 00:53:55
- Physical device.

493
00:53:55 --> 00:53:56
- For profiling specifically.

494
00:53:56 --> 00:54:17
- I think another note is because physical devices are sometimes harder to get as a developer, tools like, you know, like Metricit and Organizer will allow you to get field data, which you would have a bigger data set from your users. So if you naturally have users that have different types of devices, different conditions, those would be really good ways for you to get information on

495
00:54:17 --> 00:54:31
your CPU usage on a larger data set in real environments. I want to talk about this. You mentioned the condition inducers a couple times. Can you just say more about what that is? Like, How does it induce a thermal condition?

496
00:54:32 --> 00:54:33
Does it make the device hot?

497
00:54:33 --> 00:54:34
Like, what's going on?

498
00:54:35 --> 00:54:35
No, not really.

499
00:54:35 --> 00:54:38
That just artificially induces that.

500
00:54:38 --> 00:54:40
And that's a feature that is part of Xcode.

501
00:54:40 --> 00:54:43
So developers are able to start a debug session.

502
00:54:43 --> 00:54:48
Or I believe it might be, I'm not sure, it might be in the new device hub.

503
00:54:49 --> 00:54:52
They are able to put their device in an artificially state.

504
00:54:52 --> 00:54:56
And they could limit things like network, speed, and other variables as well.

505
00:54:56 --> 00:54:57
Gotcha.

506
00:54:57 --> 00:55:00
So it is reducing, like, CPU and other things.

507
00:55:00 --> 00:55:03
It's just, it's simulating what would happen if the device were warm.

508
00:55:03 --> 00:55:04
Got it.

509
00:55:05 --> 00:55:05
Okay.

510
00:55:05 --> 00:55:10
Well, since we're talking about power, I have another question here that's kind of along the same lines.

511
00:55:11 --> 00:55:18
The question is, our app heavily relies on ARKit, Metal, and other compute-intensive processing.

512
00:55:18 --> 00:55:27
Since it is primarily used outdoors in direct sunlight, devices frequently experience elevated thermal states.

513
00:55:27 --> 00:55:33
What are the recommended strategies for managing thermal pressure and preserving a good user experience?

514
00:55:34 --> 00:55:39
Yeah, I sympathize with an app that's used in direct sunlight all the time.

515
00:55:39 --> 00:55:41
Yeah, it's a challenge.

516
00:55:41 --> 00:55:50
Yeah, one of the things I would definitely advise is there is an API out there that indicates when you're running into the higher thermal states.

517
00:55:50 --> 00:55:53
So make sure your application is listening for that.

518
00:55:53 --> 00:55:55
That's the process info to thermal state.

519
00:55:56 --> 00:56:00
Go ahead and make sure your application is able to listen to higher thermal states.

520
00:56:00 --> 00:56:03
And based on that, back off on the experience, right?

521
00:56:03 --> 00:56:10
I think if there's often experiences can be designed in a way that they don't have to be rich all the time.

522
00:56:10 --> 00:56:15
You can back off of some of the richer experiences without significantly compromising the end user experience.

523
00:56:15 --> 00:56:20
So looking at those thermal states is a good way to, you know, avoid such situations.

524
00:56:21 --> 00:56:21
Yeah.

525
00:56:22 --> 00:56:25
Do you have examples of like what you could back off?

526
00:56:25 --> 00:56:33
Like, is anything, like, maybe non-obvious that someone could do to back off the work in thermal pressure?

527
00:56:33 --> 00:56:36
Yeah, I think this is, like, pretty case-by-case, right?

528
00:56:36 --> 00:56:37
It would really depend.

529
00:56:37 --> 00:56:48
Like, for example, let's say you had heavy assets that you were downloading from the network that, you know, result in heavy parsing times, right?

530
00:56:48 --> 00:56:51
And, like, a lot of decoding that's happening on your device.

531
00:56:51 --> 00:57:04
Now, one of the things you could do is in your heavier thermal state, you could actually make those HTTP requests and, like, request for more lighter resources from the network so that you're not going to be spending a lot of processing time on your device.

532
00:57:04 --> 00:57:15
There's other things you could do, like, if you have heavier animations or more richer, smoother transitions, you could, like, back off on those and have more simpler navigations or simpler animations.

533
00:57:16 --> 00:57:18
It's pretty case-by-case, actually.

534
00:57:18 --> 00:57:20
I think that the frame rate thing is actually pretty cool, too, right?

535
00:57:20 --> 00:57:29
I think we've seen that strategy in a lot of cases where you just reduce the frame rate until you exit that pressure condition.

536
00:57:29 --> 00:57:31
Yes, that one definitely helps.

537
00:57:31 --> 00:57:41
And often the system can also, like in heavier thermal states, the system also puts in certain mechanisms to minimize animation frame rates as well as display frame rates.

538
00:57:41 --> 00:57:46
So there's some benefit you get just by default, and then you can add on top of those.

539
00:57:46 --> 00:57:49
Any other thoughts on kind of thermal conditions?

540
00:57:50 --> 00:57:55
Yeah, I mean, just to, like, yeah, frame rate, kind of backing off on frame rate, stuff like backing off on resolution.

541
00:57:55 --> 00:58:01
So if normally you're running at a high resolution, you get a thermal state indicator, that might be a good time to kind of lower the resolution.

542
00:58:01 --> 00:58:04
You know, pushing less data around, less pixels, it's going to be less work.

543
00:58:05 --> 00:58:12
And ultimately, at the end of the day, you're just, your app is in a use case where you kind of have to keep performance in mind and you just have to optimize your app in that case.

544
00:58:13 --> 00:58:15
There's no getting around thermodynamics.

545
00:58:15 --> 00:58:24
Well, and I think that's the interesting thing about this problem is that like there's a part of this that you control, which is your app and the compute that your app needs and the power draw that your app needs.

546
00:58:25 --> 00:58:26
There's a part of this you don't control.

547
00:58:26 --> 00:58:31
Like ultimately, you know, devices can't run at 100 degrees Celsius.

548
00:58:31 --> 00:58:32
It doesn't work.

549
00:58:34 --> 00:58:44
So there's an aspect to this which is like optimize the heck out of the part that you do control and that'll give the best possible chance that someone in those conditions are going to be able to use the app.

550
00:58:46 --> 00:58:46
Great.

551
00:58:46 --> 00:58:49
I think we have time for maybe one more question.

552
00:58:49 --> 00:58:55
I'm just going to peruse here a little bit.

553
00:58:55 --> 00:59:04
Let's look at-- I think we talked about the environment briefly, so maybe this will be somewhat quick.

554
00:59:04 --> 00:59:12
But the question is, does using a lot of @environment properties in a large SwiftUI app have any performance impact?

555
00:59:12 --> 00:59:21
For example, if many values are injected into the environment across a deep view hierarchy, does that affect rendering or update performance in any way?

556
00:59:21 --> 00:59:27
- So for the environment, the biggest thing for performance and kind of all that stuff is environment churn.

557
00:59:27 --> 00:59:33
So if you're just putting values in the environment and reading them out of your views, it's not that bad, it's not that expensive.

558
00:59:33 --> 00:59:42
It's when you start kind of like updating those in a high frequency and causing environment churn because other views that are reading from the environment now have to reevaluate, like has something in my environment changed?

559
00:59:42 --> 00:59:50
So putting things in the environment is not necessarily that expensive, but it's when you start kind of like really churning the environment that that's when you start to run into issues.

560
00:59:51 --> 00:59:51
Gotcha.

561
00:59:51 --> 00:59:57
And of course, you can see this in the variety of instruments we talked about, like the SwiftUI instrument today.

562
00:59:57 --> 00:59:57
Absolutely.

563
00:59:57 --> 00:59:58
That's great.

564
00:59:58 --> 01:00:02
Well, I think that's about all the time we have for this group lab.

565
01:00:02 --> 01:00:06
We're so thankful that you joined us today and hope that you found today's conversation helpful.

566
01:00:07 --> 01:00:11
Thank you to all of our panelists, as well as the folks behind the scenes working hard to make today happen.

567
01:00:12 --> 01:00:25
If we didn't get to your questions, please visit the Apple Developer Forums at developer.apple.com slash forums, including the power and performance Q&A, which is taking place on the forums on Thursday at 10 a.m. Pacific.

568
01:00:26 --> 01:00:32
And of course, if you have any feedback or bugs, file them at feedbackassistant.apple.com.

569
01:00:33 --> 01:00:44
And you can also try out the new generative AI search experience at developer.apple.com to get answers about frameworks, design, accounts, and everything related to development on our platforms.

570
01:00:45 --> 01:00:51
And speaking of feedback, you'll receive an email with a survey link to let us know about your experience at WWDC.

571
01:00:51 --> 01:00:54
We would love to incorporate your feedback in future events.

572
01:00:54 --> 01:00:58
Thank you so much for joining us today, and have a great WWDC.
