---
title: Coding Intelligence, Machine Learning & AI Group Lab
source: https://developer.apple.com/videos/play/wwdc2026/8121/
session: 8121
collection: wwdc2026
duration: 01:02:21
fetched: 2026-06-12
via: whisper.cpp ggml-large-v3 (session SD video audio)
transcription: machine-generated, no speaker diarization
---

> Machine transcription of the **Coding Intelligence, Machine Learning & AI Group Lab** SD video via whisper.cpp (`ggml-large-v3`).
> Group labs ship no Apple transcript; this is generated from the audio. Cues are timestamped segments, **not** speaker-attributed turns.

## Transcript

1
00:00:03 --> 00:00:07
Hey, everyone. Welcome to the Coding Intelligence, Machine Learning, and AI Group Lab.

2
00:00:07 --> 00:00:15
I'm Shashank. I'm part of the evangelism team here at Apple, and I'm joined today by a fantastic panel of experts from across the machine learning framework teams.

3
00:00:15 --> 00:00:20
We'll start off with a quick round of introductions, but I'd like to make it a little more interesting.

4
00:00:21 --> 00:00:34
There have been a lot of exciting machine learning and AI announcements at WWDC, agents in Xcode, new Core AI framework, new evaluations framework, Foundation Malls Framework Updates, MLX Distributor, and so much more.

5
00:00:34 --> 00:00:45
So, each of you, as you introduce yourself, please share one thing that you are most excited and you think developers would be most excited about from your area.

6
00:00:46 --> 00:00:47
Yeah.

7
00:00:47 --> 00:00:50
I'm Kevin, and I work on Xcode.

8
00:00:50 --> 00:01:06
And one of the things that I'm most excited about is that I get to actually build software that I couldn't really build before, whether it's something that was maybe I just didn't have time for, or maybe it was something new, like a new technology, like one of your technologies that I get to adopt in an app and try out.

9
00:01:06 --> 00:01:09
Agents, let me do it efficiently and learn new things.

10
00:01:12 --> 00:01:13
Are we just going down the line?

11
00:01:13 --> 00:01:13
All right.

12
00:01:13 --> 00:01:14
Hi, everybody.

13
00:01:14 --> 00:01:15
My name is Eric.

14
00:01:15 --> 00:01:17
I work on the Foundation Models Framework.

15
00:01:18 --> 00:01:20
And I would say, man, we've got a lot coming out this year.

16
00:01:20 --> 00:01:29
But the thing that I'm most excited about, personally, is the language model protocol that allows you to plug in all kinds of different inference backends to the framework.

17
00:01:29 --> 00:01:31
So you can call out to MLX, Core AI.

18
00:01:32 --> 00:01:34
We've got a Google package that just went live.

19
00:01:34 --> 00:01:36
Anthropic released theirs this morning.

20
00:01:37 --> 00:01:38
There's so many options now.

21
00:01:38 --> 00:01:42
And developers like you guys are going to be able to add your own integrations as well.

22
00:01:42 --> 00:01:44
So it's going to open up tons of possibilities.

23
00:01:45 --> 00:01:46
I'm really excited about that.

24
00:01:46 --> 00:01:46
Exciting.

25
00:01:47 --> 00:01:48
Hi, everyone.

26
00:01:48 --> 00:01:49
I'm Steven.

27
00:01:49 --> 00:01:51
I'm an engineer on the evaluations framework team.

28
00:01:52 --> 00:01:57
I'm personally really excited about the entire concept of evaluations and bringing that to you all.

29
00:01:58 --> 00:02:09
One thing in particular is the model judge evaluator, just how easy we've made it for you to configure a model judge evaluator to rate the quality of the responses that you're getting from your LLM-based features.

30
00:02:11 --> 00:02:12
Hi, everyone.

31
00:02:12 --> 00:02:13
My name is Raziel.

32
00:02:13 --> 00:02:15
I work on Core AI.

33
00:02:15 --> 00:02:20
Selfishly, I will say that the most exciting feature is the launch of Core AI.

34
00:02:21 --> 00:02:28
I got into building AI frameworks because I wanted to bring my research to production, and I built a few, and I think this is the best one so far.

35
00:02:29 --> 00:02:36
And also because it powers a lot of our features, like Eric was mentioning, the foundational framework, Siri.

36
00:02:36 --> 00:02:40
So we get to contribute to everything we're building at Apple.

37
00:02:40 --> 00:02:41
Thanks.

38
00:02:42 --> 00:02:43
Hi, everybody. I'm Angelus.

39
00:02:44 --> 00:02:45
I'm in the MLX team.

40
00:02:46 --> 00:02:53
And what I'm most excited about, I mean, I've been saying this all the past few days to people that ask me.

41
00:02:54 --> 00:02:57
I'm really excited about local AI this year.

42
00:02:57 --> 00:03:02
I think we're basically at the point where local AI is starting to become useful.

43
00:03:02 --> 00:03:07
And it's not something like a gimmick, like a chat, like a small thing you can do in your CLI.

44
00:03:07 --> 00:03:09
You can actually do work with it.

45
00:03:09 --> 00:03:11
You can actually have local agents.

46
00:03:11 --> 00:03:18
You can have the FM chat on app, even, to do other things on your devices.

47
00:03:18 --> 00:03:21
Basically, I think it's the year of local AI, right?

48
00:03:21 --> 00:03:23
So I'm really excited about that.

49
00:03:23 --> 00:03:25
And of course, I'm always excited about MLX.

50
00:03:25 --> 00:03:28
So no need to mention that.

51
00:03:28 --> 00:03:50
Thank you. Thank you. All right. In addition to those you see on screen, there's a whole team of experts behind the scenes helping triage all your questions. So please keep them coming. We'll get to as many as we can today. For code specific questions or anything we can't get to today, I encourage you to head over to our developer forums at developer.apple.com slash forums and

52
00:03:50 --> 00:04:21
ask there. And if you have a bug report or feature request, please file those using feedbackassistant.apple.com so that we can use the time today for questions that are more broadly applicable to everyone. All right. So with that, let's get started with our first question. Here's a question from Jane Chow. Could you explain the roles of core AI, core ML, and MLX in simple terms?

53
00:04:22 --> 00:04:44
From a beginner's perspective, how should one understand them and decide which one to learn or use okay who wants to take this one okay i can give the high level overview go for it yeah and i will i will make it broader right like here is not mentioning for example the foundation model framework and i think what we're trying to do at apple is build this comprehensive suite of

54
00:04:44 --> 00:05:02
different technologies that basically the developers can approach at different levels you're going to start right like at the top most with the foundation model framework If you want to use and your use case is well served by an LLM that we provide then or that you can plug in there, then that works great.

55
00:05:02 --> 00:05:14
Then if you will have something more custom, either a model that you have, you train or you download it from, you know, one of the multiple repositories, you should try Core AI, right?

56
00:05:14 --> 00:05:18
Like it comes with a lot of SLAs and guarantees, particularly if you're building an application.

57
00:05:18 --> 00:05:24
And then obviously there is MLX, which is extremely successful and doing really powerful stuff, right?

58
00:05:24 --> 00:05:31
Like the demos that Angelus has shown about distributed with multiple machines, that's super exciting.

59
00:05:31 --> 00:05:33
And you can build another type of use cases with that.

60
00:05:33 --> 00:05:36
So that's what we're trying to do.

61
00:05:36 --> 00:05:44
Specifically with Core ML, I think moving forward, we're asking everybody that work with neural networks to use Core AI.

62
00:05:44 --> 00:05:53
I have Core ML that stays there, but I think right now it's going to remain focused on just the traditional ML, decision trees, that type of stuff.

63
00:05:53 --> 00:05:55
but everything new should be moving to Core AI.

64
00:05:56 --> 00:05:57
Thanks, Raziel.

65
00:05:57 --> 00:06:03
Any foundation model-specific nuance or MLX-specific nuance to contribute to that answer?

66
00:06:03 --> 00:06:11
Yeah, so specifically if you're doing things with an LLM, language model stuff, start with foundation models, try the system language model, see if that gets you there.

67
00:06:12 --> 00:06:16
Use evaluations to show for sure whether it gets you there or not.

68
00:06:16 --> 00:06:19
If that doesn't work, you've got private cloud compute.

69
00:06:20 --> 00:06:30
And then if you need something more custom, you want to bring your own model, you want to use an open source model, Try plugging in Core AI through foundation models so that you have the same API.

70
00:06:30 --> 00:06:34
It's just like a one and two line switch, and you're off to the races.

71
00:06:34 --> 00:06:40
If you're doing things that are not language models, so you're doing diffusion models, you're doing image segmentation, drop down to Core AI.

72
00:06:40 --> 00:06:42
If you need it, drop down to MLX.

73
00:06:42 --> 00:06:45
Does that sound pretty clear?

74
00:06:45 --> 00:06:48
No, that's exactly that.

75
00:06:48 --> 00:06:50
I think it's pretty obvious.

76
00:06:50 --> 00:06:55
When you're doing language model stuff, you should take advantage of the language model protocol.

77
00:06:55 --> 00:07:00
It allows you to change and try things-- local, remote, obviously, right?

78
00:07:00 --> 00:07:08
And I would say maybe the only thing that hasn't been mentioned, which could be kind of interesting for people, is that if you want to train, for instance-- Oh, yeah, yeah.

79
00:07:08 --> 00:07:15
--on device and things, then that is a use case that's kind of like way easier in MLX or maybe unique to MLX this year.

80
00:07:15 --> 00:07:20
But yeah, and that would be a reason to use, for instance, the MLX adapter for foundation models.

81
00:07:20 --> 00:07:21
Yeah, exactly.

82
00:07:21 --> 00:07:22
Things like that.

83
00:07:22 --> 00:07:24
So yeah, definitely start foundation models.

84
00:07:24 --> 00:07:28
Definitely use evaluations to know which of these models actually, is the one that covers your use case.

85
00:07:28 --> 00:07:30
Because why go PCC?

86
00:07:30 --> 00:07:34
Why go to a remote model if the on-device model already covers your use case?

87
00:07:34 --> 00:07:34
Yeah, exactly.

88
00:07:34 --> 00:07:39
So why bring something when it's already there to fix it?

89
00:07:39 --> 00:07:40
Yeah, I think that's a good summary.

90
00:07:40 --> 00:07:45
I will mention that we have several sessions this year that cover all these topics.

91
00:07:45 --> 00:07:53
We have sessions on MLX that talk about how to connect four Mac studios to run a trillion parameter model, run local agentic loops.

92
00:07:53 --> 00:07:55
Not on the phone, guys.

93
00:07:55 --> 00:07:55
Not on the phone.

94
00:07:57 --> 00:08:05
And there's Korea sessions on bringing models from PyTorch also integrating into apps and PCC and dynamic profiles.

95
00:08:06 --> 00:08:10
A lot of sessions, and maybe we'll get to some of these if you all have specific questions.

96
00:08:10 --> 00:08:11
So thank you, thank you.

97
00:08:11 --> 00:08:12
So let's move on.

98
00:08:12 --> 00:08:13
Here's another question.

99
00:08:14 --> 00:08:16
This is from Abhi27.

100
00:08:16 --> 00:08:21
What is the on-device foundation model's context window in iOS 27?

101
00:08:21 --> 00:08:26
is the input and output counted against one shared token budget.

102
00:08:27 --> 00:08:29
Sounds like a question for you, Eric.

103
00:08:29 --> 00:08:29
I can answer that one.

104
00:08:30 --> 00:08:33
So the context size is the same as it was before.

105
00:08:33 --> 00:08:36
It's 4096, and it is a shared window.

106
00:08:36 --> 00:08:41
So if you input 4,000 tokens, your response can go up to the remaining 96.

107
00:08:42 --> 00:08:43
It's about what it boils down to.

108
00:08:44 --> 00:08:45
And PCC?

109
00:08:46 --> 00:08:47
Oh, and PCC is 32K.

110
00:08:47 --> 00:08:50
So it goes all the way up to 32K.

111
00:08:50 --> 00:08:51
Same thing, it's a shared budget.

112
00:08:52 --> 00:09:06
Okay, so from a developer's point of view, if they want a larger context window, we would recommend using the PCC and if they have a need for deeper reasoning capabilities and those sorts of things.

113
00:09:06 --> 00:09:06
Yeah, exactly.

114
00:09:06 --> 00:09:10
So you have more choice this year, although the window for on-device is the same.

115
00:09:10 --> 00:09:11
Yeah, exactly.

116
00:09:11 --> 00:09:21
And if you really want to go crazy with huge context sizes, plug in something from MLX or AI or grab one of those server language model packages, go up to a million tokens.

117
00:09:21 --> 00:09:22
Yeah, exactly.

118
00:09:22 --> 00:09:33
I do want to mention the MLX team and the Core AI team have a Swift package that lets you use the Foundation Models framework by just switching the model.

119
00:09:33 --> 00:09:42
So you can bring in your own model asset, bundle it with your app, and then take advantage of whatever the token context window is for that model.

120
00:09:43 --> 00:09:45
It's why Eric is excited about the language model protocol.

121
00:09:45 --> 00:09:45
Yeah.

122
00:09:47 --> 00:09:50
And on that note, I'm going to steal this moment to make a plug.

123
00:09:50 --> 00:09:57
Both of those packages are open source, and so very soon, so will be the Foundation Models framework.

124
00:09:57 --> 00:10:02
So there's a great story here around all the open source components plugging together and being able to learn from the source code.

125
00:10:02 --> 00:10:03
It should be really neat.

126
00:10:03 --> 00:10:04
That's a pretty good point.

127
00:10:04 --> 00:10:10
There are great examples for other people to just look at the code, see what we did there.

128
00:10:10 --> 00:10:12
Maybe they want to do something very special.

129
00:10:12 --> 00:10:15
They have really good examples there.

130
00:10:15 --> 00:10:16
Yeah, absolutely.

131
00:10:16 --> 00:10:24
We actually have a session this year that not only shows how to use these different packages, but also how to build your own.

132
00:10:24 --> 00:10:26
Bring your own LLM provider.

133
00:10:27 --> 00:10:36
So if you have a custom ML model framework, then you can conform to this protocol and bring your own.

134
00:10:36 --> 00:10:38
And you can really cheat.

135
00:10:38 --> 00:10:40
So here's a tip for you.

136
00:10:40 --> 00:10:48
If you want to make your own language model and plug it into the framework, go into Xcode, open up your agentic coding.

137
00:10:48 --> 00:10:54
We have a skill that we've open sourced that will help your agent go in and implement the language model protocol.

138
00:10:54 --> 00:11:03
And you can also point it at both of the open source repos for MLX and core AI and give it to your agent as a source of inspiration.

139
00:11:03 --> 00:11:07
And it should be able to do a pretty good job putting the pieces together for you.

140
00:11:08 --> 00:11:15
And of course, you have to use the evaluation framework to decide which model is-- Yeah.

141
00:11:15 --> 00:11:17
We got you covered.

142
00:11:17 --> 00:11:19
All right, let's move on.

143
00:11:19 --> 00:11:21
Another good question-- thank you.

144
00:11:21 --> 00:11:23
Thank you, Abhi.

145
00:11:23 --> 00:11:24
Another question from Abhi27.

146
00:11:24 --> 00:11:39
This is, can foundation models run inside background app refresh task or background processing task, especially while the phone is locked asleep or the app has been backgrounded for a while?

147
00:11:40 --> 00:11:42
Ooh, that's a really good question.

148
00:11:42 --> 00:11:43
Good nuanced question here.

149
00:11:43 --> 00:11:44
Yeah, that's a good one.

150
00:11:45 --> 00:11:48
In the background, yes, it can run.

151
00:11:48 --> 00:11:49
So you can do it in like a background task.

152
00:11:49 --> 00:11:56
There is a possibility, particularly if the OS is busy doing other things, it may rate limit you.

153
00:11:56 --> 00:12:03
So you may catch a rate-limited error from the system language model that tells you you're done for a little bit.

154
00:12:03 --> 00:12:06
So just wait and try again in the future.

155
00:12:06 --> 00:12:10
On macOS, you should be good as long as you're in the foreground.

156
00:12:10 --> 00:12:16
And with the private cloud compute language model, you may hit rate limiting for other reasons.

157
00:12:16 --> 00:12:19
There's rate limiting that happens because the system's busy.

158
00:12:19 --> 00:12:25
And then there's rate limiting that can happen because you've sent too many requests in a very short period.

159
00:12:25 --> 00:12:31
And those manifest as different kinds of errors in the API, so you've got to tell them apart.

160
00:12:31 --> 00:12:34
FRANCESC CAMPOY: So a developer would be able to catch them and appropriately show the right kind of message.

161
00:12:34 --> 00:12:36
FRANCESC CAMPOY: This is because I didn't get it.

162
00:12:36 --> 00:12:41
So on the macOS, are you also rate limited, or you're never rate limited with the local foundational?

163
00:12:41 --> 00:12:44
With the local foundation model, you won't get limited as long as your app's in the foreground.

164
00:12:45 --> 00:12:45
Okay.

165
00:12:45 --> 00:12:51
Do I have to worry about quality of output if I'm rate limited, or is it just a matter of time?

166
00:12:51 --> 00:12:52
It's just a matter of if it comes out or not.

167
00:12:53 --> 00:12:55
You'll get the same response in the foreground and the background.

168
00:12:56 --> 00:12:58
It just might take a little longer in the background.

169
00:12:58 --> 00:12:58
Yeah.

170
00:12:59 --> 00:13:01
Sounds good.

171
00:13:02 --> 00:13:05
Another question from Dessa.

172
00:13:05 --> 00:13:09
For macOS 27, Apple Intelligence, what does waitlist mean?

173
00:13:10 --> 00:13:13
Both city, local, and PCC models are working.

174
00:13:13 --> 00:13:16
Are we getting different models on the wait list?

175
00:13:17 --> 00:13:21
Does this beta include AFM core advanced 20 billion model?

176
00:13:21 --> 00:13:22
Thanks.

177
00:13:22 --> 00:13:25
Oh, that's like they snuck in two questions within one.

178
00:13:27 --> 00:13:30
Who's going to take that one?

179
00:13:32 --> 00:13:33
Everybody's looking at you.

180
00:13:34 --> 00:13:37
We're the closest to Apple intelligence, but yeah.

181
00:13:38 --> 00:13:43
So on the record, I don't work directly on Siri, so I'm not fully qualified to answer this, but I do know the answer.

182
00:13:43 --> 00:13:44
Then I should plug in.

183
00:13:44 --> 00:13:49
We do have a group lab for Apple intelligence, I believe, later this week.

184
00:13:49 --> 00:13:51
So please bring this question again.

185
00:13:51 --> 00:13:55
But we'll try to answer-- I'll tell you the answer as spoiler alert.

186
00:13:55 --> 00:13:58
So the waitlist applies only to Siri.

187
00:13:58 --> 00:14:04
It doesn't apply to private cloud compute language model or to any of the things that Siri does on device.

188
00:14:04 --> 00:14:06
OK.

189
00:14:06 --> 00:14:08
You knew the answer very well.

190
00:14:08 --> 00:14:10
We just wanted to plug the other group.

191
00:14:10 --> 00:14:12
And as a bonus, I'll answer the second question, too.

192
00:14:12 --> 00:14:16
The second question was, does the beta include AFM core advanced?

193
00:14:16 --> 00:14:18
The answer is yes.

194
00:14:18 --> 00:14:22
It's used for the voice features and stuff.

195
00:14:22 --> 00:14:24
FRANCESC CAMPOY: Great.

196
00:14:24 --> 00:14:30
This question is from Indigo J. The Foundation Models Framework-- you're popular, can I say?

197
00:14:30 --> 00:14:38
The Foundation Models Framework now supports bringing your own LLM provider alongside the on-device model and private cloud compute.

198
00:14:38 --> 00:14:42
Can you mix all three within a single agentic flow?

199
00:14:42 --> 00:14:48
And what are the data privacy and attribution boundaries once a third-party provider is in the loop?

200
00:14:49 --> 00:14:50
This is such a good question.

201
00:14:50 --> 00:14:52
I'm really glad that you asked this one.

202
00:14:53 --> 00:14:54
So thank you, IndigoJay, for that one.

203
00:14:54 --> 00:14:56
You should start by plugging your session.

204
00:14:56 --> 00:14:57
Yes.

205
00:14:58 --> 00:14:59
There's a really great session.

206
00:15:00 --> 00:15:01
The guy who delivers it is awesome.

207
00:15:02 --> 00:15:04
It's, oh, I just forgot the name of it.

208
00:15:04 --> 00:15:05
Help me out, Shank.

209
00:15:05 --> 00:15:10
Building agentic experiences with foundation models.

210
00:15:10 --> 00:15:13
Building agentic experiences with foundation models.

211
00:15:13 --> 00:15:14
Give that one a watch.

212
00:15:14 --> 00:15:18
It has the answer to the question, But I'll recap it here.

213
00:15:18 --> 00:15:27
We have a new API called Dynamic Profiles that allows you to route to different models using a declarative API.

214
00:15:27 --> 00:15:29
On Apple platforms, we have the system language model.

215
00:15:29 --> 00:15:31
We have private cloud compute.

216
00:15:31 --> 00:15:34
They're appropriate for different kinds of things.

217
00:15:34 --> 00:15:43
And there's great reasons to start doing something with the system language model, but then switch to the private cloud compute language model when things get more complicated.

218
00:15:43 --> 00:15:48
And you may even want to pull out a third party language model like you just suggested.

219
00:15:48 --> 00:15:52
We try to make those boundaries extremely clear in the API.

220
00:15:52 --> 00:16:03
It's declarative, and that declarative nature makes it really easy to reason about which model is going to wind up doing each task within that workflow that you're talking about.

221
00:16:03 --> 00:16:21
And so in the session that I encouraged you to watch, you'll see that we talk a lot about designing the boundaries between the models, designing your handoffs based on things like performance, privacy, and then if you're using a third party model, things like cost come into play as well.

222
00:16:21 --> 00:16:25
FRANCESC CAMPOY: So this may be better answered by watching the session.

223
00:16:25 --> 00:16:30
So does this mean that the whole context is going to be sent to each model?

224
00:16:30 --> 00:16:32
Or-- FRANCESC CAMPOY: Oh, that's a really good question.

225
00:16:32 --> 00:16:34
That would be the main privacy concern, right?

226
00:16:34 --> 00:16:36
MARK MANDEL: Yeah.

227
00:16:36 --> 00:16:38
So we talk about two patterns.

228
00:16:38 --> 00:16:40
There's obviously lots of patterns you can use to build agents.

229
00:16:40 --> 00:16:46
But the two that we call out in the session are baton pass and phone a friend.

230
00:16:46 --> 00:16:48
And baton pass-- It's not very private.

231
00:16:48 --> 00:16:49
It does not sound very private.

232
00:16:49 --> 00:16:50
It is not.

233
00:16:52 --> 00:16:58
So baton pass, every agent or every dynamic profile is involved in the same race.

234
00:16:58 --> 00:17:03
Like, when you're running a relay race, you know the person before you.

235
00:17:03 --> 00:17:04
You see them coming.

236
00:17:04 --> 00:17:05
You know everything that they're doing.

237
00:17:05 --> 00:17:09
When they hand off the baton to you, you have full context on everything that's happening.

238
00:17:09 --> 00:17:12
And then you take that baton and you run across the finish line.

239
00:17:12 --> 00:17:14
Like, once you have the baton, you don't give it back.

240
00:17:14 --> 00:17:16
You're crossing the finish line.

241
00:17:16 --> 00:17:17
You're in charge now.

242
00:17:17 --> 00:17:22
And so that is the way that you want to orchestrate things.

243
00:17:22 --> 00:17:27
If you intend the full context to be shared, you know there's nothing in there that's privacy sensitive.

244
00:17:27 --> 00:17:32
Or if you're only using the on-device model, you're only using private cloud compute, you don't have to worry about it.

245
00:17:32 --> 00:17:33
And so that's a great way to go.

246
00:17:33 --> 00:17:44
If you want to establish those privacy boundaries, you know that you're handing off to, like, an untrusted model or just one that has a weaker privacy guarantee, then you want to use phone-a-friend.

247
00:17:44 --> 00:17:55
Phone a friend is like when you're on Who Wants to Be a Millionaire, and you get a question from Regis, and the question's like, hey, what's the name of that statue in New York?

248
00:17:56 --> 00:18:02
So I get on the phone, and I call up Shashank, and I'm like, Shashank, I can't remember the name of that statue in New York.

249
00:18:02 --> 00:18:03
It's a big green lady.

250
00:18:04 --> 00:18:08
Shashank has no view into the previous questions that I've been asked.

251
00:18:08 --> 00:18:10
He just knows what I'm asking him right now.

252
00:18:10 --> 00:18:14
And so that preserves the privacy of the beginning of the game show, right?

253
00:18:14 --> 00:18:19
A key part of that is once Shashank says, oh, that's the Statue of Liberty.

254
00:18:19 --> 00:18:20
Back to you.

255
00:18:20 --> 00:18:23
It's back to me, and I have to give the final answer.

256
00:18:24 --> 00:18:26
So there's a little bit of extra repetition in there from time to time.

257
00:18:27 --> 00:18:30
Would it be okay to say it's like tool calling, basically?

258
00:18:31 --> 00:18:31
Yeah, it is.

259
00:18:32 --> 00:18:35
You're tool calling a bigger model or a different model.

260
00:18:35 --> 00:18:36
It could be smaller.

261
00:18:36 --> 00:18:39
Tool call with an ephemeral session, and then you get back the answer and you repeat it.

262
00:18:40 --> 00:18:42
So there's great privacy benefits to doing that.

263
00:18:42 --> 00:18:45
It also gives you an extra context window.

264
00:18:45 --> 00:18:48
So if you need a little bit more size, you can get it that way.

265
00:18:50 --> 00:18:51
Like if you want a bunch of stuff.

266
00:18:51 --> 00:18:52
Yeah, yeah, yeah.

267
00:18:52 --> 00:18:52
Very good.

268
00:18:52 --> 00:18:57
And if you are sharing context, right, how do you manage that between models that have different context windows?

269
00:18:58 --> 00:18:58
Yeah.

270
00:18:58 --> 00:19:00
So another great question.

271
00:19:00 --> 00:19:05
You'll see in the Foundation Models Framework this year, we've added this notion of profile modifiers.

272
00:19:05 --> 00:19:19
And so you can hook in on things like handoffs, and you can also do it just purely in a declarative way that says, for example, right, I want to save on context, so I'm just going to keep the last 10 entries in the transcript.

273
00:19:20 --> 00:19:28
Or as soon as a tool call output has been used to produce a response, we're just going to drop that tool call from the one day.

274
00:19:28 --> 00:19:29
It doesn't need to be in the transcript anymore.

275
00:19:30 --> 00:19:36
Yeah, and these can be done in functional ways, so it's a stateless transform.

276
00:19:36 --> 00:19:41
So if you go to the on-device model, you're just looking at the last 10 entries in the transcript.

277
00:19:41 --> 00:20:05
but if you bounce back to private cloud compute you've got a lot more context size to work with and so everything before that suffix comes back into play and you have access to the full context and so there's really flexible ways to go about you into the transcript but just the last few yeah yeah yeah makes sense wow that was good i think a nuanced discussion yeah uh all right uh

278
00:20:05 --> 00:20:08
Next question is from Claire Casey.

279
00:20:08 --> 00:20:09
I'm a new developer.

280
00:20:10 --> 00:20:17
My app uses on-device speech-to-text and must recognize names, proper nouns that general models miss.

281
00:20:17 --> 00:20:28
Does iOS automatically personalize recognition to each user, learning their words and pronunciations over time, or do I need to build and maintain that list myself?

282
00:20:29 --> 00:20:31
Who's qualified to answer that one?

283
00:20:32 --> 00:20:41
This refers to a few different possible frameworks across speech and, I don't know, does anyone?

284
00:20:42 --> 00:20:46
I don't think we might have the expertise for that one on this panel.

285
00:20:47 --> 00:20:53
At a higher level, it feels like there are two sort of subtopics here.

286
00:20:53 --> 00:21:01
One is the ability to do all these online learning, I think, what Siri might do at an OS level.

287
00:21:02 --> 00:21:04
The other is what you do within the app, right?

288
00:21:05 --> 00:21:11
There's things that you do inside the app, then that is on the developer to manage, right?

289
00:21:11 --> 00:21:15
I mean, I know I used to do research on speech recognition.

290
00:21:15 --> 00:21:25
So in general, typically when you have a speech recognizer, you have also typically another component that does your personalization component.

291
00:21:26 --> 00:21:31
For example, you have a smaller language model that does personalization.

292
00:21:31 --> 00:21:35
So that's something that you can fine-tune just based on the examples.

293
00:21:35 --> 00:21:38
This is very common when you have your contact list, right?

294
00:21:38 --> 00:21:46
For example, my name is really hard for anybody like automated language model to know how to pronounce Raziel, right?

295
00:21:46 --> 00:21:48
So that's typically something that gets learned.

296
00:21:49 --> 00:21:54
So that will be the component that whether our API supports or not.

297
00:21:54 --> 00:21:57
I just don't know categorically whether we do.

298
00:21:57 --> 00:21:58
I will assume that we do.

299
00:21:59 --> 00:21:59
Yeah, yeah.

300
00:21:59 --> 00:22:00
But we don't know, right?

301
00:22:00 --> 00:22:01
Yeah.

302
00:22:02 --> 00:22:15
No one here represents those frameworks, but just to sort of close this out, would bringing your own custom model through ODI, it's possible to build a system like this?

303
00:22:15 --> 00:22:18
Definitely, you can build it your own.

304
00:22:18 --> 00:22:19
Yeah.

305
00:22:19 --> 00:22:21
The question is whether you have to build it.

306
00:22:21 --> 00:22:24
Do you want to do it, or if there's a native-- OK.

307
00:22:24 --> 00:22:30
I would also think that unless it's one of the languages that's not supported, that maybe is the reason.

308
00:22:30 --> 00:22:34
But if it's one of the languages that's already supported by the speed circuit condition on our devices.

309
00:22:34 --> 00:22:39
I think it's one of the-- not the-- like I would just use what they supported.

310
00:22:39 --> 00:22:42
I think it's going to be already pretty good, right?

311
00:22:42 --> 00:22:45
MARK MANDEL: I would recommend also just asking this question on the developer forum.

312
00:22:45 --> 00:22:48
We've got engineers watching that like a hawk all week long.

313
00:22:48 --> 00:22:49
FRANCESC CAMPOY: This week.

314
00:22:49 --> 00:22:52
MARK MANDEL: So you've got a very good chance of getting the answer from the person who really knows it.

315
00:22:52 --> 00:22:54
FRANCESC CAMPOY: Exactly.

316
00:22:54 --> 00:22:57
FRANCESC CAMPOY: Sounds good.

317
00:22:57 --> 00:23:01
Next question from Pichaya_TRYYS.

318
00:23:01 --> 00:23:06
How do you train coding agents to know more about my code style specific area.

319
00:23:06 --> 00:23:26
I use local LLM, Gwen, in LLM Studio with Xcode/VSCode, continue, to learn my complex code base, which contains Vision OS, metal, physics simulation, and macro that generates complex 3D resources, but it does not perform very well.

320
00:23:26 --> 00:23:27
Ooh.

321
00:23:27 --> 00:23:28
Oh, I'll take this one.

322
00:23:28 --> 00:23:29
This is a good one.

323
00:23:29 --> 00:23:30
Yeah.

324
00:23:30 --> 00:23:35
I think there's a general principle here that I like, which is, how do agents learn anything?

325
00:23:35 --> 00:23:41
And I think one of the things we like to talk a lot about is search and learning.

326
00:23:41 --> 00:23:47
The more tools that you give agents the ability to search and find things, then they can learn.

327
00:23:48 --> 00:23:54
And you also can help teach them how to learn by even just writing down and documenting what they do.

328
00:23:54 --> 00:23:57
A lot of agents learn a lot of how we learn, right?

329
00:23:57 --> 00:24:07
Like if I was trying to teach you something, taking notes is a great way to remember something so you don't have to go search everything over again to learn the same topic.

330
00:24:08 --> 00:24:25
So when it comes to, like, coding, like, style and, like, what the, like, the kinds of, you know, I don't know, the different APIs you like to use or just even, like, your syntax that you like to have, first, the agents are going to be really good just out of the box by seeing source code that is already in your project and copying that.

331
00:24:25 --> 00:24:29
And they'll try to copy that as close as they can already just of what's there.

332
00:24:30 --> 00:24:39
The other thing that you can do is you can provide the ability for the agent to always have some examples every single time it runs a query.

333
00:24:39 --> 00:24:46
So different agents have different files that can get automatically included, like an agent's MD file or a cloud MD.

334
00:24:47 --> 00:24:51
That file, though, is in every single query that you run inside of Xcode.

335
00:24:51 --> 00:24:54
So that context window will get eaten up by that file every time.

336
00:24:54 --> 00:24:55
So you want to keep it really short.

337
00:24:55 --> 00:24:59
You wouldn't want to put, like, your entire style guide in there every single time.

338
00:24:59 --> 00:25:02
you'll be eating up a lot of tokens.

339
00:25:02 --> 00:25:07
So then what you can do is you can reference other files inside of your agent's MD file.

340
00:25:07 --> 00:25:10
So you can say, oh, there's a style guide, and here's where it is on disk.

341
00:25:10 --> 00:25:15
Or whenever you're working in this area of the code base, go check out this other Markdown file.

342
00:25:15 --> 00:25:23
And so the agent will see that in your agent's MD, and it will say, oh, actually, I need to go find some information about what it means to work in this area.

343
00:25:23 --> 00:25:26
Go off and find that particular Markdown file, et cetera.

344
00:25:26 --> 00:25:36
So it's really just about search, go find Markdown files, And then learning, and as it goes along, you can also tell it things like document all the things that you're seeing in these Markdown files.

345
00:25:36 --> 00:25:44
So if it's seeing your network layer for the first time, tell it to write down all the assumptions that it sees as it goes along.

346
00:25:45 --> 00:25:46
And then you have that.

347
00:25:46 --> 00:25:49
You can correct it, and you can also keep it up to date.

348
00:25:49 --> 00:25:56
And then you can point it at it and say, hey, every time you do networking, go look in this networking thing first to kind of learn how to do those tasks.

349
00:25:56 --> 00:25:57
Oh, that's a great tip.

350
00:25:57 --> 00:26:05
Like I find sometimes when I create a new conversation, it doesn't necessarily remember what I had done in another project.

351
00:26:05 --> 00:26:10
And if you have a file like that, you can kind of get it to build up consistent behavior across everything that you're working on.

352
00:26:11 --> 00:26:11
Yeah.

353
00:26:11 --> 00:26:11
I like that.

354
00:26:12 --> 00:26:12
Yeah, many files.

355
00:26:13 --> 00:26:13
Yeah, yeah.

356
00:26:13 --> 00:26:18
And you can modify it like per your entire global or you can do it per project, right?

357
00:26:18 --> 00:26:30
So I feel like the onus is on the developer a little bit to continuously teach these agents what is my style and what is preferred for this particular project, for my coding style, what does my team prefer.

358
00:26:30 --> 00:26:36
And the more that you can do that, the better that you're going to get that agent to conform to the style that you like.

359
00:26:36 --> 00:26:48
Yeah, like Eric was saying, for building a new provider for the foundation model framework, you can have a skill, then you point to examples like what we have for MLX and Core AI, and then it learns how to do that.

360
00:26:49 --> 00:27:02
Now, one thing I'll add, which is it can be overwhelming to think about, oh, agents MD and all these documents and skills, like I have to like build this big, you know, corpus of them.

361
00:27:02 --> 00:27:04
Sometimes that can be helpful.

362
00:27:04 --> 00:27:09
But whenever a new model comes out, our recommendation is try not using any of it.

363
00:27:10 --> 00:27:11
Try starting over.

364
00:27:11 --> 00:27:21
And so actually developing the ability to, or I don't want to overuse the word skill, developing the skill to learn which skills you actually need is a good skill to have.

365
00:27:21 --> 00:27:32
Because then as new models come out, as they train new things that run locally or new frontier models, you can kind of have a habit of checking, oh, do I actually need all that information?

366
00:27:32 --> 00:27:36
because you might not need it, because the model has gotten advanced enough to understand it.

367
00:27:36 --> 00:27:40
FRANCESC CAMPOY: Yeah, they learn new APIs as well.

368
00:27:40 --> 00:27:46
Maybe if you're using-- especially if you're using a local model that is a bit older, for instance, it may not know the new APIs, right?

369
00:27:46 --> 00:27:47
And then it learns them.

370
00:27:47 --> 00:27:49
So no need to document them explicitly anymore.

371
00:27:49 --> 00:27:52
MARK MANDEL: Now, speaking of Apple APIs, that's actually a great point.

372
00:27:52 --> 00:27:54
Thank you for bringing that up, Andres.

373
00:27:54 --> 00:28:00
Xcode has a really extensive documentation that we search on.

374
00:28:00 --> 00:28:03
So you can say things like, search documentation for information about this API.

375
00:28:03 --> 00:28:15
So even if you're using a model that was even trained a while ago that hasn't seen our new APIs, you can bring all the new APIs from the new version of iOS and macOS, et cetera, right into Xcode.

376
00:28:15 --> 00:28:23
And it will be able to see all those by doing tool calls to search for those APIs and learn what's new and to learn how to use them and pull them into your project.

377
00:28:23 --> 00:28:27
MARK MANDEL: That's really useful, especially during betas like this.

378
00:28:27 --> 00:28:42
People underestimate how these things actually make are-- I like to say, and this-- anybody that tries-- that is doing local models where you can actually try old models and see that with the new tools, they're actually suddenly much better.

379
00:28:42 --> 00:28:51
A huge improvement that has happened lately is not actually in the models, but it is in all these things, all the tools that we give them now that we didn't use to give them like two years ago, right?

380
00:28:51 --> 00:28:53
Yeah, the rigging around them.

381
00:28:53 --> 00:28:54
Yes, exactly, all the things around them.

382
00:28:54 --> 00:28:59
So basically if you take a model that is even a few years old And you put it in the new Xcode.

383
00:28:59 --> 00:29:00
New hardness.

384
00:29:00 --> 00:29:04
Yeah, it's going to be so much better than even last year's Xcode experience.

385
00:29:05 --> 00:29:06
So that's pretty good.

386
00:29:06 --> 00:29:07
Yeah, you get grounding.

387
00:29:07 --> 00:29:14
You get running the code, seeing if it's working, debugging, then going back into an agentic loop and fixing the things, running it again.

388
00:29:14 --> 00:29:15
It's incredible.

389
00:29:15 --> 00:29:17
And you can even use the simulator.

390
00:29:17 --> 00:29:18
Yeah, yeah.

391
00:29:18 --> 00:29:20
And then get it to remember all the things that it tried.

392
00:29:20 --> 00:29:23
I think the question mentioned some crashes.

393
00:29:24 --> 00:29:27
As you see crashes, have it document what it did to solve it.

394
00:29:27 --> 00:29:35
The next time it comes through, it can use it as almost a memory to remember, like, oh, I should avoid that particular pattern, and the agent gets it right the first time more often.

395
00:29:36 --> 00:29:44
There's a small nuance in the question where they say, I use a local LLM Qen in LM Studio with Xcode, right?

396
00:29:45 --> 00:29:49
And then they mentioned that the code base has all these metal and multiple things.

397
00:29:50 --> 00:29:59
So I think depending on what model Xcode is hooked up to, the ability of the model to have a larger context window or smaller.

398
00:29:59 --> 00:30:02
If it's running locally on your MacBook, context window is smaller.

399
00:30:02 --> 00:30:08
So it may not be able to replicate the style of the entire-- may not be able to read everything.

400
00:30:08 --> 00:30:12
FRANCESC CAMPOY: I would say, from personal experience, obviously, we don't know the specific model.

401
00:30:12 --> 00:30:13
We don't know the code base.

402
00:30:13 --> 00:30:20
I think it would be pretty good, especially on style replication and things like that, even smaller models that you can run on your laptop.

403
00:30:20 --> 00:30:25
I do assume that they're running with MLX, just because.

404
00:30:25 --> 00:30:26
FRANCESC CAMPOY: Fair.

405
00:30:26 --> 00:30:29
FILIP HRACEK: Yeah, yeah, exactly.

406
00:30:29 --> 00:30:35
But yeah, the difficulty will be into fixing more complicated things that require very deep reasoning.

407
00:30:35 --> 00:30:48
If you're using a smaller model, then it's going to be obviously not as good as-- but the tools and all the goodies that come with Xcode are going to be usable from a smaller model as well.

408
00:30:48 --> 00:30:56
We have an example in our session where we're actually using Xcode with a QWEN 35B.

409
00:30:56 --> 00:30:59
And it goes through the normal user tools.

410
00:30:59 --> 00:31:01
So are you using-- this is actually a good point about the question.

411
00:31:01 --> 00:31:04
Are you using the chat?

412
00:31:04 --> 00:31:05
I am using the chat provider.

413
00:31:05 --> 00:31:09
But I know I should be using the-- It's new.

414
00:31:09 --> 00:31:13
So something that's new in Xcode 27 is we've added ACP support.

415
00:31:13 --> 00:31:22
So you can now actually plug in agents that would talk to the models hosted locally with LLM Studio or Alamo or the other local providers.

416
00:31:22 --> 00:31:26
That's something that's new with Xcode 27 and definitely something for free to try out.

417
00:31:26 --> 00:31:29
Yeah, but even-- and you should.

418
00:31:29 --> 00:31:30
People should try this out.

419
00:31:30 --> 00:31:33
But even with the TAD, it would still work well, right?

420
00:31:33 --> 00:31:35
It still works, but agents are great.

421
00:31:35 --> 00:31:36
Way, way better.

422
00:31:36 --> 00:31:36
Way better.

423
00:31:36 --> 00:31:43
I'm not saying, but the point is if they do ask question, can I use a local model with Xcode?

424
00:31:43 --> 00:31:44
Yes.

425
00:31:44 --> 00:31:45
The answer is clearly yes.

426
00:31:45 --> 00:31:49
You should use the ACP, but you could also do it any other way.

427
00:31:49 --> 00:31:50
It still should work.

428
00:31:50 --> 00:31:53
So on that topic, what is the advantage of ACP?

429
00:31:53 --> 00:31:58
What's that get you over just the chat completions?

430
00:31:58 --> 00:32:02
Well, the agent client protocol lets you talk to the agent of your choice.

431
00:32:02 --> 00:32:06
So the difference with the chat, it's just a single transcript.

432
00:32:06 --> 00:32:10
Whereas with agents, agents can have sub-agents.

433
00:32:10 --> 00:32:11
They can manage their state.

434
00:32:11 --> 00:32:20
They can do file I/O. The agentic harnesses can go in those loops much, much, much longer than a single conversation.

435
00:32:20 --> 00:32:46
Awesome. So answer to the question is, yes, it should work. You should use it. And the crash is a mystery. Unless we have more information, we don't know why it's crashing. So developer forums is a good place to continue this conversation. Thank you. So a question from Brian KM. With regards to UI testing, what practical steps can teams take to integrate automated approaches

436
00:32:46 --> 00:32:50
into their testing workflows on Apple platforms.

437
00:32:51 --> 00:32:56
Should we assume testing here is testing the output of the model?

438
00:32:57 --> 00:32:58
That's a good question.

439
00:32:59 --> 00:33:00
Sounds like UI testing.

440
00:33:00 --> 00:33:05
Yeah, sounds like UI testing, but perhaps using AI tools.

441
00:33:05 --> 00:33:06
Yeah.

442
00:33:06 --> 00:33:08
I like to think about it in a couple of layers.

443
00:33:09 --> 00:33:14
I like to think of first is getting the agent to think in the smallest kind of kernels, right?

444
00:33:14 --> 00:33:15
These are like your unit tests.

445
00:33:16 --> 00:33:23
One of the things that we try to do is build systems in a way that they can be tested independently.

446
00:33:24 --> 00:33:28
And then you get the agent to enumerate all the different possible cases.

447
00:33:28 --> 00:33:32
So your unit tests are really like the place that you think about all the permutations and combinations.

448
00:33:32 --> 00:33:35
And we often don't hook them up to like real backend systems.

449
00:33:35 --> 00:33:37
It's all something that we can run super fast.

450
00:33:38 --> 00:33:41
Then the next layer is we think of, well, let's start bringing in some of those dependencies.

451
00:33:42 --> 00:33:45
And let's have them run a little bit longer, a little bit more expensive.

452
00:33:45 --> 00:33:52
And again, agents are great at helping you kind of structure your modules in your code base to be able to be more testable.

453
00:33:52 --> 00:33:57
Something that I often would do when I was handwriting was like not think about those boundaries as much.

454
00:33:57 --> 00:34:00
And I kind of get stuck in the spot where I have to write UI tests for everything.

455
00:34:00 --> 00:34:01
Those are just really expensive.

456
00:34:02 --> 00:34:04
But agents let me be like, actually, you know what?

457
00:34:04 --> 00:34:12
Try to tease this apart and make it a lot more testable and kind of bring it off into a submodule here, bring it into a submodule here, et cetera.

458
00:34:12 --> 00:34:22
And so I kind of view the UI testing as kind of like the last step where you kind of just check to make sure, okay, once I get the UI up and running, does those couple connection points all work out?

459
00:34:23 --> 00:34:30
So I think of like thousands and thousands of unit tests, you know, and kind of these like middle kind of like more expensive integration tests or unit tests.

460
00:34:30 --> 00:34:31
You kind of have a couple hundred of those.

461
00:34:31 --> 00:34:33
And then you have like just a handful of UI tests.

462
00:34:33 --> 00:34:38
And then new with Xcode 27 is now we have interaction with the simulator.

463
00:34:38 --> 00:34:41
So you can have the agent actually test that.

464
00:34:41 --> 00:34:45
learn the patterns, and then have it write UI tests so it can do that repeatedly.

465
00:34:45 --> 00:34:47
And you don't have to use the agent every time.

466
00:34:47 --> 00:34:48
Interesting.

467
00:34:48 --> 00:34:51
So the agent can use the simulator to tap and do stuff?

468
00:34:51 --> 00:34:52
Yeah.

469
00:34:52 --> 00:34:52
Wow, that's pretty cool.

470
00:34:52 --> 00:34:54
That's new and excellent.

471
00:34:54 --> 00:34:56
And look at the eye, like take a screenshot or whatever.

472
00:34:56 --> 00:35:03
Yeah, so the agent can tap, it can swipe, it can type, and then it actually surfaces back the accessibility tree and screenshots.

473
00:35:03 --> 00:35:07
And it can kind of use both and figure out what's on screen and kind of tap through it.

474
00:35:07 --> 00:35:17
So sometimes we'll just let the agent run for a couple of hours and look for different bugs and things and give out reports of which areas needed the most attention.

475
00:35:17 --> 00:35:23
And then we can kind of write UI tests or even figure out, oh, actually, we should make that a unit test because that system is just really flaky.

476
00:35:24 --> 00:35:32
Does that mean you could just give a bunch of screenshots of the finalized UI and say, go build this, and then it should be able to instantly recreate that?

477
00:35:32 --> 00:35:32
Yeah.

478
00:35:34 --> 00:35:34
Give it a shot.

479
00:35:35 --> 00:35:35
See how it goes.

480
00:35:37 --> 00:35:38
VDOS, VDOS.

481
00:35:38 --> 00:35:38
Yeah.

482
00:35:40 --> 00:36:05
Sounds good. Another question here. Have there been any updates to the natural language processing and Apple Vision Kit? Now that the foundation model supports image attachment, what should be the preferred method of image extraction? A few different frameworks mentioned here, natural language framework, vision framework, I assume, and foundation models having support for

483
00:36:05 --> 00:36:26
image inputs. Yes. Maybe we could start there. Yeah, there's all kinds of stuff in there. I don't know as much about the natural language framework so i can't speak to that the vision kit or the vision framework has lots of updates this year there's some really cool stuff in that i'd recommend the what's new in vision session image understanding yeah image understanding apologies

484
00:36:26 --> 00:36:46
what's new in image understanding they cover all of it there there's things like segmentation models which are yeah which i think are just really really cool for as far as i think like part of the thing that this question is getting at is when should I use foundation models to understand something about an image versus the vision models? Vision framework,

485
00:36:46 --> 00:36:57
yeah. Yeah, and the line is a little bit blurry, particularly because we're introducing new tools for foundation models that are powered by vision.

486
00:36:57 --> 00:37:12
So we've got a barcode reader tool and we've got a OCR tool that we're adding into the framework this year. And so the way that I think about trying to delineate it is if you're doing something that is pretty well understood.

487
00:37:12 --> 00:37:23
It's roughly the same every time, like you're trying to detect a particular image or a particular object in an image, go for vision 'cause it's optimized for that.

488
00:37:23 --> 00:37:25
It's a well understood problem.

489
00:37:25 --> 00:37:26
You can test it really well.

490
00:37:26 --> 00:37:28
It's extremely efficient.

491
00:37:28 --> 00:37:46
If you're doing something completely different every time, or if it requires like semantic understanding, or if you're taking a prompt from a user that has some kind of natural language nuance, That's really the domain of foundation models and natural language understanding mixed with, of course, those new vision capabilities.

492
00:37:46 --> 00:37:51
And so then you're going to need to step up to the foundation models framework and tackle it that way.

493
00:37:51 --> 00:37:53
I hope that delineates it.

494
00:37:53 --> 00:38:01
Yeah, so basically, if there is something in structure for which there is already an API that fits that structure, use that API.

495
00:38:01 --> 00:38:01
Yeah, definitely.

496
00:38:01 --> 00:38:03
And now the foundation model framework is...

497
00:38:03 --> 00:38:03
Yeah.

498
00:38:03 --> 00:38:07
I talk about it sometimes, like foundation models are kind of like a 3D printer.

499
00:38:07 --> 00:38:10
You can do all kinds of stuff with them.

500
00:38:10 --> 00:38:15
And if you have to make lots of custom orders, man, foundation models, 3D printers, they get the job done.

501
00:38:16 --> 00:38:19
But if you're going into production, you've like designed your thing, you know what it's going to be.

502
00:38:20 --> 00:38:32
Sometimes like a custom, well, not custom, but like a baked model that does one thing really well, like a production line that's just stamping out copies of your product is a little more efficient than a 3D printer.

503
00:38:33 --> 00:38:51
I think the other example I was giving someone else today was the difference between using foundation models framework for translation versus using the Translate API because there are a subset of languages the Foundation Models Framework supports for translation, whereas the translation framework has a higher number of languages.

504
00:38:51 --> 00:38:52
They are specialized.

505
00:38:52 --> 00:38:54
You know what the input language is.

506
00:38:54 --> 00:38:56
You know what the output language is.

507
00:38:56 --> 00:39:04
It's much easier to use that versus-- Yeah, but the difference is, again, to go in the nuance because you could say, translate it as if it was 1920.

508
00:39:04 --> 00:39:07
Yeah, you want to change the style.

509
00:39:07 --> 00:39:15
FRANCESC CAMPOY: Yeah, there's stuff you really cannot do with-- FRANCESC CAMPOY: Or if you're translating somebody that is speaking two languages at the same time.

510
00:39:15 --> 00:39:16
Yeah, exactly.

511
00:39:16 --> 00:39:22
I mean, this probably could be done with classic models as well, but the other thing I don't think any classic model would try to support, right?

512
00:39:22 --> 00:39:23
FRANCESC CAMPOY: Yeah, exactly.

513
00:39:23 --> 00:39:27
Yeah, so it boils down to the use case, right?

514
00:39:27 --> 00:39:32
If you're just translating something every day in the app or the background, then translate API.

515
00:39:32 --> 00:39:54
But if it is dynamic, you didn't know what was coming, those different styles or different, then a language model would be more nice. Yeah, good discussion. Next question is from John Lee. On-device LLMs have relatively limited token capacity. What are the best practices for managing prompt size, tool definitions, and context to

516
00:39:54 --> 00:40:18
avoid exceeding limits while still maintaining high quality response? They don't mention foundation models framework so i assume uh we can start there we can also talk about the best practices and if you're rolling you know shipping your own model in your app and how do you manage yeah maybe start so i can talk a little bit about the apis that we've added this year to help you

517
00:40:18 --> 00:40:40
do things like this and then we can all just talk generally about like how those should be used and what kind of general practices work best so in foundation models api this year we've added a couple of things one of them we actually added a little bit before so in ios 26.4 we added new symbols into the foundation models framework for context size on the system language model and then

518
00:40:40 --> 00:41:02
also counting tokens and those are really useful to know first like programmatically how much context size do i have to work with and then given a prompt how much is this going to take up we've complemented that this year now when you get back a response from the framework you can access response dot usage and that tells you exactly how many tokens were on the input how many tokens were

519
00:41:02 --> 00:41:22
on the output it tells you how many were cached and if you're using a reasoning model the output tells you how many of those output tokens were reasoning versus just the rest of the output but by the way i just want to say that was a highly requested feature yes thank you for all of you for surfacing it as uh to us so we could you know yeah exactly that was that was one that

520
00:41:22 --> 00:41:42
you guys asked for, and we were like, we can make that happen. So if you use those now, you should be able to track your usage very accurately and understand how much context size you have left, and then you can start employing different strategies to manage that. And so, for example, one of the things that you might want to do, particularly when you're working with the

521
00:41:42 --> 00:42:08
on-device language model, which has a smaller context size, is that after you're done with a tool so like the model has invoked the tool the tool has generated output and the model has produced a response from that tool call you may want to go back and drop the tool call and the tool output because often once you have the answer that's really all you need you don't you don't

522
00:42:08 --> 00:42:31
need how you got there anymore and so if your use case is one of those that's one thing that you can do the other thing that you can do we talked about this a little bit earlier you can start to drop older entries the ones that seem to be less relevant or you can compress them so we open sourced a new repo foundation models utilities and the idea with that repo is to more periodically

523
00:42:31 --> 00:42:51
release high level components or building blocks that you can use with the foundation models framework or that you can leverage when you're using core ai or mlx or any of these other models in a very general purpose way to build higher order functionality and one of the things we put in there is a summarize history modifier. And so at the beginning of every prompt, we'll check

524
00:42:51 --> 00:43:13
how big your transcript has gotten. And if it's exceeded a certain size that you can specify, then we will summarize the whole thing down to one entry and we'll start from there. And so you can kind of periodically compress once you get over a certain boundary. And we've made that really easy to do. So we hope that'll help a lot, particularly with the on-device model or when

525
00:43:13 --> 00:43:18
you're moving between a really big model and one that's smaller.

526
00:43:18 --> 00:43:25
Like if you have half the context size with model A that you do with model B, then those tools can be super useful for managing that as you bounce between them.

527
00:43:25 --> 00:43:29
A question on the utilities you mentioned, the open source utilities.

528
00:43:29 --> 00:43:29
Yeah.

529
00:43:30 --> 00:43:35
Would those be applicable if you were using other model providers?

530
00:43:35 --> 00:43:37
Oh, it applies to any backend?

531
00:43:38 --> 00:43:40
Yes, it applies to any backend.

532
00:43:40 --> 00:43:42
This is one of the coolest things about that language model protocol.

533
00:43:42 --> 00:43:43
This is why I'm so jazzed about it.

534
00:43:44 --> 00:43:49
If you conform to that protocol, everything that builds on foundation models, you get that for free.

535
00:43:49 --> 00:43:50
Nothing is special to the...

536
00:43:50 --> 00:43:51
Yeah, exactly.

537
00:43:52 --> 00:43:55
So the dynamic profiles API is really cool.

538
00:43:55 --> 00:43:58
If you haven't had a chance to look at it, go watch the sessions on it.

539
00:43:58 --> 00:44:00
It's like nothing else that's out there.

540
00:44:01 --> 00:44:04
And you get that for free if you adopt the language model protocol.

541
00:44:04 --> 00:44:09
And all the stuff that we're putting in this foundation models utility framework, you get that as well.

542
00:44:09 --> 00:44:12
And so it's really worth doing that work up front.

543
00:44:12 --> 00:44:22
How do you think developers should think about the tradeoff between, like, basically invalidating the KV cache when they're dropping tool calls or tool results versus not doing that?

544
00:44:22 --> 00:44:23
Hotly debated, yeah.

545
00:44:23 --> 00:44:23
Right?

546
00:44:23 --> 00:44:24
Like, what's the best approach?

547
00:44:24 --> 00:44:25
Yeah.

548
00:44:25 --> 00:44:27
So this is where evaluations comes in.

549
00:44:28 --> 00:44:30
You team yourself up.

550
00:44:30 --> 00:44:31
I see what you did there.

551
00:44:32 --> 00:44:35
So, like, there's a debate, right?

552
00:44:35 --> 00:44:43
You can let the context fill up as far as you can because you're not invalidating the KV cache.

553
00:44:43 --> 00:44:44
And so you're keeping your latency low.

554
00:44:45 --> 00:44:53
And then you can eat one big invalidation every now and then where it's a little bit more expensive when you drop stuff or when you reset.

555
00:44:54 --> 00:44:58
Or you can try to do these adjustments, particularly dropping tool calls.

556
00:44:58 --> 00:45:00
You just do that after every response.

557
00:45:00 --> 00:45:02
And then the invalidation is a little bit smaller.

558
00:45:02 --> 00:45:06
It happens more often, but is it as big a deal if it's small?

559
00:45:07 --> 00:45:13
And then the other thing you have to balance with that is you're thinking about strictly the performance from a latency perspective.

560
00:45:13 --> 00:45:15
There's also the performance from an accuracy perspective.

561
00:45:16 --> 00:45:27
It's like, if I let the model fill up its full context size, maybe I'm doing well performance-wise, but maybe there's a lot of stuff in there that's no longer relevant, and maybe the model gets a little bit distracted by it.

562
00:45:27 --> 00:45:40
And so to answer those questions about, like, how often do I summarize, you want to start using the evaluations framework and see, for your use case, with the model that you're using, does it do well when the transcript gets really long?

563
00:45:40 --> 00:45:42
Does it do better after I've summarized?

564
00:45:42 --> 00:45:45
Right. Does it want more context? Does it want less context?

565
00:45:45 --> 00:46:02
right and so you can set up you know a b testing effectively with the same evaluation data set you can see how well it does for each of those different configurations right so set up all of your different dynamic profiles that have those different strategies employed and then just run them all and there's this really great feature which is compare so you can just literally

566
00:46:02 --> 00:46:23
directly compare the results for all those different configurations across the same data set and see how well it does and so it's an incredibly powerful tool i'm really excited about evaluations like we receive so many questions from developers like can the model do a or can the model do b and what it comes down to is your use case is unique yeah yeah and we don't know the

567
00:46:23 --> 00:46:43
answer for you but now we have the tools for you to go get the answer and be very confident in it and that's so important when we're doing you know we're moving into this world where there's a lot more non-deterministic behavior it also means when you reach for a certain model or a certain configuration that you're confident that that is the best one for that job right you can test all

568
00:46:43 --> 00:46:47
So everyone's asking, oh, should I use PCC, or should I use the on-device model for this?

569
00:46:47 --> 00:46:50
Again, evaluations is a really great way to determine that.

570
00:46:51 --> 00:46:56
But when you're summarizing, do you, like, you can change the instructions to summarize?

571
00:46:57 --> 00:47:00
Like, because it's probably, like, you're using the language model to summarize, right?

572
00:47:00 --> 00:47:01
Yes.

573
00:47:01 --> 00:47:05
So there's some instructions, summarize this in this way, or summarize your context in this way?

574
00:47:05 --> 00:47:06
Yeah, exactly.

575
00:47:06 --> 00:47:15
So the principle that we follow for everything that we put into the API is that if there is a prompt involved, That prompt has to be overwritable.

576
00:47:15 --> 00:47:18
So there's a default one baked into the utilities.

577
00:47:18 --> 00:47:19
Try it out, see if it works for you.

578
00:47:19 --> 00:47:26
FRANCESC CAMPOY: Yeah, exactly, because I wanted to say that even the thing you were saying before, it's not just everything or one time a little thing.

579
00:47:26 --> 00:47:27
It's configurable.

580
00:47:27 --> 00:47:34
You can say, make a summary that is verbose, or make a very concise summary, so then you don't have to do that.

581
00:47:34 --> 00:47:37
And that's another thing to hill climb with adjacent things.

582
00:47:37 --> 00:47:38
You can edit everything.

583
00:47:38 --> 00:47:41
This is the main point here.

584
00:47:41 --> 00:47:42
It's using a model.

585
00:47:42 --> 00:47:44
It is a very general model.

586
00:47:44 --> 00:47:45
All of them are.

587
00:47:45 --> 00:47:49
You have to actually tune it for your use case.

588
00:47:49 --> 00:47:51
And you have to measure your use case.

589
00:47:51 --> 00:47:52
And that's it.

590
00:47:52 --> 00:48:03
So yeah, just to close out this, any last quick best practices from your experience on context window management, MLX, Core AI?

591
00:48:03 --> 00:48:09
I think what Eric mentioned about knowing-- and again, evaluations are critical.

592
00:48:09 --> 00:48:13
Knowing how much to remove, how much to condense, what to condense.

593
00:48:13 --> 00:48:17
Because you might condense to say, oh, I want to keep this many tokens.

594
00:48:17 --> 00:48:19
But maybe you want to remove user tool calls.

595
00:48:19 --> 00:48:22
Or maybe there is something else that you want to remove.

596
00:48:22 --> 00:48:25
And a lot of it is very use case dependent.

597
00:48:25 --> 00:48:30
So it's really cool that you can plug in into the existing tools that they are building.

598
00:48:30 --> 00:48:52
And for example, when we were building the demo that we showed earlier today in some other talk, we had the option to build just the LLM directly through Core AI, but we were like, well, we can use the Foundation Model Framework directly programming the custom LLM there, and we got a lot of stuff for free.

599
00:48:52 --> 00:48:56
So let's just do that.

600
00:48:56 --> 00:48:58
So the only thing I could say is you can look at that.

601
00:48:58 --> 00:49:01
But then again, maybe it's not for the phone or anything.

602
00:49:01 --> 00:49:09
But in the more general question of what do we do with the context, right?

603
00:49:09 --> 00:49:13
So we can look at the ML community and the newer models.

604
00:49:13 --> 00:49:14
How do they deal with it, right?

605
00:49:14 --> 00:49:20
And there is a lot of new attentions that are way better behaved in terms of context.

606
00:49:20 --> 00:49:21
It's kind of a nuance.

607
00:49:21 --> 00:49:23
I don't know if people are interested in that part.

608
00:49:23 --> 00:49:24
Like, oh, my attention.

609
00:49:24 --> 00:49:28
Or it's a sliding window attention, so the context doesn't grow.

610
00:49:28 --> 00:49:32
Or it's a linear attention, so the context is fixed from the beginning.

611
00:49:32 --> 00:49:37
These are fundamental architectural changes that new models have a lot of them.

612
00:49:37 --> 00:49:40
Like most models are now using the variation of all of these.

613
00:49:40 --> 00:49:46
So you do get a better kind of behavior with context.

614
00:49:46 --> 00:49:47
That's one option.

615
00:49:47 --> 00:49:53
And then obviously, there's other problems that come with-- it's a reasoning model, right?

616
00:49:53 --> 00:49:55
So reasoning is the first thing that you drop.

617
00:49:55 --> 00:49:57
FRANCESC CAMPOY: Yeah, that's a great point.

618
00:49:57 --> 00:50:00
FILIP HRACEK: Yeah, so reasoning is the first thing you drop.

619
00:50:00 --> 00:50:03
And then there's other models also that try not to reason as much.

620
00:50:03 --> 00:50:09
Because dropping, even if it is reasoning, still cost you latency when you want to process something simple.

621
00:50:09 --> 00:50:16
So new models also-- if people are interested, again, in general-- new models also have configurable reasoning.

622
00:50:16 --> 00:50:24
And they may also choose to reason or not to reason, which is, again, another thing that people do so that they don't have to pay this cost of latency.

623
00:50:24 --> 00:50:26
And they can do better handling of longer context.

624
00:50:26 --> 00:50:31
Because if you don't reason as much, you have more context of useful tokens.

625
00:50:31 --> 00:50:34
FELIPE HOFFA: Like PCC supports low, medium, high options.

626
00:50:34 --> 00:50:35
Yeah, I mean-- Like, medium, deep.

627
00:50:37 --> 00:50:44
But like Angelo was saying, there is some models where you fake a large context, but they come with caveats.

628
00:50:44 --> 00:50:49
So I mean, there is so many different options now.

629
00:50:49 --> 00:51:02
Well, one thing I'll mention before we move on is that a naive thing to manage context window, which we've heard from app developers, or we've suggested to app developers, like, don't let the single session do too many things.

630
00:51:02 --> 00:51:05
just break it apart into multiple things.

631
00:51:05 --> 00:51:08
Don't say, do this, and this, and this, and this.

632
00:51:08 --> 00:51:11
That's just a large-- they're all independent tasks.

633
00:51:11 --> 00:51:21
They could be three separate tasks, and each of them have the whole context window for themselves, versus-- Also, using your own-- the same model as a tool, as we were saying before.

634
00:51:21 --> 00:51:25
Yeah, where then you get the extra-- awesome.

635
00:51:25 --> 00:51:25
Good discussion.

636
00:51:25 --> 00:51:27
Thank you.

637
00:51:27 --> 00:51:36
We have about 10 more minutes, so let's do a few rapid fire-- Or to the extent that we can get to a lot of good questions here.

638
00:51:37 --> 00:51:38
So this is from Evo.

639
00:51:38 --> 00:51:45
Foundation models guardrail sometimes refuse emotionally intense but legitimate journal entries, grief, venting.

640
00:51:45 --> 00:51:55
Can I prevent refusals on first-person emotional writing, and how do I detect guardrail refusal versus other errors to fall back gracefully?

641
00:51:56 --> 00:51:57
That's a good question.

642
00:51:57 --> 00:52:00
So the answer here is both yes and no.

643
00:52:00 --> 00:52:01
It's a little bit nuanced.

644
00:52:01 --> 00:52:13
If you are taking a journal entry as an input to the model, there is a setting that you can choose with the system language model when you're initializing the guardrails.

645
00:52:13 --> 00:52:18
I believe it's called permissible content transformations.

646
00:52:18 --> 00:52:19
Yeah, you got it.

647
00:52:19 --> 00:52:20
You're on the ball.

648
00:52:20 --> 00:52:22
Permissible content transformations.

649
00:52:22 --> 00:52:26
And so if you turn that on, the model will not air out on the input.

650
00:52:26 --> 00:52:29
So you can have some very emotionally charged input.

651
00:52:29 --> 00:52:42
But the model may still refuse in natural language to elaborate on it or to expand on it or to say something that's in the same style as the input.

652
00:52:42 --> 00:52:50
If you're using structured output, so guided generation, then the model can throw a refusal error.

653
00:52:51 --> 00:52:54
The refusal error is different than a guardrail error.

654
00:52:54 --> 00:53:00
The refusal error is the model saying in natural, like saying, I'm not going to answer this.

655
00:53:00 --> 00:53:02
It's the model's response.

656
00:53:02 --> 00:53:14
Right. It's the model's response. It's not related to the guardrail. It's just the alignment training of the model. It produces a response for you that says, I'm sorry, I can't help with this. The guardrail errors is a separate kind of error. You can catch that one separately.

657
00:53:15 --> 00:53:24
And that is a separate moderation model that looks at the input, looks at the output, and flags it if it's problematic.

658
00:53:25 --> 00:53:31
Sounds good. And if you were to bring your own model, then these things don't apply.

659
00:53:31 --> 00:53:34
That's correct. That only applies to Apple's models.

660
00:53:34 --> 00:53:58
right uh so for this developer maybe if some of these things don't work if it's if it's not behaving as you want then please file a feedback yes uh but if it is behaving appropriately but not to your uh what you want to use case there are options to maybe ship a quen model or yeah or something else right you got all kinds of approaches all kinds of i do want to plug though

661
00:53:58 --> 00:54:16
we have worked really hard on the guardrails this year that's been one of the other big things shouldn't be a problem but yeah it it will hopefully be much better this year we've got lots of data sets on it we've trained on it very intensively and the number of false positives should be way down so give it a shot with the newest version of the system language model

662
00:54:16 --> 00:54:38
send us feedback if it's not to your liking and explore all kinds of options thank you uh another question here uh apple has historically brought a distinct perspective to areas like design and privacy. I am curious to learn more about your guiding philosophy or approach to AI evaluation.

663
00:54:39 --> 00:55:02
Amazing. What an incredible question. So I know we don't have a ton of time. I would easily take the rest of the time. But I just want to say that I think the way that we think about evaluation is really not build your AI feature and then at some point down the road, evaluate it. It's really you want to start with evaluation because evaluation is really the living specification

664
00:55:02 --> 00:55:24
of your feature itself it is all of the things that you think it should be able to do well it should include some headroom for you to grow into it and actually perform even better than what you're doing right now should include edge cases and really by doing that you kind of adopt this mindset of an evaluation driven development life cycle where in education I actually used to

665
00:55:24 --> 00:55:45
teach for a while we had this concept of formative assessment which is an assessment for learning right it's not what did you learn already it's how do you learn more right the quizzes themselves give you information back so that you can continue to find your weak spots and that's what evaluation is and so i think we've really tried to bring this to developers in a real way through our

666
00:55:45 --> 00:56:07
documentation and through this framework in that we want to make it as easy as possible for you to start from the beginning thinking about what are all the test cases that will allow this to be a great experience for our developers and so we have tools for even starting with a really small curated data set of your core use cases and expanding that we have some synthetic capabilities for

667
00:56:07 --> 00:56:27
expanding your data set and then running that data set seeing where the model or your configuration does really well and where it falls down and then being able to go back and make those changes and compare them and really just hill climb on that feature so i think you know there's a lot more that could be said there just in general but i think just putting it at the forefront of this

668
00:56:27 --> 00:56:51
type of development and making it something that is easy to do is is kind of what we've i think uniquely been able to accomplish here yeah i must say the the best ai products that i've been involved have been driven this way exactly like evaluation is is is really the bread and butter of ai yeah and it comes it comes from a lot of experience you know having worked on you know valuations for

669
00:56:51 --> 00:57:13
our own products right yeah and so where we really have taken all of that different experience and try to build it into the fabric of this framework itself so that developers can adopt that same mindset this is uh even uh very fundamental in a lot of engineering disciplines right verification and validation as part of design and development yeah it's just it's just harder

670
00:57:13 --> 00:57:18
to do with language models, which is why you need a framework.

671
00:57:18 --> 00:57:20
Great discussion.

672
00:57:20 --> 00:57:20
Thank you.

673
00:57:21 --> 00:57:27
This is an interesting question, and we've had this asked many times, Raziel, probably for you.

674
00:57:27 --> 00:57:33
Is it possible for models used by different apps on iPhone to be shared across apps?

675
00:57:33 --> 00:57:36
This could help save storage space for users.

676
00:57:37 --> 00:57:39
Great question.

677
00:57:40 --> 00:57:46
No, it's not possible to share because it's very complicated, right?

678
00:57:46 --> 00:57:55
Like you may think, oh, if we share a model, then we get to not pollute the, you know, very constrained resources that we have.

679
00:57:55 --> 00:58:03
But at the same time, it becomes harder to control who is using the resource when I'm trying to use it at the same time.

680
00:58:03 --> 00:58:04
Right.

681
00:58:04 --> 00:58:22
Having said that, for example, for model caching that we provide in Core AI that allows you to keep resources in memory, you can share the resource in the cache group if you have an application group, an app group.

682
00:58:23 --> 00:58:24
So that's something that is possible.

683
00:58:24 --> 00:58:31
But just coming and saying, oh, there is one model that will serve multiple apps, it becomes really complicated.

684
00:58:31 --> 00:58:43
The other reason is that I think like we've been talking, these models can be very nuanced into how you want to use them and even what are the trade-offs that you're going to make.

685
00:58:43 --> 00:58:49
When you run a model, we typically optimize them or quantize them in a particular way.

686
00:58:49 --> 00:58:53
And there is some trade-offs involved in terms of quality, for example, or performance.

687
00:58:54 --> 00:59:10
So even if you're saying, say, OK, I want to use a Qend model, even if it's the same Qend model, let's say a Qend 6.6 billion, and you might want to use a floating point, because in your evaluation, that's what meets the needs of your use case.

688
00:59:10 --> 00:59:19
But somebody else may say, oh, no, I want to use the 4-bit one, because I want to buy some performance, and the evaluation tells me that that's fine for my use case.

689
00:59:19 --> 00:59:30
So it becomes really tricky to-- the fine-grained nature of all these use case specific optimizations make it tricky to do it that way as well.

690
00:59:30 --> 00:59:31
Yeah.

691
00:59:31 --> 00:59:34
There's also sandboxing on the-- I guess apps can't access.

692
00:59:34 --> 00:59:35
Exactly.

693
00:59:35 --> 00:59:46
So even download, like, because, you know, say the thing you can do on the Mac, right, which is you download, which I'm not even sure if you can do it in Mac app, but you can do it, you know, like with Python, for instance.

694
00:59:47 --> 00:59:53
You download and you put it in a shared CAS and then some other app tries to download and it's like, oh, I found it in the CAS.

695
00:59:53 --> 00:59:54
I can use it, right?

696
00:59:54 --> 01:00:00
That's fundamentally something you can't do on the iPhone for security reasons and other reasons.

697
01:00:00 --> 01:00:05
So it's actually extremely hard to share models across apps.

698
01:00:05 --> 01:00:14
FRANCESC CAMPOY: So just for my own clarification there, the model weights, you can put them into a shared app container group.

699
01:00:14 --> 01:00:16
And the weights, you can just share the download.

700
01:00:16 --> 01:00:22
But once the runtime, the loading of them in memory doesn't get shared, right?

701
01:00:22 --> 01:00:23
That's what Raziel was talking about.

702
01:00:23 --> 01:00:29
But I think even completely different apps can't really share the weights either, right?

703
01:00:29 --> 01:00:32
MARK MANDEL: The weights can be shared if the apps are owned by the same developer.

704
01:00:32 --> 01:00:33
FRANCESC CAMPOY: Yeah, that's what I mean.

705
01:00:33 --> 01:00:36
Like, if they're not, then they can't.

706
01:00:36 --> 01:00:37
But it would be great, in a sense.

707
01:00:37 --> 01:00:43
And you could-- you know, one can imagine, but it needs to be something way bigger than just a download.

708
01:00:43 --> 01:00:46
One could imagine, oh, I know I have the house.

709
01:00:46 --> 01:00:47
The weights are correct.

710
01:00:47 --> 01:00:48
Nobody's tampered with them.

711
01:00:48 --> 01:00:48
They're there.

712
01:00:48 --> 01:00:54
But it needs to be a service or something bigger than just, oh, there's a download somewhere in the disk.

713
01:00:54 --> 01:00:55
It's much more complicated.

714
01:00:55 --> 01:00:56
FRANCESC CAMPOY: OK, we are out of time.

715
01:00:56 --> 01:01:05
But I think this discussion brings us all the way to the start where we said, if it's foundation models on device, the model is in the OS.

716
01:01:05 --> 01:01:07
It's not a dependency on your app.

717
01:01:07 --> 01:01:09
Try that first.

718
01:01:09 --> 01:01:11
It doesn't contribute to your app size.

719
01:01:11 --> 01:01:14
And there's PCC as an option.

720
01:01:14 --> 01:01:18
Only if you want to bring in your own model, you can use Core AI, MLX.

721
01:01:18 --> 01:01:21
Of course, use evaluations framework for testing.

722
01:01:21 --> 01:01:27
And you need help with development, you can use agentic coding capabilities in Xcode.

723
01:01:27 --> 01:01:28
So thank you.

724
01:01:29 --> 01:01:30
So thanks, everyone.

725
01:01:30 --> 01:01:33
That brings us to the end of today's group lab.

726
01:01:33 --> 01:01:34
Thanks for joining us.

727
01:01:34 --> 01:01:36
We hope this was useful.

728
01:01:36 --> 01:01:45
I also want to say thank you to our panelists here as well as all the folks working hard behind the scenes to help this happen today.

729
01:01:45 --> 01:01:54
So as we mentioned earlier, if we didn't get your questions, please visit developer.apple.com slash forums where we can continue this discussion.

730
01:01:54 --> 01:02:02
and if you have bug reports, feature requests, head over to feedbackassistant.apple.com and share it with us there.

731
01:02:04 --> 01:02:10
Speaking of feedback, you should also receive an email with a survey link to let us know your experience at WWDC.

732
01:02:11 --> 01:02:14
We would love to incorporate your feedback for future events.

733
01:02:15 --> 01:02:19
So thanks again for joining us and hope you have a great WWDC.

734
01:02:19 --> 01:02:20
Namaskara.

735
01:02:21 --> 01:02:21
Thank you.
