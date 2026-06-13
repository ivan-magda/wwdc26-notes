---
title: Coding Intelligence for Beginners Group Lab
source: https://developer.apple.com/videos/play/wwdc2026/8007/
session: 8007
collection: wwdc2026
duration: 01:01:54
fetched: 2026-06-12
via: whisper.cpp ggml-large-v3 (session SD video audio)
transcription: machine-generated, no speaker diarization
---

> Machine transcription of the **Coding Intelligence for Beginners Group Lab** SD video via whisper.cpp (`ggml-large-v3`).
> Group labs ship no Apple transcript; this is generated from the audio. Cues are timestamped segments, **not** speaker-attributed turns.

## Transcript

1
00:00:03 --> 00:00:06
Hello, and welcome to the Coding Intelligence for Beginners Group Lab.

2
00:00:07 --> 00:00:24
My name is Angelica, I'm part of the Worldwide Developer Relations team, and it is my privilege to be hosting today's Group Lab because I am joined by some great panelists, some that you might be familiar with from a certain platform state of the union, including someone who had an incredible wardrobe with Xcode themes.

3
00:00:24 --> 00:00:25
Thank you, I didn't wear it today.

4
00:00:26 --> 00:00:28
Unfortunately, I really wanted that neon noir one.

5
00:00:28 --> 00:00:30
And also, Monsieur Fantastique himself.

6
00:00:31 --> 00:00:31
Fantastique.

7
00:00:32 --> 00:00:35
We are all so excited to answer your questions today.

8
00:00:35 --> 00:00:40
There are quite a few of you joining us today, so there's a chance we won't get to every question.

9
00:00:40 --> 00:00:44
We're going to be focusing on the most popular questions and questions that benefit the broader audience.

10
00:00:45 --> 00:00:48
If you don't get your questions answered today, don't fret.

11
00:00:48 --> 00:00:52
We can continue the conversation in developer.apple.com slash forums.

12
00:00:53 --> 00:01:01
And of course, don't forget about feedbackassistant.apple.com where you can submit code-level questions, bug reports, and enhancement requests.

13
00:01:01 --> 00:01:03
All right, that's enough from me.

14
00:01:03 --> 00:01:07
Let's get started with a round of introductions starting from across the table.

15
00:01:07 --> 00:01:08
Who are you and what do you do here at Apple?

16
00:01:08 --> 00:01:19
- Yeah, I'm Nathan and I manage one of the, one of a few different Xcode intelligence teams, specifically one that deals a lot with the interactions with the agents and the models we deal with.

17
00:01:19 --> 00:01:25
- I'm Ken, in addition to doing fun costume changes and so too, I lead the Xcode team.

18
00:01:26 --> 00:01:28
- Hi, good morning everyone, I'm Jerome.

19
00:01:28 --> 00:01:33
I'm the product manager for developer tools and also a pins collector.

20
00:01:33 --> 00:01:34
- Hey everyone, I'm Kevin.

21
00:01:34 --> 00:01:38
I'm one of the senior engineering managers for Xcode, focused on our AI tools.

22
00:01:39 --> 00:01:39
Awesome.

23
00:01:39 --> 00:01:45
Well, this is Coding Intelligence for Beginners, after all, so I'm going to start with a general question for all four of you.

24
00:01:46 --> 00:01:57
Coding Intelligence, working with agents, it's a pretty big shift in terms of the way developers work, and some folks are at different levels of familiarity with agents or working with agents.

25
00:01:57 --> 00:02:03
How would you say development has changed, especially app development?

26
00:02:03 --> 00:02:05
How has it changed since the introduction of agents?

27
00:02:05 --> 00:02:08
Maybe Jerome or Ken, you have thoughts about this.

28
00:02:08 --> 00:02:26
- I mean, I think from my perspective, what you get to do a little bit more tangibly now is you get to work with these agents to articulate some of your ideas and make those a little bit more real, something you can see.

29
00:02:26 --> 00:02:32
Something I personally do is I work with the agent to create a spec for what I'm building, right?

30
00:02:32 --> 00:02:39
So it kind of creates this document that says this captures my intent or it doesn't.

31
00:02:39 --> 00:02:43
And if it doesn't, then I can iterate with it and then I can see it over time.

32
00:02:43 --> 00:02:47
I can, you know, modify it over time.

33
00:02:47 --> 00:02:50
And so that's one big change.

34
00:02:50 --> 00:02:52
Like all of those things were happening before.

35
00:02:52 --> 00:02:56
People were doing those, but they just weren't maybe quite as codified.

36
00:02:56 --> 00:02:58
So I think that's a big one.

37
00:02:58 --> 00:02:58
Yeah.

38
00:02:58 --> 00:03:04
I mean, you know, from the very beginning of computer science, we tried to make coding easier, right?

39
00:03:05 --> 00:03:09
And, like, you know, in the beginning, you just type, you know, one character at a time, you know, and we had a mistake.

40
00:03:09 --> 00:03:11
You had to find exactly where it was.

41
00:03:11 --> 00:03:21
So we added code completion and, you know, code completion with models and then this chat experience that allows you to generate code in certain files.

42
00:03:21 --> 00:03:42
But, like, this agentic flow, you know, that can verify it's on work, fix its own errors, and actually work until something is done, that's a much bigger shift that helps you realize your idea much faster and actually keeps you very creative because you can really focus on making those ideas a reality with the help of all these intake tools.

43
00:03:43 --> 00:03:47
I would say it goes beyond just the idea of getting to the idea that you already have in your mind.

44
00:03:48 --> 00:03:52
I think what I love is that you can actually test your idea to see if it's good.

45
00:03:53 --> 00:03:56
I find most of the time my initial idea is probably wrong.

46
00:03:57 --> 00:04:04
And before I would, like, stare at this, like, complex path before me and be like, I just don't know if it's worth it to go down that path with agentic coding.

47
00:04:05 --> 00:04:07
It's so easy to explore all those different paths.

48
00:04:07 --> 00:04:18
Yeah, there's a lot sort of shifts in your mind when you're exploring a problem space and the cost of writing new code and deleting old code is so fundamentally changed.

49
00:04:18 --> 00:04:26
You feel much more emboldened to try things that you might not try before and to really do something for your user that you wouldn't have thought possible.

50
00:04:27 --> 00:04:28
I think that's very, very cool.

51
00:04:28 --> 00:04:34
That's the coolest thing, really, is that it shifts this cost around a little and makes it possible for you to try big things.

52
00:04:34 --> 00:04:50
One of my favorite things that I think that it has introduced is the introduction of some of the best practices that a lot of folks may delay or put later in their roadmap, like how introducing testing has actually helped with agent workflows.

53
00:04:51 --> 00:04:56
So, like, building your test suite actually benefits every prompt that you have with the agent moving forward.

54
00:04:57 --> 00:05:03
Or introducing the translation with agents and localizing your apps.

55
00:05:03 --> 00:05:07
And these type of things, it benefits you as a developer because it makes it easier for you to do.

56
00:05:07 --> 00:05:15
But the incredible benefit to your users, to the people who are using your apps, is, you know, immeasurable, I think.

57
00:05:15 --> 00:05:20
So, that's, I think, what was the most exciting about the introduction of agents, especially in app development.

58
00:05:20 --> 00:05:21
Totally.

59
00:05:21 --> 00:05:24
All right, we do have some great questions from some developers.

60
00:05:24 --> 00:05:28
The first one we have from Pichaya TriYS.

61
00:05:28 --> 00:05:30
I really hope I pronounced that correctly.

62
00:05:31 --> 00:05:36
Where is the best place to learn all the commands like slash plan and more?

63
00:05:37 --> 00:05:41
Yeah, so when you type slash, Xcode has collected from the agent.

64
00:05:41 --> 00:05:50
If the agent happens to provide a list back to us, the compatible commands that it knows about, and then it'll also show you any of the skills that are loaded up from Xcode, things like that.

65
00:05:51 --> 00:05:53
You can also ask the agent, right?

66
00:05:53 --> 00:05:55
You can ask the agent for help with this stuff.

67
00:05:55 --> 00:05:58
The agent is aware of almost every command that can be executed and what it will do.

68
00:05:59 --> 00:06:16
I would suggest experimenting with both of those things and seeing what you can learn from that and try some of them and see what they show you because you may be really pleasantly surprised about a whole new workflow that either we provide or the agent provides already out of the box that really changes the way you work with these.

69
00:06:16 --> 00:06:21
I think ask the agent is probably something that, you know, will be an answer for many of the questions today, you know?

70
00:06:23 --> 00:06:25
Definitely a good tactic to ask the agent.

71
00:06:26 --> 00:06:27
All right.

72
00:06:27 --> 00:06:30
We have another question from our developer, Jason Chung.

73
00:06:31 --> 00:06:44
As more developers adopt agent-first workflows, what unique advantage does Xcode's built-in coding intelligence provide over external AI agents, and how does Apple see the future of relationships between AI and Xcode?

74
00:06:45 --> 00:06:50
Well, maybe the future question might be a little bit tougher since we don't talk about future plans.

75
00:06:50 --> 00:06:57
But I'm very curious if you all have insights on, you know, what's unique, what's a unique advantage of using Xcode for agent workflows?

76
00:06:58 --> 00:06:59
Yeah, Kevin, why don't you?

77
00:06:59 --> 00:07:00
Yeah, yeah, I'll start with that one.

78
00:07:01 --> 00:07:02
I think a couple things that come to mind immediately.

79
00:07:03 --> 00:07:07
One is we've spent a lot of time thinking about our tool set that we provide.

80
00:07:08 --> 00:07:11
We've spent a lot of time talking about the knowledge that we provide and also the project understanding.

81
00:07:12 --> 00:07:19
So if we look at, we've really tried to get the agent to understand how your project is laid out, what does that mean for developing on Apple platforms.

82
00:07:20 --> 00:07:25
Then there's a lot of great tools that surface information about the SDK and the APIs.

83
00:07:26 --> 00:07:30
We have our documentation tools that allow the agent to explore what are the new APIs.

84
00:07:31 --> 00:07:33
It's always getting the most relevant information.

85
00:07:34 --> 00:07:36
And then there's tools for validation.

86
00:07:36 --> 00:07:42
And I think this is what gets me really excited is how do we help the agent actually check its work as it goes along?

87
00:07:42 --> 00:07:44
So we have tools that you'd expect, like building.

88
00:07:45 --> 00:07:49
We have a couple different kinds of building tools because we're also trying to make it really fast.

89
00:07:49 --> 00:07:54
So we have both build the whole thing, but we also have little builds that can run.

90
00:07:54 --> 00:07:58
We also have tools for validating the execution of code, which is I think really exciting.

91
00:07:58 --> 00:08:07
We can do both the execution of little snippets of code all the way up through actually rendering your UI and interacting with it and testing it.

92
00:08:08 --> 00:08:16
So those are a couple of the different kind of tool sets and knowledge that we kind of provide for the agent to use inside of Xcode.

93
00:08:16 --> 00:08:24
- Yeah, and then I think there's other things that sort of go beyond that into the Apple ecosystem that give you access to data about your apps.

94
00:08:25 --> 00:08:40
So your crash data, best practices with localization, and there's a lot of Apple knowledge baked into those to help surface up things to you, to make it a lot easier to utilize that.

95
00:08:41 --> 00:08:48
Yeah, Xcode has years and years of experiences of building apps for our platforms, and it comes with unique features.

96
00:08:48 --> 00:08:56
you know, the preview system, for example, which is unique to Xcode, allowing you to actually visualize all your changes.

97
00:08:56 --> 00:09:01
And now, you know, we're giving the preview system to the agent so that it can visualize its work, right?

98
00:09:01 --> 00:09:09
And we build those very nice crafted experience within Xcode, you know, to annotate things, text, but also previews, right?

99
00:09:09 --> 00:09:18
So not only, you know, you have all the tools available, but the experience itself, you know, feels very, you know, Apple, very well crafted.

100
00:09:18 --> 00:09:24
So that it's welcoming for newcomers that maybe are using agentic for the first time.

101
00:09:24 --> 00:09:32
They will feel at home using an Apple product, but also very powerful for all the people that want to do very complex things.

102
00:09:32 --> 00:09:34
And maybe they're working in Excel for 20 years.

103
00:09:34 --> 00:09:38
They also need to be welcomed with a new agentic workflows.

104
00:09:38 --> 00:09:53
The other thing that we offer is there's an onboarding point when you're just getting started with the agents where it's very overwhelming, because you're looking at sort of all the different things you can do with these, and you just send an English language sentence to an agent, it will come back with an answer, and it'll look like it's done a great job.

105
00:09:54 --> 00:09:56
And it probably has done a pretty good job, actually.

106
00:09:56 --> 00:10:03
But we have a lot of experience working with agents now and understanding how to onboard into these agents really effectively.

107
00:10:04 --> 00:10:07
So we offer out-of-the-box guidance on a really good starting point.

108
00:10:07 --> 00:10:13
We have a really good planning mode that really helps you get the most done with the agent's built-in planning mode, and we add a lot of great stuff on top of it.

109
00:10:14 --> 00:10:19
We have a really good base set of tools, and you can extend both of those things with plug-ins now, of course.

110
00:10:20 --> 00:10:28
But it means that as you get started, you already have sort of a running start that someone outside Xcode might not have in terms of learning how to use an agent really effectively.

111
00:10:28 --> 00:10:37
Great. All right. Moving on to a very highly upvoted question from Harry3.

112
00:10:39 --> 00:10:47
Does the Xcode 27 UI have any indication of the amount of agentic resources being consumed while it is carrying out your request?

113
00:10:48 --> 00:11:07
Yes. So your agent provider will have a slash command of some kind that will give you access to that information. I don't want to enumerate every single one of the different ones because there's so many. I will point out that there's often two. There's one that tells you what the current state of the primary context window, the central coordinating agent that's handling the

114
00:11:07 --> 00:11:30
transaction, how full that is. And then there's also going to be one that tells you how much things have costed so far or how much you've used. These are often two different numbers and it can be easy to mislead yourself by looking at the wrong one in terms of am I, you know, am I gonna regret spending, you know, $20 on this session or whatever might be a different answer than am I

115
00:11:30 --> 00:11:48
getting close to the point at which the agent's going to start compressing down its context and I have to wait a minute or so. I think maybe a clarifying point here is that we don't have that information in the UI. That's something that you ask, to Jerome's point earlier, you ask the agent for that information. You ask the agent for that information, yeah. If there's more information

116
00:11:48 --> 00:11:51
like that that you'd like to have better surfaced, that's a great time to file a feedback.

117
00:11:51 --> 00:11:52
Yeah, absolutely.

118
00:11:52 --> 00:11:54
Because this is the kind of workflow stuff we're really interested in fixing for people.

119
00:11:55 --> 00:12:06
Yeah, if there's a particular use case or perhaps a suggestion for where would you like it to be surfaced outside of asking the agent, file feedback at feedbackassistant.apple.com.

120
00:12:07 --> 00:12:12
All right, let's move on to another question from a developer, Juto Art.

121
00:12:13 --> 00:12:21
If the Xcode AI agent needs to read or search implementations from other projects, is there a way to add additional working directories to its context?

122
00:12:22 --> 00:12:27
For example, could we let the agent access another local project when it needs cross-project references?

123
00:12:27 --> 00:12:31
This is a great question about security and permissions that the agents have.

124
00:12:31 --> 00:12:33
Nathan, do you want to kick it off, Nathan?

125
00:12:33 --> 00:12:34
Yeah.

126
00:12:35 --> 00:12:43
So we do sort of have a few different modes of security that you can operate in in Xcode with agents, right?

127
00:12:43 --> 00:12:52
By default, out of the box, we will use permission prompting to sort of make sure that the agent can get to the things it needs to get to but doesn't have free reign.

128
00:12:53 --> 00:13:10
If you find that the permission prompting is getting in the way of being able to let the agent do a lot of hard work, the other thing that we offer, in addition to allow always, is a mode that you can turn on that is still sort of an early preview of this functionality, but this managed security mode that lets you instead sort of gate

129
00:13:10 --> 00:13:15
on the agent's actions themselves at sort of the file system level and govern what happens.

130
00:13:15 --> 00:13:22
In terms of handling references, if it's referenced in your Xcode workspace, the agent can get to it and can know about it.

131
00:13:22 --> 00:13:33
If it's referenced outside your Xcode workspace, it's something on disk somewhere that you want the agent to be aware of, all the time I'm pasting in file paths or I'm saying, oh, it's in my documents directory and I'm letting the agent go explore and find it.

132
00:13:34 --> 00:13:38
It's very good at just figuring out the distinction between these things and getting the access it needs.

133
00:13:38 --> 00:13:43
That's a great point about configuring your Xcode environment for what you're working on.

134
00:13:43 --> 00:13:46
Xcode workspaces are a great way to bring in multiple projects.

135
00:13:47 --> 00:13:50
You can kind of mix and match based on what you're working on at the moment.

136
00:13:50 --> 00:14:12
configure your workspace and then we automatically handle the agent being able to see all those pieces yeah super important is you're always in control like you will you will see those um tool calls and and the agent would ask you permission to uh enable a certain you know some of those tools can actually you know move your files or delete your files so we always ask you if you allow those

137
00:14:12 --> 00:14:23
tools um within your project yeah but there's i mean there's great it's easy to to uh give access to things where you always want that.

138
00:14:23 --> 00:14:31
Like if you're like me, you take a lot of screenshots, drop them on your desktop, you want the model to have access to that so you can reference those and talk about those.

139
00:14:31 --> 00:14:32
Really easy to do that.

140
00:14:32 --> 00:14:36
But you always get to make the choices.

141
00:14:36 --> 00:14:38
Yeah.

142
00:14:38 --> 00:14:39
Absolutely.

143
00:14:39 --> 00:14:40
Okay.

144
00:14:40 --> 00:14:46
As Nathan mentioned, there is an experimental feature in the settings, I believe.

145
00:14:46 --> 00:14:52
You were mentioning in Xcode settings if you wanted to give it permissions to take certain actions.

146
00:14:52 --> 00:15:03
When you're copying, moving, deleting things in other directories, of course, we want to make sure that you are in control and know exactly what the agent's going to be doing since it is pretty non-deterministic most of the time.

147
00:15:04 --> 00:15:04
All right.

148
00:15:05 --> 00:15:05
Great.

149
00:15:05 --> 00:15:08
Let's move on to a question from Tammy Santana.

150
00:15:10 --> 00:15:13
Coding intelligence builds features but doesn't explain them.

151
00:15:14 --> 00:15:17
A beginner finishes a session with working code that they don't understand.

152
00:15:18 --> 00:15:20
They can't debug it, maintain it, or extend it.

153
00:15:21 --> 00:15:27
Please add an optional explain what I just did and why summary.

154
00:15:27 --> 00:15:29
This might be more of a feature request.

155
00:15:30 --> 00:15:35
Please add an optional explain what I just did and why summary after each completed task.

156
00:15:36 --> 00:15:38
For people who want to learn, not just ship.

157
00:15:38 --> 00:15:40
That's a great point.

158
00:15:41 --> 00:15:44
I think there's a couple things to do.

159
00:15:44 --> 00:15:47
One is asking the agent questions is such a great idea.

160
00:15:47 --> 00:15:48
It's a great way to do it.

161
00:15:48 --> 00:15:52
They love to talk, so it's great to be able to ask the agent, like, why did you just do that?

162
00:15:52 --> 00:15:55
And it's happy to give you in kind of the form that you want.

163
00:15:55 --> 00:15:59
Like, I love asking for, like, HTML reports or markdown files or just, like, tell me why you did this.

164
00:15:59 --> 00:16:00
Help me understand.

165
00:16:01 --> 00:16:08
And you can kind of get, like, what's cool about agents, too, is you can configure them to kind of do your common things, like, out of the box.

166
00:16:08 --> 00:16:16
So if you have, like, an agents MD in your project, you can configure it to say, after I run a prompt, just always give me some explanation.

167
00:16:16 --> 00:16:18
Here's the format I want it in.

168
00:16:18 --> 00:16:19
Here's how I want you to do it.

169
00:16:19 --> 00:16:25
And now, suddenly, you've just basically built yourself a feature inside of Xcode, which I think is just really fun and powerful.

170
00:16:25 --> 00:16:35
It's almost like you're creating a skill for yourself, which is every time I ask for something, I want you to create documentation for it or create a summary of what you just did.

171
00:16:36 --> 00:16:40
The way I'm using it, though, is I'm creating two conversations, right?

172
00:16:40 --> 00:16:43
In Xcode 27, you can have many conversations in parallel.

173
00:16:44 --> 00:16:50
And so while the agent's working, I actually ask questions on the code that's being generated or a question of a new API that it's using, right?

174
00:16:50 --> 00:16:53
So you build and learn at the same time, which is really cool.

175
00:16:54 --> 00:17:02
Yeah, and I think this kind of goes back to the sort of spec-driven development that I was talking about where, you know, you can bake it into what you're asking for.

176
00:17:02 --> 00:17:09
So you can upfront ask, you know, like, let's start by actually, you know, you tell me all the assumptions you're going to make.

177
00:17:09 --> 00:17:13
Maybe tell me how you're going to implement it and capture that in something that you always have.

178
00:17:14 --> 00:17:18
I find that to be, you know, A good way to kind of split the difference there.

179
00:17:18 --> 00:17:18
Yeah.

180
00:17:19 --> 00:17:23
Another strategy I use all the time is I will write the shape of the code.

181
00:17:24 --> 00:17:26
I will declare the struct where the work will happen.

182
00:17:26 --> 00:17:27
I will add some of the properties.

183
00:17:27 --> 00:17:29
I'll put a comment saying, and the other's there.

184
00:17:29 --> 00:17:33
And then I will go over and I will declare the protocol that something else conforms to.

185
00:17:33 --> 00:17:37
And I will add a comment somewhere else that says, and here's where I think we're going to have to call this stuff.

186
00:17:37 --> 00:17:46
And then I'll open up a chat with an agent and I will say, all right, let's do this work together to put this thing together, to thread these pieces together.

187
00:17:46 --> 00:17:49
and the agent can help fill in the gaps.

188
00:17:49 --> 00:17:52
That can be a very, very powerful technique as you're sort of working through solving these problems.

189
00:17:52 --> 00:18:03
And I found, especially in apps that I'm just bringing up for myself, I can define a lot of the structure that way very quickly without having to get as hands-on as I used to.

190
00:18:03 --> 00:18:05
- That's a great point about constraints.

191
00:18:05 --> 00:18:08
There's different fidelities of constraints you can give it.

192
00:18:08 --> 00:18:12
Sometimes I'll give it natural language constraints, like please do this thing for me.

193
00:18:12 --> 00:18:16
Sometimes I'll give it a source file that's like fill in these things for me.

194
00:18:16 --> 00:18:17
And sometimes you can combine it.

195
00:18:17 --> 00:18:20
So like I'm not sure how to build this part, so I'll do it in natural language.

196
00:18:20 --> 00:18:25
This part, I really care that it's a protocol that has these kind of requirements on it.

197
00:18:25 --> 00:18:29
And then you bring those together, and the agent does a great job of blending those.

198
00:18:29 --> 00:18:30
- That can help also with the understanding.

199
00:18:30 --> 00:18:35
You can kind of start with what you understand, and then bring in the parts you don't.

200
00:18:35 --> 00:18:43
- I was thinking too, like we talk about plan mode as a way to be able to understand before implementing any code, before anything gets changed in your project.

201
00:18:43 --> 00:18:47
But plan mode's also a really great way for you to understand how it approaches a problem.

202
00:18:47 --> 00:19:02
And so, rather than just saying that, you know, agents are just shipping features that I don't understand, that plan mode is actually there to be able to, for you to be able to make changes and be the conductor of the work that the agent actually has to build.

203
00:19:02 --> 00:19:10
So, I would say that there's a lot of opportunities to learn or learn about what you're building as you're going along with the agent.

204
00:19:10 --> 00:19:12
Again, asking questions is a great way.

205
00:19:12 --> 00:19:20
Building architecture documents that you then leave in your project is also an added benefit because that means the agent can use that in the future when you're building more features.

206
00:19:20 --> 00:19:24
And that means you also get to use that as resources or your teammate gets to use that as resources.

207
00:19:24 --> 00:19:26
So there are lots of ways you could use agents.

208
00:19:26 --> 00:19:31
It doesn't necessarily mean it has to go off and do its work on its own.

209
00:19:31 --> 00:19:35
There's a lot of control that's built into Xcode for you to be able to participate.

210
00:19:35 --> 00:19:50
- The other thing is there's always this sort of tension that you feel as you're working on this stuff of like I told the agent to go off and build this part of the calculator, but when I display the tip, I wrote this part, but now I feel very alien from that calculator stuff.

211
00:19:50 --> 00:19:51
How do I fix this?

212
00:19:51 --> 00:20:06
One of the things I've found that is a very fun way to sort of do this stuff is I will even, sometimes if it makes a big change for me, I will ask it to host a little game show for me and ask trivia questions with its question asking tools about how the code is put together.

213
00:20:06 --> 00:20:11
And then I have to answer the multiple choice questions and I get to play a little game.

214
00:20:11 --> 00:20:14
And it teaches me about the code that I now own.

215
00:20:14 --> 00:20:16
If you got it wrong, it'd stop working, right?

216
00:20:18 --> 00:20:20
That's a great point about the different ways that we all learn.

217
00:20:21 --> 00:20:23
Like, I'm a visual learner, so I love diagrams.

218
00:20:23 --> 00:20:27
So, like, I always have to make diagrams because it's, like, it makes so much more sense to me.

219
00:20:28 --> 00:20:39
I think that's why my first thought was architecture documents because I have to be able to look at the way data flows or how these features are built so that I can validate that that's actually how I want it to work.

220
00:20:40 --> 00:20:58
And I think that's the part that working in Xcode specifically, being able to understand the context of the project, what's happening with Xcode, details from, you know, like the organizer data or crash data, you could actually be a little bit more thoughtful about what you do.

221
00:20:58 --> 00:21:01
And debugging is still your responsibility.

222
00:21:01 --> 00:21:06
You could ask it to debug things, but you still have to figure out exactly if those solutions are correct.

223
00:21:06 --> 00:21:15
And so there's a lot of control that you, And again, a lot of collaboration that you can do with agents rather than necessarily leaving it to chance.

224
00:21:17 --> 00:21:20
All right, moving on to another question from Florentine F.

225
00:21:20 --> 00:21:26
What are some common mistakes the coding assistant makes that beginners would find pretty hard to catch on their own?

226
00:21:27 --> 00:21:27
It's a great question.

227
00:21:28 --> 00:21:34
So there are, of course, whole classes of bugs that these coding agents will commit just like humans do.

228
00:21:35 --> 00:21:43
The most subtle thing a coding agent can do is not understand the assumptions that you were making when you asked it to do the work.

229
00:21:44 --> 00:21:46
And this takes many different forms.

230
00:21:47 --> 00:21:54
It could be that you asked for one thing and it gives you something completely different, but it can also be that you asked for one thing and it gives you an unmaintainable version of that thing.

231
00:21:55 --> 00:21:58
You know, there are whole sort of categories of these failure modes.

232
00:21:58 --> 00:22:03
It also can often not discover absolutely everything it needs to.

233
00:22:03 --> 00:22:16
In Xcode, we do a lot of work to make sure that agents can find what they need as quickly as possible and learn from it as quickly as possible. But these are fallible tools in a way that a compiler is generally not. So you have to watch out for those things.

234
00:22:17 --> 00:22:27
I think the variant that I am often the most sort of nervous about is what I would call like cheating, where an agent has quietly gone through a bunch of work and its job is to finish the job.

235
00:22:28 --> 00:22:36
Sometimes it'll look at the work that has been done and it'll go, well, I haven't made that unit test pass, but that's okay. It doesn't pass because I'm in a debug configuration right now.

236
00:22:36 --> 00:22:56
it'll pass in production um you know if if you wouldn't accept that answer from a co-worker don't accept it from a machine uh and uh uh you know you have to watch out for that stuff and keep on top of it but you can also teach it over time what your expectations are especially with things like tests and strong types in your project that get caught by the compiler and live issues

237
00:22:56 --> 00:23:16
the agent gets feedback on that stuff fast so that it can iterate and see its wrong work before it gets to you because by the time it gets to you it's going to become much more challenging to catch everything that's happened yeah and i think that's something that we've we we spend a lot of time doing is working on the tools for validation particularly that's why we have so many tools for

238
00:23:16 --> 00:23:34
validation so i i'd say experiment with some of the different tools that are in the xcode suite to kind of figure out like what works for your project what's important like different workflows and share them with us we'd love to we'd love to i think i would love to hear you know what what developers are doing i think that's that's the my my first thought process is um i mean this

239
00:23:34 --> 00:23:56
happens with a lot of our features in Xcode, Swift language, when it's finally available to a larger audience, you know, we all sit here thinking about how agent workflows are going to be, or like how we think it would be best done with Xcode's context and the tools that we have available. But the way they use those tools are always surprising to us sometimes, you know, like

240
00:23:56 --> 00:24:15
not always, sometimes, surprising, where you're choosing a different workflow or your agent workflow is a little bit different than we expect. And of course, that's where feedback assistance is incredibly valuable. If you have an enhancement request or a tool that you want to work in a certain way, reach out to us and let us know.

241
00:24:17 --> 00:24:24
All right. Let's take a look at another highly upvoted question from Tammy Santana.

242
00:24:26 --> 00:24:30
This might be another feature request that might be great for us.

243
00:24:30 --> 00:24:31
It's my favorite kind of feature request.

244
00:24:32 --> 00:24:37
So, Tammy Santana is saying, there is no undo button.

245
00:24:37 --> 00:24:40
Xcode Coding Intelligence needs an undo agent changes button.

246
00:24:41 --> 00:24:47
When the agent edits 10 files across my project, my only rollback option is git, which beginners, not all beginners, know.

247
00:24:48 --> 00:24:53
One button to revert everything the agent did would save hours of damage from bad suggestions.

248
00:24:53 --> 00:24:56
Well, actually, you can tell the agent.

249
00:24:57 --> 00:25:00
actually, you know, this doesn't work for me, come back to the previous state.

250
00:25:00 --> 00:25:03
And they will do that usually pretty well, right?

251
00:25:03 --> 00:25:09
So he knows how to undo the work that he's just done because he has a whole context, so he can always roll back to the previous state.

252
00:25:10 --> 00:25:12
But that's a good feature request.

253
00:25:12 --> 00:25:18
The other reason that I was joking that this is my favorite kind of feature request is because it's an already implemented feature request.

254
00:25:19 --> 00:25:22
We have actually this history thing that is kept track of.

255
00:25:22 --> 00:25:39
As long as you have initialized a Git repository For the agent to work within in your project We do track states across each turn with the agent and you can open that up and move the slider up one level and hit Revert and it will go back to the way that it was right before the agent took his last turn, right?

256
00:25:39 --> 00:26:02
So there's like different levels of things you can use there's you know, obviously get is the largest tool of the largest time window I guess you could say then Xcode has this, you know history feature for you know Kind of the very granular the last action that you took and drum you're saying ask the agent The agent's also very good at understanding kind of what it did, which is also nice if there's parallel work that you're doing or there's multiple threads.

257
00:26:02 --> 00:26:07
You can be like, well, undo this part, but keep this part over here, and it will kind of merge those together.

258
00:26:08 --> 00:26:08
Yeah.

259
00:26:08 --> 00:26:13
I mean, I found the agent does a fantastic job reasoning about its own work, especially over time.

260
00:26:14 --> 00:26:29
So as you're making commits or having it make commits, and you realize something from yesterday isn't quite right or isn't panning out, It does a good job when you point that out, and so it does fantastic.

261
00:26:29 --> 00:26:35
I do want to call out also, Tammy has a great point here, which is the only rollback option is tied to Git.

262
00:26:36 --> 00:26:42
So both the history, if you have a Git repository set up with your project, that's the best way to be able to use that feature.

263
00:26:42 --> 00:26:43
You could ask the agent to go back.

264
00:26:44 --> 00:26:48
What I want to call out is regardless if you're using an agent, using Git is incredibly valuable.

265
00:26:48 --> 00:26:49
Please use Git.

266
00:26:49 --> 00:26:52
There's a reason that box is ticked by default when you hit new project.

267
00:26:52 --> 00:26:59
When you start new projects, you could set up a Git repository with your project so that you could actually do these changes.

268
00:26:59 --> 00:27:06
I would say that even if you're developing features on your own, I just want to say this because I've encountered a lot of developers who are solo developers.

269
00:27:06 --> 00:27:11
They think that Git is related to working in the team where you have to do pull requests and things like that.

270
00:27:11 --> 00:27:22
But being able to revert issues like bugs that you introduce in your app and you release it already and you have to revert very quickly and be able to release a new one really quickly.

271
00:27:22 --> 00:27:23
Git is your best friend.

272
00:27:23 --> 00:27:24
Yeah.

273
00:27:24 --> 00:27:25
Definitely take a look at that.

274
00:27:25 --> 00:27:28
I have a ton of little side projects.

275
00:27:29 --> 00:27:31
They're just me, and I always turn Git on.

276
00:27:32 --> 00:27:32
Put them on Git.

277
00:27:32 --> 00:27:34
I mean, there's no downside to it, right?

278
00:27:34 --> 00:27:35
Yeah, exactly.

279
00:27:36 --> 00:27:36
All right.

280
00:27:36 --> 00:27:42
And it's really healthy for an agent, too, to be able to look back and get history and see an archive of why changes were made.

281
00:27:43 --> 00:27:52
All the time, I will be able to trace back in one of my apps a bug that was created because of a bad assumption that happened when I was working on something unrelated.

282
00:27:53 --> 00:28:11
And and that's that's only possible if you have a tool like it at stores the changes and the explanations It's alongside each other. Yeah, I guess it's a pro tip to is like the commit message is really important Yeah, and the more you document assumptions in there, so it doesn't have to re-evaluate what the assumptions are by looking at the git diff

283
00:28:11 --> 00:28:36
I was like, you don't have git commits like me where it just says WIP like fixing habits i got out of because agents didn't like them yeah yeah i was this goes back to a point i had earlier which is i feel like what agents has has introduced to a lot of folks is because it has because by introducing things like good good development practices like git or

284
00:28:36 --> 00:28:59
testing or any of those tools that you typically wouldn't necessarily do as a solo developer once you have that context the agent actually works better because it can make a lot of assumptions about how you work and what your project history is. And those prompts that you have or whatever plans that you try to have them generate, it will have better assumptions to start

285
00:28:59 --> 00:29:26
and that first prompt will be much better than it typically would. All right, great. Let's go into another developer question from UJ. I have years of iOS development experience, but little experience with AI coding assistance. As more engineering roles expect AI assisted development skills, How would Apple recommend using Xcode's coding intelligence or other AI tools to develop the skills needed to meet industry expectations?

286
00:29:26 --> 00:29:28
How much time do we have today?

287
00:29:29 --> 00:29:31
Who wants to start?

288
00:29:31 --> 00:29:34
I mean, I would say just start simple.

289
00:29:34 --> 00:29:38
Like, you don't need to start with some massive project.

290
00:29:38 --> 00:29:56
I mean, what I would do is work it into the things you're already doing, find small changes you want to make, small bugs you want to fix, maybe that you don't want to spend the time doing it, and have the agent do that for you where you can reason about it really easily.

291
00:29:56 --> 00:30:04
I think as you do that, you'll start to find what works really well and what doesn't.

292
00:30:04 --> 00:30:12
And so that sort of iterative process, you'll become more – you'll be more confident in making bigger changes.

293
00:30:12 --> 00:30:14
So it's sort of a natural cycle, I think.

294
00:30:14 --> 00:30:16
Building the confidence is something that is important.

295
00:30:17 --> 00:30:26
So I would start with tasks you already know how to do, right, and so that you can very quickly compare the result of the agent work to what you would have done yourself.

296
00:30:26 --> 00:30:28
And like, you know, build that confidence.

297
00:30:28 --> 00:30:30
Oh, wow, this is actually working really great.

298
00:30:30 --> 00:30:36
And, you know, and slowly but surely expand, you know, to all the capabilities of this agentic flow.

299
00:30:37 --> 00:30:42
But, yeah, I mean, try it is really the first, you know, great advice we can say because it's really fantastic.

300
00:30:43 --> 00:30:48
That's a great point about, like, bringing in your experience to working with agents is invaluable.

301
00:30:48 --> 00:30:50
So if you have, like, lots of industry experience, like, that's huge.

302
00:30:50 --> 00:30:52
It makes such a big difference.

303
00:30:52 --> 00:31:00
And, like, to Drum's point, like, some of the first times, sometimes you use an agent, you might, as an experienced developer, look at it and be like, that's not what I would have done.

304
00:31:01 --> 00:31:03
But what's great is they love to learn.

305
00:31:03 --> 00:31:03
Yes.

306
00:31:03 --> 00:31:18
And you can direct them and guide them, and they're, like, you know, indefatigable about, like, all the advice that you can give them, all the guidance that you can give them to kind of help shape the way that the kind of code you want written, the architectures you want, the validation loops that you want.

307
00:31:18 --> 00:31:20
And they'll just keep doing it and keep learning.

308
00:31:21 --> 00:31:24
And I think that's, again, that experience is invaluable.

309
00:31:24 --> 00:31:29
Yeah, you can teach the agent, you know, that experience you have, right, to make you even more comfortable.

310
00:31:30 --> 00:31:35
You can tell them, hey, this is the way I work, right, and I want you to help me, you know, keeping that work the same.

311
00:31:38 --> 00:31:40
There's no one way to use these tools either.

312
00:31:40 --> 00:31:47
There are many people who use them just as a very powerful find tool to help them create the plan of action for what they do.

313
00:31:47 --> 00:31:50
That's a totally valuable way to use these things.

314
00:31:50 --> 00:31:58
If you're looking at this stuff and you go, I'm not sure I like this because it's surrendering a kind of control I'm not comfortable with surrendering at this point, then you don't have to.

315
00:31:59 --> 00:32:03
Just use it as a planning aid and see where that gets you.

316
00:32:03 --> 00:32:05
And if you like that, experiment with some other stuff over time.

317
00:32:05 --> 00:32:07
If you don't, no harm.

318
00:32:08 --> 00:32:17
I think that that's one of the coolest things here is that this is adaptive in a way that other technology traditionally hasn't been, and it can really become whatever you need it to be.

319
00:32:18 --> 00:32:26
- I think the thing that a lot of, a big misconception I think when working with agents is that you're handing all the control over.

320
00:32:26 --> 00:32:30
Actually it's really important with agents, as you were talking about, Kevin, that you don't disconnect.

321
00:32:30 --> 00:32:34
In fact, you should be reviewing all your changes.

322
00:32:34 --> 00:32:41
You should be working with the agent and using plan mode to be able to determine that its implementation details are correct.

323
00:32:41 --> 00:33:04
I wanna highly recommend a session that came out this year called Xcode Agents and You for workflow ideas it talks a lot about exploring or exploring a project or exploring new features as a way to use agents as well as building refining or orchestrating multiple sub-agents to do something like translation all of them are really great ways to use it and as Nathan said

324
00:33:04 --> 00:33:25
you don't have to do all of them you just choose which works best for your workflow one more thing I would say is I think there's both a kind of a way to come at it from the top and a way to come that from the bottom what i mean by that is i i find that working on just engineering problems in general sometimes starting with a low fidelity like a really coarse grain look at a problem is

325
00:33:25 --> 00:33:44
really helpful um like i had a colleague who'd always ask me write your header files first and that just let like let me think about the shape before i actually wrote any lines of code so you can start with the agent just like give me the shape like let's just talk about your swift interface you know equivalent or you can kind of go from the other way where you're like i know the

326
00:33:44 --> 00:33:50
shape that I want because of all my experience, and I know I want protocols here, and these classes here, et cetera.

327
00:33:50 --> 00:33:58
And then you can have the agent kind of work in the-- it has so much context at that point of what the function is and how it fits in with the whole scheme.

328
00:33:58 --> 00:34:01
It can just implement the body, and you can review that.

329
00:34:01 --> 00:34:05
So either top-down or bottom-up is two different ways to approach it.

330
00:34:05 --> 00:34:07
Absolutely.

331
00:34:07 --> 00:34:09
All right.

332
00:34:09 --> 00:34:10
Hope that answers your question, UJ.

333
00:34:10 --> 00:34:14
I know we probably could spend another 20 minutes talking about this.

334
00:34:14 --> 00:34:16
We do have several other questions.

335
00:34:16 --> 00:34:20
We have one from @pichayatryys again.

336
00:34:20 --> 00:34:24
What are the key differences between agent mode and chat mode?

337
00:34:24 --> 00:34:40
I successfully connect to Xcode to the local LLM with MLX LLM server in chat mode following example from WWDC 26 session, but the result is much different from the coding intelligence demo from our other WWDC 26 session.

338
00:34:40 --> 00:34:41
Yes, it will be.

339
00:34:41 --> 00:34:43
So this is absolutely true.

340
00:34:43 --> 00:34:56
So the difference between that chat area in the settings And the agents area that we sort of floated up to the top is really capability because in that chat mode, we do give the agent a few tools to call to do things on your code base.

341
00:34:57 --> 00:35:00
But it's on the order of 10 to 15 things that it can do.

342
00:35:00 --> 00:35:05
In the agent mode, the number of things that an agent can do is basically infinite.

343
00:35:05 --> 00:35:07
It can do anything it wants if you let it, right?

344
00:35:07 --> 00:35:09
It can use command line tools.

345
00:35:09 --> 00:35:12
It can use almost 60 tools inside Xcode now.

346
00:35:13 --> 00:35:16
We give it all sorts of capabilities there that it wouldn't have otherwise.

347
00:35:16 --> 00:35:32
And these agent wrappers that sit around the model and give it other superpowers are super important as well because they let it manage its context and they let it spawn other agents and all of these things that as you get more experience working with them, you'll find more and more useful.

348
00:35:33 --> 00:35:42
A couple of the agent integrations that are already in Xcode sitting in the UI are open source agent integrations that actually encourage you to try using with open models.

349
00:35:42 --> 00:35:48
and they have documentation on their websites or in their GitHubs that explain how to configure it.

350
00:35:48 --> 00:35:58
In the Xcode settings, you can select the agent name and then go to the dot, dot, dot, and it says set up with configuration file and then it'll point you to the configuration file.

351
00:35:58 --> 00:35:59
You can just paste in the example from the documentation.

352
00:36:00 --> 00:36:06
You can also use ACP mode, which is any agent, basically, that you can find information about.

353
00:36:06 --> 00:36:15
You can set up four local agents as well, one of those, and some of those are designed specifically around being really, really great with these local agents.

354
00:36:15 --> 00:36:22
So, I mean, the capability of the agents is just so much more.

355
00:36:23 --> 00:36:39
You'd be a great person, Nathan, to tell us like the difference in scope of like code completion, like predictive code completion, chat, agentic, like what's happening at each of those, like the time horizon that they work on goes way up.

356
00:36:39 --> 00:36:40
And so the capability goes up.

357
00:36:40 --> 00:36:54
This is my favorite question, actually, because this is something that has really been staggering to me because I remember when the predictive code completion stuff was starting and we were working on that, I remember thinking to myself, this could be kind of a big deal.

358
00:36:54 --> 00:37:02
I way underestimated how big this was going to be because when we did that, we were saving you, you know, maybe two, three, five seconds at a time.

359
00:37:02 --> 00:37:03
Pretty good.

360
00:37:03 --> 00:37:11
But then when we added this chat stuff, that became, it can go for 30 seconds and find an answer for me, and that's the right answer, and make some code changes.

361
00:37:12 --> 00:37:24
And a year ago when we started experimenting with this agent stuff, even six months ago when we started adding the agent integration you're now used to in Xcode, it was a half hour that you could get out of an agent sometimes.

362
00:37:24 --> 00:37:32
And now it's an hour, an hour and a half, even more sometimes that an agent can work independently doing a lot of work for you so that you can get to an ultimate conclusion.

363
00:37:32 --> 00:37:34
It doesn't mean it has to, but it can.

364
00:37:34 --> 00:37:36
And that's just not possible with that chat feature.

365
00:37:36 --> 00:37:38
That's not really what it was aimed at doing for people.

366
00:37:38 --> 00:37:41
So you're going to get a very different experience when you use these agent modes.

367
00:37:42 --> 00:37:45
We really want everyone moving from chat to agents.

368
00:37:45 --> 00:37:47
And now is a great time to do that with Xcode 27.

369
00:37:47 --> 00:37:50
Because if you want 30-second mode, it can still give you 30-second mode.

370
00:37:50 --> 00:37:51
It does all the time.

371
00:37:51 --> 00:37:53
Agents can do the small things too.

372
00:37:53 --> 00:37:53
Exactly.

373
00:37:55 --> 00:37:55
All right.

374
00:37:56 --> 00:37:59
We have, I'll say, two questions.

375
00:37:59 --> 00:38:01
They're related to each other, both highly voted.

376
00:38:02 --> 00:38:04
The first one is from Interferon.

377
00:38:04 --> 00:38:08
"I prefer to use local models instead of cloud models.

378
00:38:08 --> 00:38:13
"If I use a local model with Xcode "instead of a cloud model, what features do I miss?

379
00:38:13 --> 00:38:15
"Will I just get slower?

380
00:38:15 --> 00:38:18
"Will it just get slower or maybe less accurate answers?

381
00:38:18 --> 00:38:26
"Or are there whole agentic features "that are not possible with local models?"

382
00:38:26 --> 00:38:27
- Well, there's two things, right?

383
00:38:27 --> 00:38:32
There are the agents that you run and the model that it talks to, right?

384
00:38:32 --> 00:38:37
And somehow you can mix and match, I wanna say, right?

385
00:38:38 --> 00:38:49
So from the very beginning, I think in Xcode, we added the capability to talk to local, and local being, you know, on your own machine or on a local server, right?

386
00:38:49 --> 00:38:57
If you have a bigger Mac, for example, you know, you can use tools to run the model here and plug Xcode basically to talk to those models.

387
00:38:58 --> 00:39:04
So if you're just using the model, the chat mode with the local model, well, you will get the chat experience.

388
00:39:04 --> 00:39:07
Now, those local models are becoming really good, right?

389
00:39:07 --> 00:39:09
And so we get probably great results.

390
00:39:09 --> 00:39:12
But there will still be chat mode results.

391
00:39:12 --> 00:39:14
It's called that way, right?

392
00:39:14 --> 00:39:21
Now, if you plug an agent in their open source local agent that you can use, plug them to local model, you can totally do that.

393
00:39:21 --> 00:39:29
And you will get a lot of those agentic tools using the local agents and the local models right here.

394
00:39:29 --> 00:39:37
So essentially, if I were to TLDR that, because of ACP, it sounds like the tool set that Xcode provides.

395
00:39:37 --> 00:39:50
So all of the tools that are provided for our Cloud Codex and Gemini agents, if you are using ACP, all of those same tool sets, that same tool set will be available to a local agent.

396
00:39:50 --> 00:39:50
Yeah, absolutely.

397
00:39:50 --> 00:39:51
The same tool set will.

398
00:39:51 --> 00:40:07
The biggest difference is, of course, if a commercial model provider is going to give you a model that is so big it has to be hosted on a really powerful GPU in its own special cluster that they have somewhere, you are not going to get the same super genius on your MacBook Air.

399
00:40:08 --> 00:40:32
You know it's great, but you're not and that's okay. Actually you can get a lot done with this stuff now The things that you can do with our computers are mind-boggling to me It just means that you have to give it more guardrails make sure that those validation steps that we were talking about before are really Pristine because those are going to be the things that it's able to keep itself on track with when it's not quite as smart

400
00:40:32 --> 00:40:40
Yeah, and as a follow-up question, we got a question from Pachaya TriYS around the same thing.

401
00:40:40 --> 00:40:53
Is it possible to connect the coding intelligence to a local LLM like the one hosting an MLXLM.server command and an agent not in the chat mode so that it would be more capable to handle stuff in Xcode without pointing file to file?

402
00:40:54 --> 00:40:59
And as we just said, ACP support, that's basically how you want to approach it.

403
00:40:59 --> 00:41:05
And there are many open-source tools and wrappers available with local model support like OpenCode.

404
00:41:05 --> 00:41:10
And I'm really excited for ACP also to be used to help other enterprises as you have your own configurations.

405
00:41:10 --> 00:41:18
Regardless of what harness you talk to on the back end, ACP is a great way to get all those inside of Xcode and working for your enterprise.

406
00:41:18 --> 00:41:26
All right. We're going to move on to a question from antscrashing. Great username.

407
00:41:26 --> 00:41:35
what can you tell me about the privacy of the code of my app when it is accessed through a third-party LLM via Xcode?

408
00:41:35 --> 00:41:39
Will the LLM be able to train on my code base and queries?

409
00:41:39 --> 00:41:42
Will it be able to store data short-term or long-term?

410
00:41:42 --> 00:41:43
This is a great question.

411
00:41:44 --> 00:41:44
Jerome.

412
00:41:44 --> 00:41:44
Yeah.

413
00:41:45 --> 00:41:57
Well, so, you know, when you enable a coding agent and a coding model with one of the partners we have, you will see their, you know, terms and condition or how they use your code.

414
00:41:57 --> 00:42:06
And you're always in control of, you know, choosing if you allow the agent provider to use your code for training or not, right?

415
00:42:06 --> 00:42:19
It's very clear here, you know, and the way to do that is going to your account and, you know, just in the settings of the model providers tells what you can and cannot do with the code you provided.

416
00:42:19 --> 00:42:22
And it's the same for, you know, Chad and Agentic here.

417
00:42:23 --> 00:42:27
So you're always in control of what the model provider can do with your data.

418
00:42:27 --> 00:42:32
That's really important from the beginning when we start creating this feature, right?

419
00:42:32 --> 00:42:34
- And agents in Xcode don't, Apple doesn't see any of that.

420
00:42:34 --> 00:42:35
- Yeah.

421
00:42:35 --> 00:42:37
- It's all through the model provider that you use.

422
00:42:37 --> 00:42:40
- Yeah, there's a direct call to the agent provider.

423
00:42:40 --> 00:42:41
- Exactly, yeah.

424
00:42:41 --> 00:42:42
- Not in the loop here.

425
00:42:42 --> 00:42:43
- By design, that's how we build these features.

426
00:42:43 --> 00:42:45
- Yes.

427
00:42:45 --> 00:42:54
- All right, so as a kind of a quick summary for you, ants crashing, definitely check your provider for its privacy policies.

428
00:42:54 --> 00:42:55
- Yes.

429
00:42:55 --> 00:43:05
- Check out your accounts for the subscriptions you have for these providers and see what kind of control you have to be able to limit the data that it uses for training.

430
00:43:05 --> 00:43:15
But as for Apple, Apple does not see anything in as it's sending these requests, all that data is sent directly to your provider.

431
00:43:15 --> 00:43:24
- With the one caveat that if you send us a feedback report saying, when I write this, it doesn't work, we will see the thing that you told us you wrote.

432
00:43:24 --> 00:43:25
- Yes, that's true.

433
00:43:25 --> 00:43:30
If you click on the Feedback Assistant button, we will see that context.

434
00:43:30 --> 00:43:30
Yes.

435
00:43:31 --> 00:43:31
All right.

436
00:43:32 --> 00:43:32
Great.

437
00:43:33 --> 00:43:36
Moving on to, man, these usernames are fantastic.

438
00:43:36 --> 00:43:38
Stuff MC has a question.

439
00:43:39 --> 00:43:44
What's the best way to work with AI in Xcode while being offline?

440
00:43:44 --> 00:43:51
For example, in a train while commuting and without having an M5 Pro with 128 gigs of RAM.

441
00:43:52 --> 00:43:59
You kind of mentioned a little bit about this, Nathan, about providing guardrails a little bit more, but do you have any other suggestions?

442
00:43:59 --> 00:44:04
First of all, the best way to get one of those models running and get the most out of it is MLX.

443
00:44:04 --> 00:44:08
And there's a reason we have a whole session on how to use MLX in this context.

444
00:44:09 --> 00:44:16
You can get a larger, more interesting model running with MLX than any other technology on our devices.

445
00:44:17 --> 00:44:20
And I really strongly recommend watching that session and getting started with that.

446
00:44:22 --> 00:44:25
The next thing, though, is it is all about the guardrails.

447
00:44:25 --> 00:44:34
It's all about the constraints, and you'd be surprised how much you can accomplish on your laptop on a train if you make sure that the agent has to pass unit tests to move on.

448
00:44:35 --> 00:44:43
And you write good unit tests with the agent all the time, then it can't lie and cheat its way out of a situation if it happens not to be smart enough to figure it out the first time.

449
00:44:44 --> 00:44:48
It can keep working until it finds the solution, and it will find the solution.

450
00:44:48 --> 00:44:55
I think if the offline is temporary or you're going on a commute or something like that, sometimes you can combine the two.

451
00:44:56 --> 00:45:03
So you can use a larger model before you know that you're going to be offline to help the planning and requirements and setting things up.

452
00:45:03 --> 00:45:11
Then you're offline for a number of hours, a number of days, and have those local agents document all the assumptions that they make.

453
00:45:11 --> 00:45:17
And then when you're back online, you can use a large model to double check that those assumptions that were made all along the way actually match up.

454
00:45:18 --> 00:45:29
Yeah, and this is like a very exciting area, sort of ongoing research from a lot of our partners is the idea of using larger models to advise agents powered by smaller models.

455
00:45:29 --> 00:45:35
You can get a lot of that functionality and benefit just by asking agents to write things out, to mark down files, and then critique each other.

456
00:45:35 --> 00:45:42
And then they can talk back and forth and find things out, and Xcode makes it very easy to even run that simultaneously.

457
00:45:42 --> 00:45:50
It's a great feature of Xcode to be able to configure multiple agents and model and just start a new conversation with different agents and models, right?

458
00:45:50 --> 00:45:57
So you can switch when you're offline to a local version of this and then go back to the other one when you're online.

459
00:45:57 --> 00:46:00
I wanted to point out, Nathan, you mentioned a session that would be great to watch.

460
00:46:00 --> 00:46:07
I believe the name of the session is Run Local Agentic AI on Mac Using MLX is the one you can take a look at.

461
00:46:07 --> 00:46:08
Thank you.

462
00:46:08 --> 00:46:09
No problem.

463
00:46:09 --> 00:46:12
All right, moving on to another question from Jay Roden.

464
00:46:14 --> 00:46:17
I've been using Claude code in Terminal for quite a while now.

465
00:46:18 --> 00:46:20
I find that I barely need to use Xcode anymore.

466
00:46:20 --> 00:46:21
What am I missing?

467
00:46:24 --> 00:46:25
Go ahead.

468
00:46:25 --> 00:46:32
Yeah, no, I think one of the things that, I think one of the things that's really important is everyone's going to approach using agents in different ways.

469
00:46:32 --> 00:46:34
And there's different kinds of experiences.

470
00:46:35 --> 00:46:44
And so I think the thing that we have built in Xcode is both a set of really powerful tools and we've built a great user experience on top of that.

471
00:46:45 --> 00:46:53
One of the things that's really great about our user experience that we're trying to really invest in is the difference between kind of like a non-linear flow.

472
00:46:53 --> 00:46:55
A terminal is just a lot of text flying by.

473
00:46:55 --> 00:46:56
It's also very linear.

474
00:46:56 --> 00:47:01
So you're understanding the story is kind of like unwrapping in that linear fashion.

475
00:47:01 --> 00:47:09
What I love about Xcode is that we try to pull out the bits of the story that are happening into the kind of new artifacts area next to the conversation.

476
00:47:09 --> 00:47:12
So you can see the evolution of the change kind of unfolding.

477
00:47:13 --> 00:47:17
You can, you know, click around, edit source code, et cetera.

478
00:47:17 --> 00:47:20
And then there's all of Xcode's context.

479
00:47:20 --> 00:47:30
So the actual in-memory context of having the files that are open, the schemes that you have active, all that is information that we use to help you as you're working.

480
00:47:31 --> 00:47:38
So even natural language things like, oh, this and that, it's just very easy to point to what you're working on.

481
00:47:38 --> 00:47:42
Currently selected, what your project settings are, things like that.

482
00:47:42 --> 00:47:46
Yeah, it's not just a source editor that can run things.

483
00:47:46 --> 00:47:48
It's an ensemble of tools.

484
00:47:48 --> 00:47:55
You have instruments, you have all the previous system, you have the device hub now that the agent can control, right?

485
00:47:55 --> 00:48:06
And so when you put all those tools together, it really supercharges the developer experience given tools that are not possible to use in just the plain text view of the terminal.

486
00:48:07 --> 00:48:15
Yeah, I think the idea of you being able to write a great prompt is a practice that everyone's doing now, especially with agent workflows.

487
00:48:15 --> 00:48:34
But to then also back it up with the entire context that Xcode can provide about your project, about what Git history or crash data, things like that, I think it just really benefits and makes that first solution really closer to what you want to achieve.

488
00:48:35 --> 00:49:00
Yeah. I think also, I mean, a lot of it depends on how you want to work, right? Like it doesn't have to be all or nothing either, right? Going back to terminal sometimes, maybe that's the right answer. But when you're in Xcode, you get a really visually rich experience. You get richly rendered, beautiful markdown, you get previews. So you get to see like the results

489
00:49:00 --> 00:49:04
in a very different way than you would get just in a regular terminal.

490
00:49:05 --> 00:49:06
With amazing color seams as well.

491
00:49:06 --> 00:49:07
With great colors.

492
00:49:07 --> 00:49:07
Great color.

493
00:49:08 --> 00:49:08
Beyond the work.

494
00:49:10 --> 00:49:12
All right.

495
00:49:12 --> 00:49:16
We can get a few more questions in before the end of today's group lab.

496
00:49:16 --> 00:49:19
So let's go move on to Jack92's question.

497
00:49:20 --> 00:49:24
Is it possible to use local AI models for coding intelligence with Xcode 27?

498
00:49:25 --> 00:49:26
We talked a little bit about this.

499
00:49:26 --> 00:49:30
What would your suggestion be to balance speed, accuracy, and privacy?

500
00:49:31 --> 00:49:37
So kind of going for the first question, again, watch run local agentic AI on the Mac using MLX.

501
00:49:37 --> 00:49:39
That's the best way to approach it.

502
00:49:39 --> 00:49:45
But I think the interesting part that we could add here is what would you suggest to balance speed, accuracy, and privacy?

503
00:49:45 --> 00:49:55
We talked a little bit about some solutions to that, including using the larger model first for planning and then have the local smaller models maybe keep track of its work or assumptions.

504
00:49:55 --> 00:50:03
Yeah, I think part of it is, like, there's a lot of local models, and they all have different, you know, there's different quantizations, floating point, et cetera.

505
00:50:04 --> 00:50:07
Part of it is kind of see what works for your project.

506
00:50:08 --> 00:50:08
Absolutely.

507
00:50:08 --> 00:50:28
could be totally different. Yeah. We spend a lot of time trying to figure out how to assess that we are giving agents the right tools to do their jobs effectively, that when we provide them with additional context, it's helping them and not hurting them, things like that. So we've become very experienced in benchmarking these agents and models. And one of the things we've learned along

508
00:50:28 --> 00:50:48
the way has been that that gives you really powerful signal. And you can go online and read all sorts of spreadsheets that people put together of every variation with every letter of the alphabet combined in a very long name of each variant of these models uh but the thing that ultimately is the determining factor of how you are drawn to this stuff is the vibes it's how it

509
00:50:48 --> 00:51:08
feels to use so the best way to figure out this balance for yourself if you're picking which kind of agent you want to use which kind of model you want to pair with that agent whether that's from you know a commercial provider somewhere or it's local on your machine is giving it a shot and seeing if you enjoy it and if it's giving you things that you want to use i think a common

510
00:51:08 --> 00:51:29
pattern too is that you can develop your own kind of like scorecard like there are things that are you know really important to me that you know in my project that agents can you know or different models can get wrong so I can develop this like scorecard and then I can run through you know local models as new ones come out try different settings and just see for my scorecard how are

511
00:51:29 --> 00:51:41
they doing and it doesn't have to be something that's like super scientific and has a bunch of numbers behind it can be like Nathan said just like vibes of like oh this this one felt the best It enabled me to feel the most creative and to work the fastest.

512
00:51:41 --> 00:51:41
Yeah.

513
00:51:42 --> 00:51:49
And if you become a person who's really into this stuff, you might find yourself asking the agent to help you create something that's like a mechanistic scorecard that you can reuse.

514
00:51:49 --> 00:51:54
And then you wind up chasing, well, what do the vibes I felt correspond to in numbers and stuff like that?

515
00:51:54 --> 00:51:59
But, yeah, just get started and see what you can feel out about what you want these tools to do for you because they're so personal.

516
00:52:00 --> 00:52:04
And maybe another thing would be it's not always necessarily true that the bigger the model, the better the results.

517
00:52:04 --> 00:52:05
Definitely not.

518
00:52:05 --> 00:52:15
Like, sometimes it's actually good to check extremes of, like, okay, I used all this memory to load this model, but it doesn't actually give me, like, much better results or even any better results than something that's much, much, much smaller.

519
00:52:16 --> 00:52:19
It can dynamically change and have multiple models on your Mac and swap them.

520
00:52:21 --> 00:52:22
Or swap them depending on the task that you're doing.

521
00:52:22 --> 00:52:23
Exactly.

522
00:52:23 --> 00:52:24
Yeah, on the task.

523
00:52:24 --> 00:52:24
Absolutely.

524
00:52:26 --> 00:52:29
Another kind of related question from Ran Learns.

525
00:52:30 --> 00:52:35
I don't want to give access to Anthropic, OpenAI, or Google for privacy reasons.

526
00:52:35 --> 00:52:40
Is there a path to use foundation models as the agent for Xcode coding intelligence?

527
00:52:42 --> 00:52:45
I think it's a lot of the things we've already been talking about, like use local models, local agents.

528
00:52:46 --> 00:52:49
Yeah, run local agentic AI on the Mac using MLX sessions.

529
00:52:49 --> 00:52:51
It's exciting to hear all the excitement about this.

530
00:52:51 --> 00:52:52
Absolutely.

531
00:52:52 --> 00:52:53
The Mac is a great place to do this.

532
00:52:54 --> 00:52:54
Yeah, absolutely.

533
00:52:55 --> 00:52:58
Like on just an M5 Mac, it's pretty powerful already.

534
00:52:59 --> 00:53:04
If you have an array of Mac studios, you know, you could have one of the largest models on it.

535
00:53:05 --> 00:53:07
So absolutely, definitely try that out.

536
00:53:07 --> 00:53:12
Okay, let's take a look at a couple more questions.

537
00:53:12 --> 00:53:14
We have one from C. Prada.

538
00:53:15 --> 00:53:30
Is there a beginner's guide or similar document that gives an overview of this topic that we're talking about, as opposed to tutorials for those of us who need a general map of a subject that they can fill in after with details as opposed to jump in the middle of the subject hands-on approach?

539
00:53:31 --> 00:53:52
So yeah, if someone wanted to try to figure out how to approach it or like a good roadmap map for how to how to approach using agents rather than just trying it out as we've been suggesting if you're a visual learner one of the things that's really cool about the the session we keep mentioning this Xcode agents and use session is that there are lots of very illustrative diagrams

540
00:53:52 --> 00:54:09
of what is the agent actually doing right now that's giving it an understanding of what's happening and when you start to sort of break down into pieces what it's doing and understand what it's what's happening that's a very powerful thing so I really do recommend that session a lot I don't know do you have suggestions for other resources

541
00:54:09 --> 00:54:10
we provide today for this sort of thing?

542
00:54:11 --> 00:54:31
I think just talking to folks is a great way to learn too I'm like constantly learning things from you know Nathan and Ken and Jerome things that they're trying or you know yeah talk to other developers how they're using it exchange and you know the best practices you know just give it a try and and I also say like don't

543
00:54:31 --> 00:54:37
like don't overthink it like it's also like it can you know sometimes you can feel overwhelming because it's changing so rapidly and so fast.

544
00:54:38 --> 00:54:43
But because it's changing rapidly and so fast, there's also, like, there's just a lot that you can discover.

545
00:54:43 --> 00:54:44
And it's okay.

546
00:54:44 --> 00:54:52
Like, there's a lot of great kind of initial groundwork that we've done, you know, in our tools and the model providers have done to make it seamless to onboard.

547
00:54:53 --> 00:55:13
Yeah, and while I think we generally have approached it by saying, like, just try it, one of the things that, you know, it's really, really great about the way it's integrated in Xcode is that you can try it without as high of a risk or impact on what you're building or, like I always talk about plan mode because there's plan mode in lots of places,

548
00:55:13 --> 00:55:24
but plan mode really allows you to ensure that you know exactly what it's doing and you could ask questions, like you made this plan, like how did you actually approach this?

549
00:55:24 --> 00:55:34
But yes, Xcode Agents and You does do a really great job in that session to walk through, like this is a prompt, this is what it's working on, but this is also how it's working behind the scenes.

550
00:55:34 --> 00:55:35
Really great way to approach it.

551
00:55:35 --> 00:55:47
One, going back to an earlier question, I mean, this kind of reminds me, one of the values of staying in Xcode is that you get that choice of, like, how much you want to buy in to the new workflow.

552
00:55:48 --> 00:55:49
You can just do a little bit.

553
00:55:50 --> 00:55:51
You can do a lot.

554
00:55:51 --> 00:56:02
So as you're new to the experience, I think it's a lot less disruptive than if you were to just go and be all in on terminal, like, I'm going to totally change how I do things.

555
00:56:03 --> 00:56:09
So I think that you have a much broader continuum of, you know, how you can work with it.

556
00:56:10 --> 00:56:14
Yeah, and I would say maybe one other thing, too, is, like, the community has a lot of great resources out there.

557
00:56:14 --> 00:56:25
And one of the things we've worked really hard in Xcode 27 is that the way that you use agents in Xcode 27, there's a lot of transfer from other places that you use them.

558
00:56:25 --> 00:56:32
So if there's other techniques or things that are happening in other, you know, IDEs or other tools, generally those are just going to transfer just fine in Xcode 2.

559
00:56:33 --> 00:56:37
So it doesn't have to be that it's only about look for resources that are about Xcode specifically.

560
00:56:38 --> 00:56:42
Just look in general, and you can do those techniques inside of Xcode with Xcode 27.

561
00:56:43 --> 00:56:49
I was going to mention documentation for the agent providers is a really great way or how people are using agent providers currently.

562
00:56:49 --> 00:56:53
And, again, one of the cool things is that there's not a single right path.

563
00:56:54 --> 00:56:56
You're not playing Mozart.

564
00:56:56 --> 00:56:57
You're playing jazz.

565
00:56:57 --> 00:57:01
And you can sort of figure out what's working for you by borrowing from everything you hear.

566
00:57:01 --> 00:57:02
What a great quote.

567
00:57:02 --> 00:57:20
you're not playing Mozart you're playing jazz and kind of related to that Kevin you were mentioning models are changing all the time you know like best practices right now might be different in the future so it really is about trying it out and seeing how much you want to buy in or just testing things out depending on what you want to build. They do I mean

568
00:57:20 --> 00:57:27
every time a new model comes out they literally change the best practices change so you have to sort of be aware of that. Maybe there's a new one where we're actually talking. Could be.

569
00:57:29 --> 00:57:59
Alright I think we have time for one more question. Let's see which one we could take. Actually, I wanted to add a question that we got in a previous group lab. I think we had a coding intelligence, Apple intelligence group lab back in Tuesday evening here in Cupertino. And there was a great question around how do we make sure that the most up-to-date APIs, like the things that we announced at

570
00:57:59 --> 00:58:20
DubDub this year, that agents are actually familiar with this. We talked about it very briefly, but I wanted to make sure that we say it out loud in this group lab. What's the best way for new APIs to be in the agent's context? Yeah, so we have a whole tool that's dedicated to being able to look up new APIs and documentation. So we have awesome teams that develop our, you know,

571
00:58:21 --> 00:58:36
amazing documentation for all of our new APIs. And then we've provided tools for Xcode to be able to search through that documentation. And we've experimented with a bunch of techniques to help make that as efficient as possible so that you always know that you're getting the latest APIs.

572
00:58:36 --> 00:58:39
When you download Xcode, you also download the latest documentation.

573
00:58:39 --> 00:58:40
I was going to say, it's on-device.

574
00:58:41 --> 00:58:51
Yeah, and now it comes in the version you can read, but also versions specifically for models to be very efficient, to be very fast, and give you the best answers.

575
00:58:51 --> 00:59:03
And so the agent, when he doesn't know about an API, will actually tap into that documentation and get all the resources from that documentation and add it to the context that now it can help you with that.

576
00:59:03 --> 00:59:07
We're also shipping Xcode with a set of specialists, right?

577
00:59:07 --> 00:59:09
Skills that we build.

578
00:59:10 --> 00:59:15
For example, there's one new resizability feature that we're shipping in iOS 27.

579
00:59:15 --> 00:59:20
And so, you know, so we're going to help you when you ask this question of building this new API.

580
00:59:21 --> 00:59:26
The agent will bring that skills and have new context and new ways to build those features.

581
00:59:28 --> 00:59:29
So, yeah.

582
00:59:30 --> 00:59:34
The other thing is, if you don't have confidence in this yet, just give it a shot.

583
00:59:35 --> 00:59:41
Create an empty project and ask the agent to demonstrate a new set of iOS 27 APIs for you.

584
00:59:41 --> 00:59:46
I think Foundation Models is a great choice here, actually, because there doesn't happen to be one of these specialists for it.

585
00:59:46 --> 00:59:52
And part of that is because it's so good at getting that information from this documentation index and working with it.

586
00:59:53 --> 00:59:58
They've done a great job on the documentation team of giving us what we need to be able to make that happen.

587
00:59:58 --> 01:00:03
Yeah, and that's the thing else, too, is you can always ask for, like, search documentation.

588
01:00:03 --> 01:00:04
Use documentation.

589
01:00:04 --> 01:00:09
Or there's a new API, you know, in iOS 27, you know, about this topic.

590
01:00:09 --> 01:00:13
You know, it would actually directly ask the model to go to the documentation, right?

591
01:00:13 --> 01:00:17
So I guess don't feel shy about asking the agent to, like, use these tools.

592
01:00:17 --> 01:00:17
Ask the model.

593
01:00:18 --> 01:00:23
I think in particular, as we were talking about models changes, providers change.

594
01:00:23 --> 01:00:37
What's great about Xcode providing this document tool, documentation tool, Is that regardless of which provider you're using, whether you're using a local model or, say, Cloud Codex or Gemini, you are getting that information.

595
01:00:38 --> 01:00:40
Xcode is providing that to whatever agent you're using.

596
01:00:40 --> 01:00:47
So if the agent isn't up to date with the APIs because we just announced it, Xcode's got your back on that one.

597
01:00:47 --> 01:00:51
And the great news is the documentation ships as an individual asset, right?

598
01:00:51 --> 01:00:57
So we're going to update documentation as new APIs are coming, and you will get that automatically as a new download in Xcode.

599
01:00:58 --> 01:00:58
That's a great point.

600
01:00:58 --> 01:00:59
That's a great point.

601
01:01:00 --> 01:01:02
- Well, what a great way to end our group lab.

602
01:01:02 --> 01:01:05
Unfortunately, that's all the time we have.

603
01:01:05 --> 01:01:09
Thank you to my panelists, Nathan, Kent, Jerome, Kevin.

604
01:01:09 --> 01:01:13
It's so great to have you here to answer questions, give your insights.

605
01:01:13 --> 01:01:19
And thank you to the team behind the scenes for helping triage the questions and keep this event running smoothly.

606
01:01:19 --> 01:01:24
And of course, thank you to all of you who joined us, whether you asked the question or just tuned in.

607
01:01:24 --> 01:01:29
It's great to have you and we're so privileged to be able to host this for you and answer questions for you today.

608
01:01:29 --> 01:01:42
If you didn't get your question answered, check out the forums, developer.apple.com slash forums, and you could also check out the generative AI search experience in developer.apple.com to learn more about what we talked about today.

609
01:01:43 --> 01:01:51
If you have a code level question, bug, enhancement request, please submit in feedbackassistant.apple.com.

610
01:01:51 --> 01:01:53
Again, thank you for joining us.

611
01:01:53 --> 01:01:54
Have a great WWDC.
