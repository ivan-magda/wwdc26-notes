---
title: Apple Intelligence Group Lab
source: https://developer.apple.com/videos/play/wwdc2026/8011/
session: 8011
collection: wwdc2026
duration: 01:03:49
fetched: 2026-06-12
via: whisper.cpp ggml-large-v3 (session SD video audio)
transcription: machine-generated, no speaker diarization
---

> Machine transcription of the **Apple Intelligence Group Lab** SD video via whisper.cpp (`ggml-large-v3`).
> Group labs ship no Apple transcript; this is generated from the audio. Cues are timestamped segments, **not** speaker-attributed turns.

## Transcript

1
00:00:03 --> 00:00:08
Good morning. Welcome to the Apple Intelligence Group Lab. We're so glad to have you here.

2
00:00:09 --> 00:00:13
My name is Ernie Sveala. I'm an AI machine learning evangelist here at Apple.

3
00:00:13 --> 00:00:35
There have been some awesome announcements at WWDC for Apple Intelligence, Foundation Models Framework, Private Cloud Compute, Visual Intelligence, and a whole lot more. And of course, App Intents and Siri. I am joined by an awesome team of engineers who are going to help you with those questions, and why don't we begin by some quick introductions? Yeah, I'll start. My

4
00:00:35 --> 00:00:41
name's Matt, and I work on the Foundation Models Framework. Hi, I'm James. I work on App Intents.

5
00:00:42 --> 00:01:05
I'm Rob. I work on the Evaluations Framework. I'm Louis. I also work on the Foundation Models Framework. I'm Dan. I work on Siri and App Intents. Awesome. Thank you all. In addition to those you see here on stage, there's a whole slew of engineers behind the scenes working to answer your questions. Please submit those questions. If you have a code-related question or issue,

6
00:01:05 --> 00:01:26
definitely connect with us on the developer forums. That's a much better place for us to get into specific coding issues. But we really look forward to answering your questions today about all these exciting new updates. And if you have a bug or feature request, of course, go to feedbackassistant.apple.com. And yeah, we'd love to get your ideas and feedback on any bugs.

7
00:01:26 --> 00:01:27
Although, I don't know, bugs in our software?

8
00:01:27 --> 00:01:28
I haven't seen any.

9
00:01:28 --> 00:01:29
Anyway, all right.

10
00:01:29 --> 00:01:30
We have no bugs.

11
00:01:30 --> 00:01:31
No, not this year.

12
00:01:31 --> 00:01:32
On occasion, that might happen.

13
00:01:32 --> 00:01:35
Anyway, with that, we have a lot of questions.

14
00:01:35 --> 00:01:36
Let's get started.

15
00:01:36 --> 00:01:41
The first question we have today is from Mario Gravina.

16
00:01:41 --> 00:01:42
Thank you, Mario, for the question.

17
00:01:42 --> 00:01:56
The question is, what are the most useful Apple resources for learning and applying the new version 27 APIs and design changes in real apps, updated sample projects, migration guides, et cetera?

18
00:01:57 --> 00:01:58
That's a really good question.

19
00:01:58 --> 00:01:59
Thank you for that, Mario.

20
00:02:00 --> 00:02:05
Yeah, well, of course, there's WWDC videos, a lot of great documentation, a lot of sample apps.

21
00:02:06 --> 00:02:07
Any other suggestions, you guys?

22
00:02:07 --> 00:02:11
There's a dedicated sample section of the documentation page.

23
00:02:11 --> 00:02:14
Yeah, and there's definitely a lot of great technical articles as well.

24
00:02:15 --> 00:02:16
So a lot of great resources.

25
00:02:17 --> 00:02:21
I think on App Intents this year, I think we had five sessions.

26
00:02:22 --> 00:02:26
Most worked on a few – I think there was a few different sample apps that we put out.

27
00:02:26 --> 00:02:31
A lot of the sessions use them in the talks, so you can kind of see how they were built, how they're used.

28
00:02:31 --> 00:02:40
We also did a big push on our documentation this year, no for App Intents, I think for Foundation Models as well, that really show we built a lot this year.

29
00:02:40 --> 00:02:41
Yeah, definitely.

30
00:02:41 --> 00:02:49
And to kind of how you want to understand it, the docs are not just here is the API, it's how to use the API, how it benefits your users.

31
00:02:49 --> 00:02:51
Yeah, I think you made a good point.

32
00:02:51 --> 00:02:55
Like if it was in a session video, it will show up in that sample section.

33
00:02:56 --> 00:02:57
Yeah, no, great points.

34
00:02:57 --> 00:03:04
And obviously, WWDC from last year or previous years, lots of good material, too, especially if you're starting out, like, say, with App Intents.

35
00:03:04 --> 00:03:06
A lot of great foundational material as well.

36
00:03:07 --> 00:03:08
So definitely check that out.

37
00:03:09 --> 00:03:09
Awesome.

38
00:03:10 --> 00:03:11
Thank you, Mario, for that question.

39
00:03:11 --> 00:03:14
Next is from username Luke's Sape.

40
00:03:15 --> 00:03:16
Hopefully I'm saying that right.

41
00:03:17 --> 00:03:18
Nice name, by the way.

42
00:03:19 --> 00:03:23
My app doesn't fit any of the predefined schema types.

43
00:03:23 --> 00:03:25
I think we've heard this one a few times.

44
00:03:26 --> 00:03:32
Is it better to try to use the closest related types, or should I stick with not supporting the schema for now?

45
00:03:32 --> 00:03:34
My app is for tracking time.

46
00:03:34 --> 00:03:41
There are types for timers and for calendar events, but they don't quite fit within my app offers.

47
00:03:41 --> 00:03:43
That's a very good question.

48
00:03:43 --> 00:03:44
I can take that.

49
00:03:45 --> 00:03:47
So new this year is the introduction of app schemas.

50
00:03:48 --> 00:03:53
These are schemas that are fine-tuned and designed by Apple to integrate with our great system experiences.

51
00:03:54 --> 00:04:01
The cool thing about them is that as a developer, you can simply pick and choose the schemas that best match your app's capabilities.

52
00:04:01 --> 00:04:05
So you mentioned timer functionality, calendar.

53
00:04:05 --> 00:04:11
We have schemas for those actions, and you can simply pick and choose the actions that match your app's capabilities.

54
00:04:11 --> 00:04:19
If your app has functionality that is outside of the schema, well, you can also combine it with custom app intents that you can write for those actions.

55
00:04:20 --> 00:04:27
And when you write an app intent, that action becomes available in many places across the system, the Shortcuts app, Spotlight, and more.

56
00:04:28 --> 00:04:36
Now, it's important to note that for the new Siri AI integration, for integration with that, you do need to adopt one of our app schemas.

57
00:04:36 --> 00:04:44
Now, the other cool thing, too, is a lot of our schemas are sort of designed for certain specific types of actions.

58
00:04:45 --> 00:04:50
But we also have a domain called the system domain, which is more generic and broad.

59
00:04:50 --> 00:04:59
So if you have functionality in the search space, for instance, apps like e-commerce or maybe food ordering, well, you can integrate with system.search.

60
00:04:59 --> 00:05:07
And what that will do is it will allow your customers to use Siri to basically integrate directly into your app's search capabilities.

61
00:05:07 --> 00:05:20
So you can say something like, you know, order me a bicycle on my favorite marketplace app, and it will deep link to your app directly into your search capabilities so that you can continue your ordering flow through your app.

62
00:05:20 --> 00:05:22
We're really excited about this.

63
00:05:22 --> 00:05:35
There's also another schema in this domain called system.open that allows you to open specific entities that can be donated to Spotlight, and Siri will be able to open those entities directly in your app.

64
00:05:36 --> 00:05:41
And if none of these schemas satisfy your needs, well, we also have app shortcuts.

65
00:05:42 --> 00:05:43
James, do you want to talk a bit about app shortcuts?

66
00:05:44 --> 00:05:47
Sure. So app shortcuts is an API that we've had for a while.

67
00:05:47 --> 00:05:55
It allows you to add phrases for an app intent, and when you do that, the action becomes available in the system.

68
00:05:55 --> 00:06:00
So it's automatically available with Siri, shortcuts, Spotlight, and a few other places.

69
00:06:00 --> 00:06:05
You can even use it on the iPad if you want to tap on an Apple Pencil.

70
00:06:05 --> 00:06:06
It all works with app shortcuts.

71
00:06:07 --> 00:06:17
And it's a great feature, as Dan mentioned, when you have a specific action in your app that you really want users to be able to do.

72
00:06:17 --> 00:06:18
and it has a memorable phrase.

73
00:06:19 --> 00:06:29
So App Shortcuts does require a phrase, but for these very specific actions, these kind of hero use cases in your app, they are super impactful.

74
00:06:29 --> 00:06:31
And then, yeah, Dan, you mentioned a bunch of other things.

75
00:06:32 --> 00:06:33
We have a ton of APIs.

76
00:06:33 --> 00:06:34
We have a ton of different experiences.

77
00:06:36 --> 00:06:37
It's kind of a choose-your-own-adventure.

78
00:06:37 --> 00:06:38
Start with the schemas.

79
00:06:38 --> 00:06:40
If something makes sense, use it.

80
00:06:41 --> 00:06:44
And then there's a ton of other experiences as well.

81
00:06:44 --> 00:06:55
And the documentation this year is great for finding out what functionality is available through app schemas, also how app shortcuts work, please check out the developer documentation to find out how you can integrate with Siri.

82
00:06:56 --> 00:07:19
That's awesome. Great. All right. Well, thank you. Great question. Moving on to our next one. This is from, gosh, let's see, Quasitoic. Hopefully I'm saying that correctly. The message or the question, please, is my app's core entities and actions don't map to any predefined schema domain like the apps of many others?

83
00:07:20 --> 00:07:23
What's the best way to bring them to the new agentic Siri today?

84
00:07:23 --> 00:07:27
What should I do now to be ready as the platform evolves?

85
00:07:28 --> 00:07:32
And how should we think about their place in the agentic experience?

86
00:07:32 --> 00:07:33
Question.

87
00:07:34 --> 00:07:36
I think we covered a little bit of that.

88
00:07:36 --> 00:07:37
A little bit of the last one, right?

89
00:07:37 --> 00:07:37
Yeah.

90
00:07:37 --> 00:07:48
Yeah, you know, I think we have these, take a look at the schemas, see if, as Dan mentioned, there are, you know, you can kind of pick and choose parts of these.

91
00:07:48 --> 00:08:13
domains. If something makes sense, implement it. By conforming to an entity schema and kind of donating and interacting with Spotlight, you do get a lot of other benefits. Even if maybe one of the intents doesn't make sense in your application, you still can use Siri AI to ask questions about the content of these or take action on it using any view annotation API. So there's a little bit

92
00:08:13 --> 00:08:33
there. Right. Yeah. No, that's great. Thank you, James, for that. All right. Next question from Indigo J, another nice username. Epitense exposes an app's actions to Siri shortcuts, but there's no API for a third-party app to invoke another app's intents directly.

93
00:08:34 --> 00:08:47
Siri shortcuts is the orchestrator. Can an app act at its own orchestrator and drive other apps, or does cross-app action still have to route through Siri shortcuts?

94
00:08:47 --> 00:08:49
That's a very good question.

95
00:08:50 --> 00:08:50
Thank you.

96
00:08:51 --> 00:08:58
So new this year, we have a system orchestrator that can take action from app intents from many different apps across the system.

97
00:08:58 --> 00:09:04
Now, one of the important things for us to consider with this is when it comes to privacy and safety.

98
00:09:04 --> 00:09:10
One of our key concepts of Apple intelligence is that it is AI, personal, and private to you.

99
00:09:11 --> 00:09:20
When you integrate with app intents, you're basically making actions from your app available to the system, and you're also modeling content in the form of app entities.

100
00:09:20 --> 00:09:30
Now, the cool thing about our semantic index and our spotlight integration is when you index content from your app, you're making it available to the system and to the system alone.

101
00:09:30 --> 00:09:37
So, you know, it's not like you're sharing data from your app that could potentially expose other apps from accessing this data.

102
00:09:38 --> 00:09:41
It's all sandboxed to you and the system and the system alone.

103
00:09:41 --> 00:09:45
Now, there is also a concept this year called transferable.

104
00:09:46 --> 00:09:55
Transferable is an API that you can adopt as a developer that allows you to control how you can share data from your app to other apps in the system.

105
00:09:55 --> 00:10:06
The API essentially has bidirectionality support, meaning you can choose which data you want to export out of your app and which data you want to import from other apps.

106
00:10:06 --> 00:10:15
So it's a really neat API that makes the boundaries clear between what is data that your app owns versus data that you can import from somewhere else.

107
00:10:17 --> 00:10:17
Excellent.

108
00:10:17 --> 00:10:18
Yeah, great stuff.

109
00:10:18 --> 00:10:19
Yeah, great year.

110
00:10:20 --> 00:10:21
A lot of great new stuff, folks.

111
00:10:21 --> 00:10:22
Great question.

112
00:10:23 --> 00:10:24
All right, next one.

113
00:10:26 --> 00:10:26
TanerNDT.

114
00:10:27 --> 00:10:36
And the question is, with the new Siri in iOS 27, can my third-party app take part in a multi-turn conversation?

115
00:10:36 --> 00:10:37
Another great question.

116
00:10:37 --> 00:10:43
Or am I still limited to the discrete actions and entities I expose through app intents?

117
00:10:43 --> 00:10:44
I could take this one.

118
00:10:44 --> 00:10:45
Yeah, sure.

119
00:10:45 --> 00:10:45
Yes.

120
00:10:45 --> 00:10:46
Please.

121
00:10:46 --> 00:10:49
This is what the schemas provide.

122
00:10:49 --> 00:10:52
You can take action from your app.

123
00:10:52 --> 00:10:54
You could ask questions about your entities.

124
00:10:55 --> 00:10:56
You can do follow-ups.

125
00:10:57 --> 00:11:00
It is – I've been working on it.

126
00:11:00 --> 00:11:03
I'm very happy to finally show it to people.

127
00:11:03 --> 00:11:07
I want people to take a look at the betas, try it in your app.

128
00:11:07 --> 00:11:14
Um, it's, it's a great way that I, we believe your users will be able to interact with your applications.

129
00:11:14 --> 00:11:16
You know, it's, it's your app, it's your content.

130
00:11:16 --> 00:11:24
They have, um, kind of a deep understanding of this and to just be able to ask natural questions about it and do these followups is it's, it's just really awesome to see.

131
00:11:25 --> 00:11:25
Yeah.

132
00:11:25 --> 00:11:30
And I'll add on that, you know, at the heart of it, you have a large language model that is processing all of the input.

133
00:11:30 --> 00:11:36
So it gives users, you know, more natural capabilities for how to express and take actions with your app.

134
00:11:37 --> 00:11:41
Previously, the commands had to be very specific to form into a shape.

135
00:11:41 --> 00:11:55
Now, with AppSchemas, you have a lot more flexibility into how your users can phrase requests and have our model sort of parse that information and pick the right action for your users.

136
00:11:55 --> 00:11:56
Yeah, great stuff.

137
00:11:56 --> 00:12:01
Great videos, by the way, from this dub dub on these topics.

138
00:12:01 --> 00:12:05
So definitely encourage you, of course, to watch those as well.

139
00:12:05 --> 00:12:08
Yeah, a lot of great, great sample code to go along with that.

140
00:12:08 --> 00:12:10
All right, well, thank you for that question.

141
00:12:10 --> 00:12:15
Moving on, Zulfi Shah, I believe, is the way we say that username.

142
00:12:16 --> 00:12:26
Is it okay to mix and match non-schema app entities in Tense with some that match different schemas from different domains all in the same app?

143
00:12:26 --> 00:12:29
I'm loving all these schema questions, by the way.

144
00:12:30 --> 00:12:33
The answer is absolutely yes.

145
00:12:33 --> 00:12:40
With schemas, our intention is that you pick and choose the schemas that work for your app, matching your app's capabilities.

146
00:12:41 --> 00:12:50
So if you have an app that can do calendar actions, but it can also do reminders actions, please pick schemas from any of these domains.

147
00:12:50 --> 00:12:56
The cool thing is that you can mix and match them, and they can work together to deliver full holistic experiences.

148
00:12:56 --> 00:13:05
Yeah, and it's also – this is not a binary choice where you have to use schemas or you have to use app shortcuts or search in app or something.

149
00:13:05 --> 00:13:06
They play nicely together.

150
00:13:06 --> 00:13:08
There are going to be some experiences.

151
00:13:09 --> 00:13:10
You're the developer of your app.

152
00:13:10 --> 00:13:19
You understand your users, and you should be – we expect people to have little bits of a schema, little bits of app shortcuts.

153
00:13:19 --> 00:13:23
It really builds this holistic experience for users to use your app.

154
00:13:23 --> 00:13:24
Yeah, okay.

155
00:13:24 --> 00:13:25
That's great.

156
00:13:25 --> 00:13:26
All right.

157
00:13:26 --> 00:13:31
Moving on, next question from abhigh27.

158
00:13:31 --> 00:13:32
I'm not sure how to say that one.

159
00:13:33 --> 00:13:41
Is there a practical time work budget for foundation models during one background wake?

160
00:13:41 --> 00:13:46
If no fixed number, how should apps safely chunk work?

161
00:13:48 --> 00:13:49
Yeah, I can take that one for sure.

162
00:13:49 --> 00:14:12
So with the on-device system language model with foundation models, if you're running in the foreground, app like there's never any like rate limiting or anything like that unlimited requests right if it's running in the background we do apply uh some throttling if the system is under heavy load and so what you can do with the api is uh catch the rate limited error so if you're getting the

163
00:14:12 --> 00:14:30
rate limited error with the on-device model that's the way that the system is telling you that your request uh even though it ran on device that the system was under heavy load you should try again later. Okay. That makes sense. You know, I have to say, I was so excited when I learned we're going to be making PCC available to developers. It's an awesome model. Yeah. Anyway, that's really

164
00:14:30 --> 00:14:45
great. So yeah. We'll talk more about that later, I bet. Yeah. Awesome. All right. Moving on. Also from abhi27, the question is, what exact air types codes indicate foundation model throttling? Okay.

165
00:14:45 --> 00:15:02
I think you may have addressed that a little bit in the previous question. System busy, thermal pressure, quota exhaustion, which are retriable versus terminal. Is nuanced error reporting available? Yeah, I think, Matt, if you want to talk more about our handling in general, yeah.

166
00:15:02 --> 00:15:23
Yeah, for sure. So this year we came out with the language model protocol. And as part of that, and kind of encompassing common errors that a lot of models will see, we have the language model error. And so there's a lot of those common use cases of rate limiting and refusals, those sort things but uh specific with the pcc model like you guessed coming up we also have quota usage

167
00:15:23 --> 00:15:45
so you can get a signal through the property on that specific model saying hey are you getting close to that limit are you not um and we also have errors a part of that that will uh throw specifically for that model saying hey during this this prompt you have a quota exhaustion and you can handle it from there yeah that's great yeah so you can find this in the documentation documentation

168
00:15:45 --> 00:15:48
Yeah, it has a great session. Plug that real quick.

169
00:15:49 --> 00:15:52
So, yeah, I definitely check those out. They're really great.

170
00:15:52 --> 00:15:59
Yeah, it's great to hear developers bringing those kinds of questions because, you know, it's the rainy day situations you've got to prepare for in your app, too, right?

171
00:15:59 --> 00:16:01
Not just when everything goes well, right?

172
00:16:01 --> 00:16:03
So, yeah. Oh, great. Thank you for that question.

173
00:16:04 --> 00:16:07
All right, next one from Thub, T-H-U-B.

174
00:16:08 --> 00:16:09
Hopefully I got that right here.

175
00:16:10 --> 00:16:16
How to avoid language model session dot generation error dot guardrail violation.

176
00:16:16 --> 00:16:24
In my tests, I am getting quite often guardrail violation errors for prompts I cannot see any issues with.

177
00:16:25 --> 00:16:27
Hmm, I'm thinking the evaluations framework.

178
00:16:28 --> 00:16:28
Yeah.

179
00:16:28 --> 00:16:28
Yeah.

180
00:16:28 --> 00:16:33
We ran into this a bunch when we were building samples for ours.

181
00:16:34 --> 00:16:40
There's a model judge evaluator in the evaluations type, and you can create safety rubrics.

182
00:16:41 --> 00:17:02
inside of that where you just give it a scale like let's say one to four um how safe is this does it have some safety implications but if you actually ask the model uh you know or even ask you know a different model let's say if you're you know you're getting this a lot in the on-device model you could use the pcc model um to kind of check that it being a larger model it could kind

183
00:17:02 --> 00:17:12
of evaluate those and let you know if there are safety errors but the guardrail violation is there to help you avoid unsafe responses in your app.

184
00:17:12 --> 00:17:13
No, great.

185
00:17:13 --> 00:17:14
Yeah, great advice.

186
00:17:14 --> 00:17:16
And I want to put a plug in for the evaluations framework.

187
00:17:17 --> 00:17:17
Oh, my gosh.

188
00:17:17 --> 00:17:23
It's one of the biggest questions when we would do workshops was about how do I test my intelligence-based features?

189
00:17:23 --> 00:17:30
And I have to say what we've done between the model judges, quantitative, qualitative metrics, it's really, really, really great stuff.

190
00:17:30 --> 00:17:31
I don't know of anybody else doing that.

191
00:17:31 --> 00:17:32
I'm a fan, but.

192
00:17:32 --> 00:17:33
Yeah.

193
00:17:35 --> 00:17:36
A little biased there.

194
00:17:36 --> 00:17:38
No, definitely check it out.

195
00:17:38 --> 00:17:40
And thank you for that question.

196
00:17:40 --> 00:17:41
Really, really appreciate that one.

197
00:17:42 --> 00:17:43
Okay, moving on.

198
00:17:43 --> 00:17:53
Tainard NDT, in iOS 27, can the Foundation Models Framework reason over video or work with a video model?

199
00:17:53 --> 00:17:57
Or is multi-modal input limited to still images?

200
00:17:59 --> 00:17:59
That's a great math.

201
00:17:59 --> 00:18:00
That might be a good question for you.

202
00:18:00 --> 00:18:01
Yeah, I'll take that one.

203
00:18:02 --> 00:18:03
So back to that language model protocol.

204
00:18:04 --> 00:18:11
So as models and people come to the Foundation Models Framework, we have them have capabilities that they support.

205
00:18:11 --> 00:18:12
And one of these is vision.

206
00:18:12 --> 00:18:18
So if you add that as a part of the model, the framework can handle to say, yes, this is approved for this specific model.

207
00:18:18 --> 00:18:21
And if so, it can continue on and do that sort of thing.

208
00:18:21 --> 00:18:32
Specific to reasoning in Apple's models, the PCC model has that where you have the capability to specify light, moderate, and deep, and reason over those things.

209
00:18:32 --> 00:18:42
There's another great session talking all about that, where we have a new part of the prompt where you can have an attachment and bring in those images that you want to look with.

210
00:18:42 --> 00:18:52
And we've shown that with my dog, Sully, which has been great to test with all of us and see that the model performs very well.

211
00:18:52 --> 00:18:54
So yeah, those are the sort of things that it can do.

212
00:18:54 --> 00:18:59
That's sort of the key thing, that we're making the framework, the Foundation Models framework, extensible this year, right?

213
00:18:59 --> 00:19:09
But also this year, we added support for image input, both with the updated on-device model we have, and then, of course, with the new server model running on PCC.

214
00:19:09 --> 00:19:28
But in terms of the extensibility, with the language model protocol letting you really bring any model you want to the Foundation Models framework, and the protocol itself is sort of open-ended, where if you have custom modalities, for example, like video, that we have the custom segment API that you can really sort of build whatever things you

215
00:19:28 --> 00:19:51
on top of it yeah the framework doesn't uh you know kind of hold you into you have to be within these certain things if your model is very specific like it can smell something or crazy you can use a custom segment to still reach the users and um with it being open source also announced um yeah i have even more capabilities where you can run on the server with swift and

216
00:19:51 --> 00:20:11
those sort of things if i may i mean totally i mean that was a lot of great information on foundation models but um again if you're looking for a one-shot approach i think everything they said kind of makes sense but if you're willing to do processing ahead of time or if you want to build a tool you could build a tool that uses apple's other frameworks that could get a transcript

217
00:20:11 --> 00:20:32
of that video um you know maybe what it's hearing audio describing it uh there's a lot of good frameworks in our api uh there's a lot of good apis in our frameworks uh that allow you to extract those and then you just feed that text description right um and at that point i mean if you're doing summarization or something like that the on-device model is great for that yeah there is a great um

218
00:20:32 --> 00:20:51
what's new in image understanding with the vision framework oh yeah it's really awesome yeah and it's like you build a tool and it's like the tool does that processing and it feeds it into the model yeah no that's that's that's really great stuff i mean the open source oh my gosh that was such a huge announcement i'm like so excited to hear that absolutely for the developer

219
00:20:51 --> 00:21:14
community right i mean yeah apple's behind that i mean that's huge but the extensibility piece yeah that's yeah really really powerful as well so cool and hey another quick plug for valuations framework i know i suck at sales tools right like you know one of the problems i always had is like what tool got called when or you know yeah now it's valuation i'm able to see that i mean yeah

220
00:21:14 --> 00:21:29
that oh my gosh you you feed your um you feed your evaluation the transcript from the language model session. And then we have a tool call evaluator that'll allow you to build assertions against that transcript and see when it's getting called, what values are being sent to it, etc.

221
00:21:29 --> 00:21:31
And if you're a visual, I like visuals.

222
00:21:32 --> 00:21:38
We have instruments now hooked up to also see as it goes, so you can really see everything that got called.

223
00:21:38 --> 00:21:44
It really helps. So you get kind of like batch evaluation, and then you can get kind of real-time what's going on.

224
00:21:44 --> 00:21:49
Yeah, that's really cool. All right, well moving on, next question.

225
00:21:49 --> 00:22:17
is from Kyle BSHR. Are app intent schemas something that give your intents an advantage when integrating with Siri, or can any intent entity be discovered by Siri? Siri AI the same way. Notably, there's no finance schemas. Oh, that's a very good question. We've heard this one. Yeah, I could take this, and Dan, I'm sure you'll jump in here too. I think advantage is

226
00:22:17 --> 00:22:49
probably not the right way of thinking about this you know using schemas is the way that siri ai can reason about the actions and entities in your application um it's how uh advantage is really throwing me here i i think one similar question we get about this is how can you influence here right yeah and for that we have a ton of new apis that can help you uh tell siri about what actions

227
00:22:49 --> 00:22:50
are happening in your application.

228
00:22:51 --> 00:23:01
So we have new intent donation, like a new intent donation system that's used for when a user is using your app, you know, they're tapping around.

229
00:23:01 --> 00:23:07
If a user performs a similar action that has a corresponding app intent, you donate that intent.

230
00:23:08 --> 00:23:13
And that gets donated to a kind of temporary transcript that Siri can use to learn.

231
00:23:13 --> 00:23:32
So if you're kind of consistently messaging the same person and then you use Siri to, like, hey, message Dan, it will know, Siri will learn that, oh, you might want to message Dan in Unicorn Chat or many sample, space-themed sample apps this year.

232
00:23:32 --> 00:23:43
Yeah, also add on that, you know, we have the app shortcuts API that allows you, developer, to specify the phrases that can activate your app intents.

233
00:23:43 --> 00:23:58
But if you adopt one of our app schemas, the cool thing there is that Apple has done the heavy lifting of essentially providing those sample phrases, of training the model and making sure that we can deliver a consistent experience across apps when you integrate with a schema.

234
00:23:58 --> 00:24:11
So if you're messaging using the first-party messages app or you're messaging using many other third-party messages apps that are available out there that do conform to our schema, you get a consistent experience no matter which app you use.

235
00:24:11 --> 00:24:25
And that, I think, is really powerful about what we're building here, is that we're building an ecosystem where users can use Siri AI to take actions across many apps in the system and tailor the experience to your users.

236
00:24:25 --> 00:24:30
You may message a coworker using a very specific messaging app.

237
00:24:31 --> 00:24:35
And so when you send a message to that coworker, the system will learn about these integrations like James was saying.

238
00:24:36 --> 00:24:39
And so it won't even try to use iMessage to send that message.

239
00:24:40 --> 00:24:43
It will use that app that you always use to communicate with your coworker.

240
00:24:43 --> 00:24:44
Just one more small plug.

241
00:24:45 --> 00:24:50
You know, we know we have a lot of developers listening.

242
00:24:51 --> 00:24:53
Building app intents takes a little bit of work.

243
00:24:53 --> 00:24:56
You have to define a lot of your properties.

244
00:24:56 --> 00:24:59
There's a lot of code that you have to write, which we all write code.

245
00:25:00 --> 00:25:06
By using app schemas, it actually lets you write a little bit less code for this.

246
00:25:07 --> 00:25:11
Because, as Dan mentioned, Apple has done a lot of that heavy lifting for you.

247
00:25:11 --> 00:25:16
So some things that you have to provide in kind of custom intents, you no longer have to provide because we do it for you.

248
00:25:16 --> 00:25:20
So it's another developer benefit as well of hopefully all the user benefits.

249
00:25:20 --> 00:25:21
Totally.

250
00:25:21 --> 00:25:31
You know, I have to say, too, like going to app intents workshops, working with developers who are new to app intents and when they get it, that aha moment and seeing how they're like, oh, wow.

251
00:25:32 --> 00:25:38
It's like, you know, you converted my device into my, you know, the app knows, the device knows so much more about my app now.

252
00:25:38 --> 00:25:40
And just seeing that light bulb turn on.

253
00:25:40 --> 00:25:41
Yeah, it's really exciting.

254
00:25:41 --> 00:25:44
And then when you add all the new capabilities, it's pretty – it's fun.

255
00:25:44 --> 00:25:53
And the cool thing, too, to see is when they add the schema to their app intent, they get to delete a bunch of code because that's no longer needed, and developers love deleting code.

256
00:25:54 --> 00:25:57
I love deleting code, especially when it's not mine.

257
00:26:00 --> 00:26:00
Awesome.

258
00:26:00 --> 00:26:01
All right, well, thank you for that question.

259
00:26:01 --> 00:26:05
Next, moving on, Interferon.

260
00:26:05 --> 00:26:05
Ooh, that's a cool one.

261
00:26:06 --> 00:26:07
Okay, cool username here.

262
00:26:09 --> 00:26:15
Can I use Image Playground to generate photorealistic images inside my app?

263
00:26:15 --> 00:26:17
I think I could take this one.

264
00:26:17 --> 00:26:19
Short answer is yes, you can.

265
00:26:19 --> 00:26:23
A lot of great new additions to the Image Playground API.

266
00:26:24 --> 00:26:28
There's a really great video this year, this dub-dub.

267
00:26:28 --> 00:26:35
And basically what you do is you'd use the imageplaygroundstyle.all as a parameter there.

268
00:26:36 --> 00:26:38
So, yeah, please check that out.

269
00:26:38 --> 00:26:41
Great video, and I'm sure there's some great documentation on that as well.

270
00:26:41 --> 00:26:43
Because that's running on private cloud compute now, right?

271
00:26:43 --> 00:26:44
That's right.

272
00:26:44 --> 00:26:51
Yeah, you get the advantage of the more powerful model and the API itself.

273
00:26:51 --> 00:26:52
So great, great.

274
00:26:52 --> 00:26:53
Thanks for that question.

275
00:26:55 --> 00:26:57
All right, moving on here.

276
00:26:58 --> 00:26:59
Louis 42.

277
00:26:59 --> 00:27:00
Hi, Louis 42.

278
00:27:00 --> 00:27:01
Thank you for your question.

279
00:27:02 --> 00:27:02
Where's Louis 41?

280
00:27:03 --> 00:27:05
Yeah, where's Louis 41?

281
00:27:05 --> 00:27:06
Is Louis 41 in the house?

282
00:27:07 --> 00:27:07
Is he all right?

283
00:27:08 --> 00:27:15
I'm confused by the difference between indexed entity and things with defined entities.

284
00:27:16 --> 00:27:18
Example, AppSchema.BooksEntity.

285
00:27:18 --> 00:27:25
Should we use indexed entities for everything that isn't a book, calendar event, et cetera?

286
00:27:25 --> 00:27:28
Or are these serving totally different purposes?

287
00:27:29 --> 00:27:29
Great question.

288
00:27:30 --> 00:27:31
That is a great question.

289
00:27:32 --> 00:27:35
So the schema defines the shape of the content.

290
00:27:35 --> 00:27:37
It defines the properties that it can have.

291
00:27:37 --> 00:27:43
So if we're talking about a book, it can have the content of the book, the title of the book, the author, and so on.

292
00:27:44 --> 00:27:51
Indexed entity is a separate API that essentially allows you to index that content into the system's semantic index.

293
00:27:51 --> 00:28:01
So the content can essentially be retrieved by Siri AI when users are performing actions like, I want to read a book by a certain author.

294
00:28:01 --> 00:28:10
This is the API that allows the system to essentially look into that index and find the right entity that represents that specific content.

295
00:28:10 --> 00:28:14
So in a way, they actually complement one another.

296
00:28:14 --> 00:28:29
If you adopt an app schema and you also make it conform to the indexed entity protocol and you then make that call at runtime to essentially index content from your app into the semantic index, you get the best Siri AI experience for your customers.

297
00:28:30 --> 00:28:37
Back to the building blocks, you can use index entity as well for your custom entities.

298
00:28:37 --> 00:28:39
And you can donate them to Spotlight.

299
00:28:40 --> 00:28:42
The advantage there is they show up in Spotlight.

300
00:28:42 --> 00:28:47
And you can have a system.open intent.

301
00:28:48 --> 00:28:53
So if you go from Spotlight and you tap a result that's in there, you can kind of deep link directly into your app.

302
00:28:54 --> 00:29:02
Another kind of advantage of the schemas that I mentioned before, the deleting code, is if you've worked with Spotlight before, you have these indexing keys.

303
00:29:03 --> 00:29:05
So, you know, the title of a book is the display name.

304
00:29:06 --> 00:29:08
For app schemas, we've done all that work for you.

305
00:29:08 --> 00:29:09
So you don't have to worry about it.

306
00:29:10 --> 00:29:15
Use one API to donate, and we handle everything kind of for you in the back end.

307
00:29:15 --> 00:29:23
Yeah, that piece actually is really nice because when you're using the app schema, you don't have to worry about trying to figure out what's the right key value pair for Spotlight.

308
00:29:24 --> 00:29:26
Like, Apple does the heavy lifting for you.

309
00:29:26 --> 00:29:39
As you developer, you have to basically focus on the minimum amount of adoption needed, which is, you know, you define your app entity, it conforms to schema, and then you simply provide values for those properties, and we take care of the rest.

310
00:29:41 --> 00:29:42
No, great points.

311
00:29:42 --> 00:29:52
And, you know, to plus one Dan's point about we've done the heavy lifting, I mean, oh, my gosh, the engineering that went into those domains, right, schema, I mean, it's a lot.

312
00:29:52 --> 00:29:56
So you're really getting advantage of all that engineering effort from Apple, right?

313
00:29:56 --> 00:29:59
So, yeah, really, really great stuff.

314
00:30:00 --> 00:30:00
Well, cool.

315
00:30:00 --> 00:30:01
Thank you for that question.

316
00:30:01 --> 00:30:02
Moving on.

317
00:30:04 --> 00:30:07
Username, let's see, EasyFranca2015.

318
00:30:08 --> 00:30:09
Thank you for the question.

319
00:30:10 --> 00:30:12
I love the evaluation framework session.

320
00:30:12 --> 00:30:14
Rob, plug for Rob here.

321
00:30:14 --> 00:30:16
Did you write this question, Ernie?

322
00:30:16 --> 00:30:16
Yeah.

323
00:30:17 --> 00:30:18
He paid me $20.

324
00:30:18 --> 00:30:19
I'm joking.

325
00:30:19 --> 00:30:20
Slip a few bills.

326
00:30:22 --> 00:30:25
It's both a professional developer and an academic researcher.

327
00:30:25 --> 00:30:27
Oh, well, thank you for being an academic researcher.

328
00:30:27 --> 00:30:39
I'm curious whether Apple envisions the framework being used for academic research, such as collecting metrics, generating evidence for peer-reviewed studies in addition to product evaluation.

329
00:30:39 --> 00:30:41
That's really interesting.

330
00:30:42 --> 00:30:46
I think there's a lot in what kind of academic research you could do.

331
00:30:47 --> 00:30:51
I think what it comes down to is what it is you're measuring.

332
00:30:52 --> 00:30:59
The evaluations framework was designed not just for evaluating language models, but really any stochastic system.

333
00:30:59 --> 00:31:08
So, you know, if you have a machine learning model, a more traditional model like linear regression or classifier, evaluations can absolutely be adopted for that.

334
00:31:09 --> 00:31:11
It's just a matter of implementing the framework.

335
00:31:12 --> 00:31:17
We split our metric collection into a couple of phases.

336
00:31:17 --> 00:31:26
So you iterate over a series of samples, and you feed that into kind of the function that is generating the model's response.

337
00:31:26 --> 00:31:31
And then you use a series of evaluators that will calculate those measurements.

338
00:31:31 --> 00:31:45
So if this is something that, you know, the metrics you want to collect is something that can be measured in those evaluators, and it can be scenario-based, so you're feeding it in a collection of input samples, then I would say yes.

339
00:31:45 --> 00:31:49
It doesn't matter whether you're doing product development or you're doing research.

340
00:31:50 --> 00:31:51
You could use this.

341
00:31:51 --> 00:31:56
But, you know, we modeled this over, you know, a lot of model providers have benchmarks that they submit.

342
00:31:56 --> 00:31:59
And they say, you know, this model performs this well on this benchmark.

343
00:32:00 --> 00:32:01
This model performs this well on this benchmark.

344
00:32:02 --> 00:32:13
We wanted you to be able to create your own app-specific or scenario-specific benchmark specific to what you're trying to do, whether that's research or product development.

345
00:32:14 --> 00:32:14
Yeah.

346
00:32:15 --> 00:32:15
Totally.

347
00:32:16 --> 00:32:23
And I have to say, oh, yeah, I was just going to say really quick, the three sessions we have, right, you get the introduction, you get the more advanced features.

348
00:32:23 --> 00:32:26
I think the synthetic data capability we provide is freaking awesome.

349
00:32:27 --> 00:32:27
Yeah.

350
00:32:27 --> 00:32:31
And then the hill climbing, right, like how do you incrementally keep improving things.

351
00:32:31 --> 00:32:34
So it really is kind of a nice three-session set.

352
00:32:34 --> 00:32:34
Yeah, Matt.

353
00:32:34 --> 00:32:45
I was going to say that Xcode integration, too, showing visuals also really helps maybe to provide that kind of academic side where you can kind of see graphically everything that's happening as well.

354
00:32:45 --> 00:32:47
Yeah, and that's actually great.

355
00:32:47 --> 00:32:49
And you can save those reports.

356
00:32:49 --> 00:32:49
You can export them.

357
00:32:50 --> 00:32:51
They're all JSON-based data structures.

358
00:32:52 --> 00:32:54
So if you want to feed it into another workflow.

359
00:32:55 --> 00:33:02
But yeah, there's a lot of work going on in that academic research today specifically.

360
00:33:02 --> 00:33:08
So if it's non-deterministic output or responses to a system, then evaluations is a good fit.

361
00:33:08 --> 00:33:09
Yeah, that's great.

362
00:33:09 --> 00:33:10
Great question.

363
00:33:10 --> 00:33:11
Thank you for that.

364
00:33:11 --> 00:33:14
All right, next question is from Li Shuang Quan.

365
00:33:15 --> 00:33:16
Hopefully I'm saying your name.

366
00:33:16 --> 00:33:44
username correctly here. This year, Siri can understand screen context. Can Siri also understand context on CarPlay screen when connected? Also, can Siri platform actions like play a song based on user voice input, like Siri play song on row two, column one? Does this also apply to CarPlay? Well, that's interesting. We haven't really talked a lot about on-screen context today,

367
00:33:44 --> 00:33:45
So I'm excited to talk about that.

368
00:33:46 --> 00:33:57
It's one of the three pillars of the new Siri AI is Siri can understand the content that's visible on your screen and take action based on that relevant context.

369
00:33:57 --> 00:33:58
It's really powerful.

370
00:33:58 --> 00:33:59
We have great APIs for that.

371
00:34:00 --> 00:34:02
We have integration with NSUserActivity.

372
00:34:02 --> 00:34:08
If you know of the old NSUserActivity API that's been around for a while, we integrate directly with that.

373
00:34:08 --> 00:34:16
But we also have new ViewAnnotations API so that you can annotate content that is on screen with your entity-conforming schemas.

374
00:34:17 --> 00:34:24
I'm just really excited for people, for developers to see this work, for users to see this work.

375
00:34:25 --> 00:34:26
It's just cool.

376
00:34:26 --> 00:34:34
It's like you build the Swift UI view, you have your entities, you attach them, you start speaking, and it does exactly what you want.

377
00:34:35 --> 00:34:38
It's just, you have sessions, that's why they're in there.

378
00:34:38 --> 00:34:40
It's just really, really powerful.

379
00:34:41 --> 00:34:41
It's natural.

380
00:34:41 --> 00:34:43
It's kind of a natural way of interacting.

381
00:34:43 --> 00:34:44
Yeah, totally.

382
00:34:44 --> 00:34:48
And what about the car play part of that question?

383
00:34:48 --> 00:34:49
No.

384
00:34:50 --> 00:34:50
Sorry.

385
00:34:51 --> 00:34:57
If you have specific things you're looking for, we always take, you can file bug reports for your enhancement requests.

386
00:34:58 --> 00:34:58
Yes.

387
00:34:58 --> 00:35:04
But I will also say that on the car, you know, we want to make sure that you're focusing on the road, not on the content on your screen.

388
00:35:04 --> 00:35:05
Yes, totally.

389
00:35:05 --> 00:35:07
Yeah, no, that's a good point.

390
00:35:07 --> 00:35:11
But, yeah, definitely, you know, for if you find a bug.

391
00:35:12 --> 00:35:12
Okay, we have bugs.

392
00:35:14 --> 00:35:18
By all means, file a feedback request, but also enhancements.

393
00:35:18 --> 00:35:24
You know, we'd love to hear from you, and we take that very seriously, your inputs from the developer community and how we prioritize new features in the work.

394
00:35:24 --> 00:35:32
So, yeah, please take the time and file that about requesting that support for CarPlay with some safety features, of course, like Dan mentioned.

395
00:35:32 --> 00:35:35
Got to keep those hands on the wheel, you know.

396
00:35:35 --> 00:35:37
And don't file the feedback while you're driving.

397
00:35:37 --> 00:35:39
Yeah, do that while you're driving, please.

398
00:35:39 --> 00:35:42
All right, next question from Chaz K.

399
00:35:43 --> 00:35:49
Are we going to be able to use App Intense with HomePod, since currently there is not a beta for HomePod?

400
00:35:49 --> 00:35:56
The new Siri AI is available on iPhone, iPad, Mac, and Vision OS.

401
00:35:57 --> 00:35:58
It is not available on HomePod.

402
00:35:58 --> 00:35:59
Not on HomePod. Okay.

403
00:36:00 --> 00:36:09
Yeah, but your existing app shortcuts have worked on HomePod for a while, so we still have options for you to call into your app.

404
00:36:09 --> 00:36:12
Right. No, that's great to know. I love my HomePod.

405
00:36:13 --> 00:36:15
Okay. Thank you for that question.

406
00:36:15 --> 00:36:18
Next is A. Harris-Crown.

407
00:36:19 --> 00:36:26
The message is, can we expect watchOS responses to app intents to match up one-to-one with iOS?

408
00:36:28 --> 00:36:30
This is interesting.

409
00:36:31 --> 00:36:38
I think, yes, the answer would be, like, we want to make sure that you're kind of testing these different experiences.

410
00:36:40 --> 00:36:43
Dan, I don't know if you have anything else to add.

411
00:36:43 --> 00:36:49
Yeah, I'll say, you know, the cool thing about the new Siri AI is that it's available on many different platforms.

412
00:36:49 --> 00:37:00
And we want to encourage developers that when you're adopting our new or old API, so you're making sure that you're testing your experiences across all the different devices that we support, including AirPods.

413
00:37:00 --> 00:37:02
You know, a lot of people forget about AirPods.

414
00:37:03 --> 00:37:09
And, you know, when I'm on the run and I have my AirPods on, Siri is extremely helpful to perform quick actions.

415
00:37:09 --> 00:37:14
And so we want to make sure that developers are leveraging the full capabilities of the Apple ecosystem.

416
00:37:14 --> 00:37:18
Yeah, I feel like it's a somewhat less used API that we provide in App Intent.

417
00:37:18 --> 00:37:22
So when you perform an app intent, you can optionally provide some dialogue.

418
00:37:22 --> 00:37:28
But that dialogue can be changed depending on what we call full and supporting modes.

419
00:37:29 --> 00:37:35
So, you know, you might be in a – when you're using your AirPods, you don't have a screen in front of you.

420
00:37:35 --> 00:37:36
You might want to be a bit more verbose.

421
00:37:36 --> 00:37:37
Yeah.

422
00:37:37 --> 00:37:43
Where you're on – you know, you're using your iPad, and we have a nice snippet, and you might want a shorter version of the text.

423
00:37:43 --> 00:37:45
So those APIs are all there.

424
00:37:45 --> 00:37:47
And it makes sense.

425
00:37:47 --> 00:37:49
You know, you might need a little extra content.

426
00:37:49 --> 00:37:52
Yeah, and, you know, App Intents testing framework this year is out.

427
00:37:53 --> 00:37:54
So for everything we've been discussing about App Intents.

428
00:37:54 --> 00:37:56
I was hoping for some questions about App Intents testing.

429
00:37:57 --> 00:37:57
Yeah.

430
00:37:57 --> 00:37:58
Great.

431
00:37:58 --> 00:38:08
I'll also compliment that we have a great session this year that allows you to focus on the customizing the Siri experience, like intent dialogue, like James mentioned.

432
00:38:08 --> 00:38:11
Highly recommend to check out the sessions by our coworker, Antonio.

433
00:38:12 --> 00:38:12
It's a great session.

434
00:38:13 --> 00:38:13
Yeah.

435
00:38:14 --> 00:38:20
A lot of really great, actionable, good, you know, toolbox kinds of tips and tricks in that session.

436
00:38:21 --> 00:38:22
So really good stuff.

437
00:38:23 --> 00:38:24
Okay, moving on.

438
00:38:25 --> 00:38:27
Username, Apple Gex.

439
00:38:28 --> 00:38:28
That's interesting.

440
00:38:29 --> 00:38:29
All right, Apple Gex.

441
00:38:30 --> 00:38:31
I want to say Apple Geek.

442
00:38:31 --> 00:38:32
Anyway, all right.

443
00:38:33 --> 00:38:41
Does Image Playground now require an internet connection for PCC, or does it fall back to the previous model gracefully?

444
00:38:42 --> 00:38:44
Well, I can take this one, actually.

445
00:38:44 --> 00:38:54
Image Playground always requires an internet connection at this point to generate images because, as we mentioned, right, it uses PCC, which is our server-based model.

446
00:38:54 --> 00:38:59
Yeah, it will not fall back to a non-device use of the model.

447
00:38:59 --> 00:39:01
So thank you for that question.

448
00:39:02 --> 00:39:03
All right.

449
00:39:03 --> 00:39:06
Next one here is from MCRich23.

450
00:39:07 --> 00:39:12
What are each of you most excited about regarding the updates to Apple Intelligence?

451
00:39:13 --> 00:39:13
Wow.

452
00:39:13 --> 00:39:14
Well, thank you.

453
00:39:14 --> 00:39:15
We can spend an hour on that one.

454
00:39:16 --> 00:39:23
Although a lot of us, you know, having worked on delivering those features, we're probably like, oh, my gosh, yeah, a lot of hard work here.

455
00:39:23 --> 00:39:25
We're going to go down party lines.

456
00:39:25 --> 00:39:25
Yeah, exactly.

457
00:39:26 --> 00:39:26
Right.

458
00:39:26 --> 00:39:31
But, yeah, do you want to share what you're most excited about with Apple Intelligence?

459
00:39:31 --> 00:39:37
The one that I was really excited about was the, like, intelligence features inside the phone, like when you're on a phone call.

460
00:39:37 --> 00:39:39
Like, I think the example they showed was the flight.

461
00:39:40 --> 00:39:41
Oh, right, right, right.

462
00:39:41 --> 00:39:43
I'm always struggling, like, what's your confirmation number?

463
00:39:43 --> 00:39:44
And you're going through the mail.

464
00:39:44 --> 00:39:45
and you're like, oh, I can't, you know.

465
00:39:45 --> 00:39:50
And having that right there, I was like, that's just a natural, easy way to have that.

466
00:39:50 --> 00:39:55
I like those little features where it's so powerful, but a small little thing.

467
00:39:56 --> 00:39:59
For me, I'm going to have two answers, so don't yell at me.

468
00:40:00 --> 00:40:13
I've obviously been doing a lot of app intents and stuff, but it's been so great to see kind of some of our partner teams, like all the foundation models, where the evaluation framework, it's all so cool, and I was a little less connected to that, so I have a long flight home, and I'm going to be watching a lot of dub dub videos tonight.

469
00:40:14 --> 00:40:16
And you can find your number, right?

470
00:40:16 --> 00:40:16
I can, I can.

471
00:40:17 --> 00:40:18
But the one thing is App Intents Testing.

472
00:40:19 --> 00:40:20
We haven't talked about it today.

473
00:40:21 --> 00:40:25
App Intents Testing is a new way to kind of unit test your App Intents.

474
00:40:25 --> 00:40:30
So traditionally, you might build App Intents, and we would say, you know, you want to test it with Siri, you want to test it with shortcuts.

475
00:40:30 --> 00:40:40
But what was missing is a way to ensure that this functionality was working in kind of your CI pipelines in any type of automated way.

476
00:40:40 --> 00:40:42
So Venkatesh has a great talk this year on it.

477
00:40:42 --> 00:40:50
But for testing your app intents, testing your entities, the queries, integrating with Spotlight, there's a ton of features there.

478
00:40:50 --> 00:40:53
We're really excited for people to dig into that.

479
00:40:53 --> 00:40:54
And it's not just unit tests, right?

480
00:40:54 --> 00:40:56
You can test UI integrations.

481
00:40:56 --> 00:40:58
You can test Spotlight integrations.

482
00:40:58 --> 00:41:00
It's a really valuable framework.

483
00:41:00 --> 00:41:04
I love, in the contested session, how he's got the spacesuit.

484
00:41:04 --> 00:41:05
You've got to watch the video.

485
00:41:05 --> 00:41:06
It's a really cool spacesuit.

486
00:41:06 --> 00:41:07
Spoilers.

487
00:41:07 --> 00:41:07
Anyway.

488
00:41:08 --> 00:41:08
You've got to watch the whole thing.

489
00:41:09 --> 00:41:09
Don't just go to the end.

490
00:41:09 --> 00:41:11
Yeah, just see the whole thing.

491
00:41:11 --> 00:41:11
A lot of good stuff there.

492
00:41:11 --> 00:41:12
So, cool.

493
00:41:12 --> 00:41:13
Robert, what would you say?

494
00:41:13 --> 00:41:15
I'm going to borrow from James here.

495
00:41:15 --> 00:41:18
It was really interesting to see what a lot of teams did.

496
00:41:19 --> 00:41:31
Foundation models with kind of bringing in different language providers, but then also to see that integration go out to, say, core AI, and now I can just bring in any models from the web.

497
00:41:31 --> 00:41:33
But it's like I could still use foundation models.

498
00:41:33 --> 00:41:37
It's like this central API that's really neat.

499
00:41:38 --> 00:41:41
And then, yeah, I'll plug evaluations just a little bit.

500
00:41:43 --> 00:41:52
And if you're using it with foundation models, it's really easy to bring in evaluations because, like James was saying, testing for these things is really a nascent thing.

501
00:41:52 --> 00:41:55
Like I don't, you know, a lot of people talk about it.

502
00:41:55 --> 00:41:57
A lot of people don't know how to do it.

503
00:41:57 --> 00:42:06
And I think we've taken a lot of steps this year to kind of say, all right, how can you think about this feature instead of just assuming the model is going to work like magic?

504
00:42:06 --> 00:42:08
Yeah, that's great.

505
00:42:08 --> 00:42:09
Louis, what would you say?

506
00:42:09 --> 00:42:10
Let me guess, foundation models.

507
00:42:10 --> 00:42:13
Well, I mean, you've mentioned some of the other things already.

508
00:42:13 --> 00:42:18
So yeah, and we talked about like image input support for the on-device model.

509
00:42:18 --> 00:42:21
But the big one, I think, is private cloud compute, right?

510
00:42:21 --> 00:42:23
That developers now get access to.

511
00:42:23 --> 00:42:29
Because we know people have been asking for it-- you've been asking for it-- since last year.

512
00:42:29 --> 00:42:35
And so yeah, this year, new server model running on private cloud, and you can easily access it through our API.

513
00:42:35 --> 00:42:38
And honestly, this is like the perfect time to talk about my new book, actually.

514
00:42:38 --> 00:42:40
Oh!

515
00:42:40 --> 00:42:43
If you don't mind, don't worry, I'll explain.

516
00:42:43 --> 00:42:47
I'll explain, it's not a real book, but just to talk about the benefits here.

517
00:42:48 --> 00:42:58
With private cloud compute, the real thing is that you don't have to worry about API keys or anything else, or authentication, things like that, like with typical server models, right?

518
00:42:58 --> 00:43:06
And so, this is a joke, but it's real, where it's just one line of code to connect to a server model now, right?

519
00:43:06 --> 00:43:11
And so, by the way, in the video that we have, I'll do the little-- - Yeah, you throw it here, man, yeah.

520
00:43:11 --> 00:43:12
All right!

521
00:43:13 --> 00:43:16
Drive to point home that it's easy.

522
00:43:16 --> 00:43:18
You don't have to worry about API keys.

523
00:43:20 --> 00:43:21
Everybody back house just freaked out.

524
00:43:21 --> 00:43:22
They were like, oh, God.

525
00:43:24 --> 00:43:25
No books were harmed in this.

526
00:43:25 --> 00:43:26
No, okay.

527
00:43:27 --> 00:43:28
So there you go.

528
00:43:28 --> 00:43:29
Yeah, that's great.

529
00:43:30 --> 00:43:30
Dan, let me guess.

530
00:43:31 --> 00:43:31
Siri?

531
00:43:33 --> 00:43:35
I was going to say, that's a tough act to follow.

532
00:43:36 --> 00:43:37
Did you also bring a book?

533
00:43:37 --> 00:43:38
Yeah, I did not bring a book.

534
00:43:38 --> 00:43:40
I missed a memo, apparently.

535
00:43:41 --> 00:43:44
But, yeah, no, I am really excited about the new Siri AI.

536
00:43:44 --> 00:43:54
I mean, we have done a lot of great advancements to Siri, and we have a lot of great APIs this year for developers to adopt and bring Siri to their app.

537
00:43:54 --> 00:44:15
I mean, I think it's just really exciting, and I can't wait to see how we'll eventually get to this ecosystem where you have all of these apps that are deeply integrated with Siri and Apple intelligence providing content to the semantic index So that, you know, people can ask natural questions about content from all these different kinds of apps and get answers instantly.

538
00:44:16 --> 00:44:23
And then when you integrate with intent schemas, Siri taking actions on a variety of apps across many different domains.

539
00:44:24 --> 00:44:28
I'm just really excited to see sort of like the bicycle for the mind.

540
00:44:28 --> 00:44:33
You get this ecosystem that is deeply integrated and Siri taking action across many different apps.

541
00:44:33 --> 00:44:35
Yeah, I want to change my answer to that, actually.

542
00:44:37 --> 00:44:57
it's cool to see also like i'm a little fart removed but how much work you guys have put into that and yeah but how natural it becomes when you're the end user it's just like amazing to me i think that is so powerful large delusional simplicity effort from a lot of folks yeah definitely but i can't wait for all my favorite apps to adopt my apis and then just be able to

543
00:44:57 --> 00:45:11
have siri you know perform all these actions absolutely very excited incredible and i'd say for me all of it is really exciting you know one of the great things about being an ai machine learning evangelist is you get to see all of these great things, you know, and get exposure.

544
00:45:12 --> 00:45:31
So I'd say kudos to everyone here at the table and all your teams and the people behind the scenes who made all these features happen. It's like an incredible amount of work. So yeah, kudos to you all, but great stuff. All right. Well, thank you for that question. Next one is from Gerald A. Let's see, Gerald dash, go to capital A, like he's an A student.

545
00:45:31 --> 00:45:34
No, anyway, sorry. Thank you for your question.

546
00:45:34 --> 00:45:35
Teams will be anonymous.

547
00:45:38 --> 00:45:47
And the question is, why again going for hard-coded schemas instead of choosing a dynamic approach like GPT or Cloud are taking?

548
00:45:48 --> 00:45:53
I'm thinking about Markdown describes skills that can reference app entities.

549
00:45:54 --> 00:45:57
Wouldn't this be much more flexible and solve a lot of the other questions mentioned?

550
00:45:58 --> 00:45:59
It's a great question.

551
00:45:59 --> 00:46:00
That is a great question.

552
00:46:00 --> 00:46:01
I'm loving all the schema questions.

553
00:46:02 --> 00:46:03
Keep them coming, by the way.

554
00:46:04 --> 00:46:11
The cool thing about what we're building here and the ecosystem that we're going for is that we're striving for consistency and privacy.

555
00:46:12 --> 00:46:19
Like Apple's take on Apple intelligence is that it's personal intelligence and private to you and the people who use it.

556
00:46:20 --> 00:46:27
And so with schemas, we're able to effectively guarantee an experience that is holistic but also standardized across the platform.

557
00:46:28 --> 00:46:47
I think one of the things that is cool about it is, you know, if you're using Siri with an app that has adopted our schemas in one of the domains, like I'll just pick messaging, for instance, when people interact with Siri in the messages domain, they'll get an experience that will naturally transition to other apps that adopt schemas from that domain as well.

558
00:46:47 --> 00:47:00
This way you get a consistent behavior and interaction with the system in a way that's like once you learn how to interact with Siri in a specific domain, you will just feel natural integrating with all these other kinds of apps.

559
00:47:00 --> 00:47:02
And that's what's really cool about the experience.

560
00:47:02 --> 00:47:23
And also, you know, schemas provide sort of like a security benefit as well, which is like, you know, in certain situations, if you're sending money to somebody, for instance, you probably want to confirm before you're actually just having, you know, Siri send money to someone.

561
00:47:23 --> 00:47:31
And so with schemas, like you get all these security features built right in so that you get a consistent, safe experience on our platforms.

562
00:47:31 --> 00:47:41
I think Antonio does a great job in his talk describing this around our kind of new entity ownership API.

563
00:47:41 --> 00:47:48
For certain items in your app, certain entities, they might be shared with other users.

564
00:47:48 --> 00:47:52
A calendar might be your own calendar or event, but it might be shared with folks.

565
00:47:53 --> 00:47:59
And by using these schemes that we know about, that we can reason about, you might get different behavior.

566
00:47:59 --> 00:48:06
Where if it's just your event, maybe it gets deleted, but if it's shared with other people, then maybe we want to add that confirmation.

567
00:48:06 --> 00:48:12
So it's these kind of things that we really feel like is this powerful, where the schemas give us this power.

568
00:48:12 --> 00:48:14
It's all those edge cases that we've talked about, right?

569
00:48:15 --> 00:48:23
Yeah, we want to provide, for a lot of folks, this will be their first time integrating with these types of systems, building this way.

570
00:48:23 --> 00:48:27
So we always want to be as safe as possible as we're building these things.

571
00:48:27 --> 00:48:29
Yeah, that's how you build trust and safety, right?

572
00:48:29 --> 00:48:50
that's right key to it neither you mentioned localization but i know a lot of the reasons we do this is to kind of integrate in support for localizing this in other languages i know we haven't talked explicitly about yeah um you know plans but i don't think it's surprising way to know we're trying to get this out to different yeah that's the power of the api right like apple

573
00:48:50 --> 00:49:10
has done the heavy lifting in terms of providing all of the model training the natural language strings and so when you're talking about localization apple can scale all of these integrations across many different locales in a way that you developer you simply adopt our api and we take care of all that heavy lifting for you yeah we will make we will try to make this

574
00:49:10 --> 00:49:34
better you know we're going to continue to make this better like that's yeah that's our that's the promise there no great stuff great stuff um all right well gerald dash a thank you for that question. The next question is from Nia Murphy. Does the evaluations framework have a way to see if my tools are being called? I know that one. Yeah, I think we talked about this a little bit

575
00:49:34 --> 00:49:58
earlier. You can supply the transcript from your language model session that you've configured and then use a tool call evaluator in your evaluator process. And actually, then you just provide in your data samples the assertions you want. We have an expectation system where you could say, you know, I want my tools to be called. Were they called? Were they called in

576
00:49:58 --> 00:50:17
the right order? Were they sent the right properties? Were the values that were sent to those properties? Like, you could go pretty deep with this. It's a pretty sophisticated assertion system, but, you know, really you supply that at the data sample, like the scenario level, and then our evaluator takes care of the rest.

577
00:50:17 --> 00:50:18
It looks at the transcript.

578
00:50:18 --> 00:50:22
It finds out all that information and just gives you a report.

579
00:50:22 --> 00:50:23
Yeah, that's really awesome.

580
00:50:23 --> 00:50:32
And, again, having worked with developers at workshops where they're implementing some of their first tools, you're like, wait a minute, it should have been called this way, and it wasn't.

581
00:50:33 --> 00:50:35
It was kind of a source of frustration, frankly.

582
00:50:35 --> 00:50:37
Yeah, authentication is a big one.

583
00:50:37 --> 00:50:42
If you need to access something that requires an authentication, you want to know that.

584
00:50:43 --> 00:50:46
And the model won't always know that or the model won't know, oh, I have to call this tool before this.

585
00:50:47 --> 00:50:47
Totally.

586
00:50:47 --> 00:50:54
And the importance of really giving the LLM good guidance on when that specific tool should be used.

587
00:50:54 --> 00:50:55
Dynamic profiles.

588
00:50:56 --> 00:50:56
Dynamic profiles.

589
00:50:57 --> 00:50:58
We haven't mentioned that yet today.

590
00:50:58 --> 00:50:59
Other great stuff.

591
00:50:59 --> 00:51:01
So, yeah, that's a great feature.

592
00:51:01 --> 00:51:04
And, yeah, thank you, Nia Murphy, for that question.

593
00:51:04 --> 00:51:05
All right.

594
00:51:05 --> 00:51:06
Moving on to the next one here.

595
00:51:08 --> 00:51:09
ProtonStir.

596
00:51:09 --> 00:51:09
I like that.

597
00:51:10 --> 00:51:10
ProtonStir.

598
00:51:11 --> 00:51:11
I don't know.

599
00:51:12 --> 00:51:12
Yeah.

600
00:51:12 --> 00:51:12
Very cool.

601
00:51:12 --> 00:51:13
Super cool.

602
00:51:13 --> 00:51:15
There's a person with a cape, you know.

603
00:51:16 --> 00:51:17
Next year, anonymous.

604
00:51:17 --> 00:51:20
Too much time is being spent on the names.

605
00:51:21 --> 00:51:33
How does the Apple Intelligence Framework mitigate semantic entropy when passing context between on-device models and larger server-based models?

606
00:51:33 --> 00:51:34
That lives up to the username.

607
00:51:36 --> 00:51:38
It's funny you just mentioned dynamic profiles.

608
00:51:38 --> 00:51:41
That's Foundation Models Framework, I think, is what it's alluding here.

609
00:51:41 --> 00:52:05
but um yeah it's a great question like we you know we have the uh system language model that has a 4k context size and we have the pcc model that's at 32k so when you're using dynamic profiles which quickly a high over view of that is you can um with the same transcript switch between specific profiles maybe somebody is specific to accounting and they do all those things but you want to have

610
00:52:05 --> 00:52:28
another profile that's doing something health related you can switch between those it's like agents right yeah exactly um and uh in order to kind of keep the the uh the context sharing uh we have modifiers in this declarative style like history transform to say hey i switched to this different profile maybe i want to throw out some tool calls maybe i want to change something and

611
00:52:28 --> 00:52:50
keep those balanced and then another great thing that we added this year is the foundation models utilities package that has uh ways where you can do like summarization and maybe compact the transcript if you're going to a smaller size so there's all these tools um another great way to test that was with the evaluations framework we marry well together and be able to see what works

612
00:52:50 --> 00:53:00
best with your use cases and tailor that awesome great thank you for that question um okay moving Again, Freescott is the username.

613
00:53:01 --> 00:53:05
Do I need to use the evaluations framework with foundation models?

614
00:53:06 --> 00:53:07
Oh, that's a great question.

615
00:53:07 --> 00:53:14
As I mentioned earlier, I think when we were talking about academic research, you can obviously use a language model.

616
00:53:14 --> 00:53:18
You don't have to use the foundation models framework to do a lot of the evaluation.

617
00:53:18 --> 00:53:27
We just talked about tool call evaluator, which is one example where if you want to use the tool call evaluator, you do need to use foundation models because we need that transcript.

618
00:53:27 --> 00:53:37
we're actually working to kind of do a more generic transform from other models, such that if they're using a model provider, they'll get that transform.

619
00:53:38 --> 00:53:40
But by and large, no, you don't.

620
00:53:40 --> 00:53:44
You can evaluate any stochastic system with that.

621
00:53:45 --> 00:53:45
Yeah, that's great.

622
00:53:46 --> 00:53:46
Great stuff.

623
00:53:46 --> 00:53:47
Thank you for that question.

624
00:53:48 --> 00:53:52
Okay, next one from Drobodin Message.

625
00:53:52 --> 00:54:01
A question is, I have multiple apps that will all donate entities via index entity to the Spotlight Semantic Index.

626
00:54:02 --> 00:54:18
Can one of my apps, Foundation Model Sessions, retrieve another of my apps donated content via Spotlight Search Tool, same developer, shared app group, or is retrieval strictly sandbox to the donor?

627
00:54:19 --> 00:54:23
Yes, retrieval is limited to your app sandbox only.

628
00:54:23 --> 00:54:23
Right.

629
00:54:23 --> 00:54:43
wouldn't be able to yeah since they do mention app groups like you could do interesting things presumably where like like two apps from the same developer can be in the same app group and they can share files right on this can share some data not like literally through that sandbox but like you probably like find ways to maybe share things uh yeah so the way we think about that in app

630
00:54:43 --> 00:55:07
intense is with the transferable protocol um so that is kind of the best way for um inner for doing this kind of interrupts between different file formats essentially you know you can you transferable works by a kind of progressive from highest fidelity to lowest fidelity you know data format so if you were both to share like a similar data format you can use

631
00:55:07 --> 00:55:28
transferable to go between apps and the cool thing about that api is that you're essentially opting into the data transfer so it's done in a private and secure way because you're essentially saying, like, I want to share maybe a small projection of my app, but not all the contents of my app. So you, the developer, is always in control of what gets shared with whom.

632
00:55:28 --> 00:55:55
That's great. Awesome. Great question. Thank you for that. Okay, next question. Again, from, looks like, John. That's an easy username. Yeah, Jay. Here we go. Schemas. Are schema-bound entities interchangeable? Can an app's file entity be turned into a photo entity and vice versa?

633
00:55:55 --> 00:56:00
I suspect this is possible through transferable. You're right on.

634
00:56:02 --> 00:56:04
Plus one there. You are higher, John.

635
00:56:06 --> 00:56:27
That's the cool thing about transferable is that you get to specify all the different data transformations that your app can support. So like, for instance, in our sample app, that's available that you can download today, Unicorn Chat, we adopt transferable in the app in a way where you can have messages that could be text,

636
00:56:27 --> 00:56:29
but they can also contain attachments.

637
00:56:29 --> 00:56:35
And so the app supports transferability, exporting text, but they can also export files.

638
00:56:35 --> 00:56:37
You can also export photos.

639
00:56:37 --> 00:56:44
And so depending on the content, you can even provide transferability in a dynamic way based on the payload type.

640
00:56:44 --> 00:56:53
And that allows you to export your contents in a way that can integrate with different apps based on the capabilities that an app supports.

641
00:56:53 --> 00:57:04
So when you export a message as a photo, it means that this photo can now be imported into the Photos app, but it can also be imported into any other third-party photo app that adopts our photo schemas.

642
00:57:04 --> 00:57:06
Yeah, there's one more note there.

643
00:57:06 --> 00:57:16
There is a file entity protocol that works for all app entities that's designed for when you need these file-based formats.

644
00:57:16 --> 00:57:18
So read the documentation.

645
00:57:18 --> 00:57:20
Yeah, more information there.

646
00:57:20 --> 00:57:21
Oh, that's great.

647
00:57:21 --> 00:57:24
You know, I hear Unicorn Chat's, like, moving up on the top app list.

648
00:57:25 --> 00:57:26
There we go.

649
00:57:26 --> 00:57:28
You used to have these models in that, right?

650
00:57:28 --> 00:57:29
Yeah, you did.

651
00:57:29 --> 00:57:30
It combines our technologies.

652
00:57:31 --> 00:57:32
No, that's great.

653
00:57:32 --> 00:57:33
Well, thank you for that question, John.

654
00:57:34 --> 00:57:41
Okay, next question from Zulfi Shah, I believe.

655
00:57:41 --> 00:57:42
Sorry, we say that.

656
00:57:42 --> 00:57:47
In my app, I adopt app intents and app entities for various concepts in my app.

657
00:57:47 --> 00:57:51
Some of these might map to app schemas, though not perfectly.

658
00:57:52 --> 00:58:00
For example, I manage a list of contacts, and a close match would be schema from .messages, .messages person.

659
00:58:01 --> 00:58:04
Is that acceptable to participate in new Siri?

660
00:58:05 --> 00:58:08
Yeah, absolutely. I mean, the answer is definitely yes.

661
00:58:09 --> 00:58:17
The cool thing about this API is that you can pick and choose the functionality that your app supports and its capabilities based on the app schemas that we support.

662
00:58:17 --> 00:58:22
So, you know, you mentioned some kind of contact integration.

663
00:58:22 --> 00:58:30
We have schemas for a message person, for instance, where you can export context from your app to support some messaging features.

664
00:58:30 --> 00:58:31
In fact, Unicorn Chat uses this.

665
00:58:32 --> 00:58:40
And you can check out the sample code to see how we model content in the app using this schema to expose this information to Siri and Apple intelligence.

666
00:58:41 --> 00:58:57
But, you know, if you can't find any schema that sort of fits into your app's functionality, we have the more generic system search capability so that you can integrate with Apple Intelligence and expose your app's search capabilities.

667
00:58:57 --> 00:59:02
It's great for apps like e-commerce, food ordering, and many other types.

668
00:59:03 --> 00:59:12
But then you can always use app shortcuts as well as another way to essentially integrate your app intent in a way that can be more generic.

669
00:59:12 --> 00:59:22
Now, of course, because app shortcuts are custom, it means that you have to provide sample phrases that match sort of your app's experiences and the scenarios that you're thinking about.

670
00:59:23 --> 00:59:25
And that's the cool thing about app schemas.

671
00:59:25 --> 00:59:29
When you integrate with a schema, Apple is doing the heavy lifting, so you don't have to provide those phrases.

672
00:59:31 --> 00:59:31
That's great.

673
00:59:31 --> 00:59:32
James, anything you want to add on?

674
00:59:32 --> 00:59:33
I think you covered it.

675
00:59:33 --> 00:59:39
I'm glad people are kind of asking this question, though.

676
00:59:39 --> 00:59:40
It's come up a few times.

677
00:59:41 --> 00:59:44
So, you know, I think it's really cool that people are thinking about this.

678
00:59:45 --> 00:59:45
Yeah.

679
00:59:45 --> 00:59:47
This is good stuff.

680
00:59:47 --> 00:59:50
I can't wait to see what people build with these integrations and these APIs.

681
00:59:50 --> 00:59:51
Very excited.

682
00:59:51 --> 00:59:51
Yeah.

683
00:59:52 --> 00:59:53
Thank you for that question.

684
00:59:54 --> 00:59:54
All right.

685
00:59:54 --> 00:59:55
Almost out of time here.

686
00:59:56 --> 00:59:57
So let's see if we can get one more in here.

687
00:59:57 --> 00:59:59
From R-Grad Harkershran.

688
01:00:00 --> 01:00:04
Apologies for stepping all over your username there.

689
01:00:04 --> 01:00:12
We already know that with image processing addition to foundation models, the token limit is still 4096.

690
01:00:12 --> 01:00:22
Do we need to be aware of any constraints to our other inputs like prompt or instructions in the case for the model to function at its best?

691
01:00:25 --> 01:00:28
I mean, with the on-device model, you have 4096, right?

692
01:00:29 --> 01:00:32
With the image input support, I mean, it doesn't really change anything.

693
01:00:32 --> 01:00:42
If you put an image in there, you can measure this with instruments, with the support we added this year, but I think it comes down to about 200 tokens or so for an image, typically.

694
01:00:42 --> 01:00:49
But in terms of your instructions or anything else, you can take advantage of the full 4,096.

695
01:00:49 --> 01:00:58
If you want to give it 4,000 tokens input and generate a really small output, that will work just as well as giving it a small input and generating large output.

696
01:00:58 --> 01:01:01
And then with Private Cloud Compute, just for clarity, you get 32.

697
01:01:02 --> 01:01:31
K. Yeah context size, right? Yeah, that's much larger. Yeah much much. I think - in 26.4 we also shipped Token counting api's so you can play around with that to see what's working. But also, you know, depending on your use case You you kind of want to hill climb? Yeah Looting over here and we're over here You know You might have a very specific use case and tailoring that prompt and making a brief maybe without smaller context size and point and evaluations

698
01:01:31 --> 01:01:33
to do that climbing can also help with that.

699
01:01:33 --> 01:01:41
Yeah, the last thing I'll say with the on-device model, we get a lot of questions about, oh, it's not as capable because it's smaller.

700
01:01:41 --> 01:01:45
And it's like, it's actually, you just have to be more precise with your instructions.

701
01:01:46 --> 01:01:49
And sometimes it's finding the right synonym to use.

702
01:01:49 --> 01:01:51
So you might try different variations of words.

703
01:01:52 --> 01:01:59
And to your point, hill climbing is a perfect way to just kind of benchmark that and see which variation of the prompt works best.

704
01:01:59 --> 01:02:05
But you can get a lot out of the device model if you're willing to put some time kind of massaging your prompt.

705
01:02:05 --> 01:02:07
And put examples in your prompt as well, right?

706
01:02:07 --> 01:02:07
Yeah.

707
01:02:07 --> 01:02:08
Oh, yeah.

708
01:02:08 --> 01:02:09
Give it examples.

709
01:02:10 --> 01:02:13
You know, give it as much information as you can.

710
01:02:14 --> 01:02:14
Yeah.

711
01:02:14 --> 01:02:16
Well, great stuff.

712
01:02:16 --> 01:02:21
Well, unfortunately, that's about all we have time for today for this group lab.

713
01:02:21 --> 01:02:25
We're really thankful for you taking the time out of your busy schedules to join us.

714
01:02:25 --> 01:02:29
We really hope you found this to be really helpful and beneficial.

715
01:02:30 --> 01:02:32
You know, we also want to say thanks to our panelists.

716
01:02:33 --> 01:02:33
Thank you all.

717
01:02:33 --> 01:02:37
I know it's been a really busy week and the release and everything else you have going, so thank you.

718
01:02:39 --> 01:02:41
And, yeah, just want to say thanks again to all of you.

719
01:02:42 --> 01:02:49
As we mentioned earlier, if you go to developer.apple.com slash forms, please post your questions, your code questions, and so on.

720
01:02:49 --> 01:02:52
We're there to help you any way we can.

721
01:02:52 --> 01:02:55
And, of course, feedback assistant, right, if you have ideas.

722
01:02:55 --> 01:02:57
And if you find a bug, okay.

723
01:02:58 --> 01:02:59
I'll be a first, but still.

724
01:03:00 --> 01:03:03
But, yeah, exactly.

725
01:03:03 --> 01:03:06
But by all means, take advantage of the resources.

726
01:03:06 --> 01:03:10
Some amazing videos this year, a lot of great technical documentation.

727
01:03:11 --> 01:03:16
And, yeah, you know, we really appreciate all your feedback and insights.

728
01:03:16 --> 01:03:19
If you'd like to see more of these kind of group labs, you know.

729
01:03:19 --> 01:03:20
Or see me throw a book, right?

730
01:03:20 --> 01:03:23
Yeah, you can see me throw more books and other things.

731
01:03:24 --> 01:03:26
Please let us know that as well.

732
01:03:26 --> 01:03:27
We really want to help you be successful.

733
01:03:27 --> 01:03:32
And thank you for being part of the Apple community, the Apple family.

734
01:03:32 --> 01:03:39
We really appreciate all your time and great work in seeing the innovative things, apps, and new technologies you come up with.

735
01:03:40 --> 01:03:40
So thank you.

736
01:03:41 --> 01:03:43
And, yeah, with that, thanks again for joining us.

737
01:03:44 --> 01:03:45
Have a great rest of your WWDC.

738
01:03:46 --> 01:03:47
And we can't wait to see what you build.

739
01:03:47 --> 01:03:48
Yes, absolutely.

740
01:03:49 --> 01:03:49
Thank you all.
