---
title: SwiftUI Group Lab
source: https://developer.apple.com/videos/play/wwdc2026/8120/
session: 8120
collection: wwdc2026
duration: 01:02:35
fetched: 2026-06-12
via: whisper.cpp ggml-large-v3 (session SD video audio)
transcription: machine-generated, no speaker diarization
---

> Machine transcription of the **SwiftUI Group Lab** SD video via whisper.cpp (`ggml-large-v3`).
> Group labs ship no Apple transcript; this is generated from the audio. Cues are timestamped segments, **not** speaker-attributed turns.

## Transcript

1
00:00:03 --> 00:00:09
Hi, welcome to another SwiftUI group lab, or as we like to call it, SwiftUI After Dark.

2
00:00:10 --> 00:00:14
My name's Kurt, and I'm part of the Worldwide Developer Relations team here at Apple.

3
00:00:14 --> 00:00:22
I'm joined by engineers and leaders from the UI Frameworks team, Anna, Stephen, Nick, Russell, and Sam.

4
00:00:22 --> 00:00:30
In addition to those on screen, there's a team behind the scenes helping with the triage of all your inbound questions.

5
00:00:30 --> 00:00:35
We're excited to answer your questions tonight about all things SwiftUI and UI Frameworks.

6
00:00:35 --> 00:00:48
If you have a code-specific question or somehow we can't get to your question today, please bring your questions to the developer forum at developer.apple.com slash forums, where we can continue the discussion.

7
00:00:49 --> 00:00:54
If you have a bug or a feature request, go to feedbackassistant.apple.com.

8
00:00:54 --> 00:00:57
We'll focus on questions tonight that will help the broadest audience.

9
00:00:58 --> 00:01:01
So, without further ado, let's get to questions.

10
00:01:01 --> 00:01:11
And I'd like to start things off by just hearing from each of you about what parts of SwiftUI you work on.

11
00:01:11 --> 00:01:12
Maybe, Sam, you can get us started.

12
00:01:12 --> 00:01:13
Sure.

13
00:01:13 --> 00:01:18
So before this world, I did a lot of work in programming languages.

14
00:01:18 --> 00:01:27
And so as a sort of natural consequence of that, I work on things that are like at the intersection of Swift and SwiftUI because that's sort of where my interests lie.

15
00:01:27 --> 00:01:37
So if you've seen, like, some of the type checking performance work that we announced this year, I'm very involved in that and anything else where we're thinking about, like, how do we use Swift in really interesting ways.

16
00:01:38 --> 00:01:42
I actually worked on UIKit for most of my time here.

17
00:01:43 --> 00:01:49
But I worked on animations there and the interop between that and SwiftUI and, like, the bridging parts of SwiftUI a little bit.

18
00:01:50 --> 00:01:51
Nice. Nick?

19
00:01:51 --> 00:01:54
I, too, I did a stint in AppKit.

20
00:01:54 --> 00:01:58
I did a little bit of work in UIKit, and now I'm a manager on SwiftUI.

21
00:01:58 --> 00:02:07
So when I do get to write code, I'm sometimes in presentations with Sheet and some of the containers like Inspector, Navigation, Dabble, and Controls.

22
00:02:07 --> 00:02:10
And then this year, I spent a little bit of time in Dataflow.

23
00:02:10 --> 00:02:12
Oh, nice. And Steven?

24
00:02:12 --> 00:02:18
So before SwiftUI, I was an engineer on the Apple Music team doing SwiftUI development on the app side.

25
00:02:19 --> 00:02:26
And now my focus is on SwiftUI performance and also the agentic coding skills that we introduced this year.

26
00:02:27 --> 00:02:27
Nice. And Anna?

27
00:02:28 --> 00:02:32
Yeah, I work on mostly navigation, bars, tabs.

28
00:02:33 --> 00:02:37
Effectively, if it's touching the way that someone is navigating your app, that's something that I work on.

29
00:02:38 --> 00:02:39
I get to work on it with Nick.

30
00:02:40 --> 00:02:41
Nice. Excellent.

31
00:02:41 --> 00:02:49
And before I became part of the Worldwide Developer Relations team, I also worked on presentations and navigation.

32
00:02:49 --> 00:02:52
In fact, Russell and I shared an office, which was great.

33
00:02:53 --> 00:02:57
So let's go to some questions from the forum.

34
00:02:57 --> 00:03:11
So the first question is, with over a million reorderable items, 4-H doesn't render every view, but still traverses the whole collection on first display and every count or order change, causing slowdowns.

35
00:03:12 --> 00:03:15
UIKit's diffable data source has the same issue.

36
00:03:15 --> 00:03:17
Only a classic data source helps.

37
00:03:18 --> 00:03:20
What can we do about this in SwiftUI?

38
00:03:23 --> 00:03:24
You want to take a stab at it, Steven?

39
00:03:25 --> 00:03:29
Yeah, so this is actually an issue that we're looking into and that we're aware of.

40
00:03:30 --> 00:03:32
It definitely shouldn't be working in that way.

41
00:03:32 --> 00:03:38
And the best thing to do is to file a feedback in order for us to keep track of it.

42
00:03:39 --> 00:03:47
And I should say, when we're asking for feedback, it's not so much that we need to be aware of the issue.

43
00:03:47 --> 00:03:49
We know there are issues, as Stephen said.

44
00:03:49 --> 00:03:52
But we want to know your particular use case.

45
00:03:53 --> 00:03:56
So why do you need to display that many items in a list?

46
00:03:56 --> 00:04:02
What's the nature of your data that causes that to be sort of the structure that you're building up?

47
00:04:02 --> 00:04:11
So that's the sort of thing we look for in feedback reports is tell us what you're trying to accomplish so we can make sure the APIs support your use cases.

48
00:04:11 --> 00:04:14
And bravo to this developer for downloading the betas and testing them out.

49
00:04:15 --> 00:04:20
Like we are – every day I get to work right now and I come in and see what feedbacks are coming in from developers.

50
00:04:20 --> 00:04:22
They're very visible to us.

51
00:04:22 --> 00:04:22
We take a look at them.

52
00:04:22 --> 00:04:24
We've already gotten a couple on state macro as well.

53
00:04:24 --> 00:04:29
And so we're fixing the bugs, and when you get them in early, we get to look at them pretty quickly.

54
00:04:29 --> 00:04:35
Totally the best time to file feedback that will influence the next release is right now.

55
00:04:36 --> 00:04:44
So our next question, and let's see, Anna, I think I'll let you take a crack at this one first.

56
00:04:44 --> 00:04:50
So in iOS 27, at least beta 1, it's not possible to create a custom transition.

57
00:04:51 --> 00:04:53
The protocol navigation transition is publicly empty.

58
00:04:56 --> 00:05:01
So what is the purpose of this protocol, and is there anything we can do to make our own?

59
00:05:02 --> 00:05:10
Yeah, so that is effectively, like, a way for SwiftUI to expose things that, you know, conform to the protocol so they can be, like, navigation transitioning.

60
00:05:11 --> 00:05:21
Like, this is something that, you know, in SwiftUI, sometimes you will see, like, either, like, protocols or, like, also styles that will give you the pieces to be able to do your own.

61
00:05:21 --> 00:05:22
Like, for example, like a button style.

62
00:05:23 --> 00:05:26
But there are other ones that maybe expose concrete members.

63
00:05:26 --> 00:05:35
So, like, you can make a Zoom, you know, navigation transition, but they may not necessarily, at least right now, expose a way to do it to create your own custom one.

64
00:05:35 --> 00:05:49
So, kind of, unfortunately, going back to the last one, but if you're able to file a feedback with your use case, we'd be really curious about the kinds of things you're trying to build because it helps us know also, you know, what are the pieces that you might need in order to be able to build your own custom navigation transition.

65
00:05:50 --> 00:05:56
So we've got crossfade transition and zoom transition and automatic as the sort of the trifecta today.

66
00:05:57 --> 00:06:06
But one of the ones that I'm actually quite excited about this year, which is very sleeper, but any navigation transition, which means you can switch between the two of them, which is something that you weren't able to do before.

67
00:06:07 --> 00:06:08
So like having that dynamism is nice.

68
00:06:08 --> 00:06:08
Cool.

69
00:06:09 --> 00:06:11
And that crossfade one applies to sheets, too.

70
00:06:11 --> 00:06:14
So you can do some pretty cool things with it.

71
00:06:14 --> 00:06:14
Zoom as well.

72
00:06:15 --> 00:06:15
Oh, yeah, yeah.

73
00:06:16 --> 00:06:16
Nice.

74
00:06:16 --> 00:06:19
Yeah, so the API is composed, right?

75
00:06:19 --> 00:06:25
So when you say navigation transition, we named that specifically because there was a lot of back and forth on what that should be called.

76
00:06:25 --> 00:06:28
Presentation transition, navigation transition, transition transition.

77
00:06:30 --> 00:06:35
But we named it navigation transition because navigation was kind of the unifying concept, like sheets are navigation.

78
00:06:35 --> 00:06:47
Another fun thing, just when you're making these transitions, there's no SwiftUI first-class API for custom transitions in the sense that the UIKit one exists.

79
00:06:48 --> 00:06:56
But it's also quite fun and easy to build custom transitions for your own SwiftUI views if they're not on top of a navigation stack.

80
00:06:56 --> 00:07:03
So you can drive the animation yourself and SwiftUI gives you all sorts of modifiers to flex the shape and you can even use shaders.

81
00:07:04 --> 00:07:10
And so if it doesn't strictly have to be pushing onto a navigation stack, it's quite a lot of fun to build your own transitions.

82
00:07:11 --> 00:07:19
Yeah, so if your animations are really powerful, especially with Animatable and you can have keyframe-based animations, there's some crazy stuff you can do.

83
00:07:19 --> 00:07:20
Yeah, it's a lot of fun.

84
00:07:21 --> 00:07:24
So, Stephen, I'm going to send this next question your direction.

85
00:07:24 --> 00:07:32
With coding intelligence and agentic models in Xcode, what are some ways we can implement it in building views with liquid glass, for example?

86
00:07:33 --> 00:07:39
So I guess let's talk about that more generally instead of diving into the specifics about it, and then we can maybe double back to the specifics.

87
00:07:39 --> 00:08:01
Yeah, so the new skills that are shipping with Xcode are a really good way to get some additional insight, Especially into new api's and best practices and we would definitely like to get feedback on the skills as well the the tools that are built into Xcode are really good for rendering previews of liquid glass also, so

88
00:08:03 --> 00:08:22
iterating on the various pieces of the design to kind of get things looking the way that you want them to look is a really good way to Sort of work your way towards the the outcome that you're trying to get And the skills are usable with the model of your choice as well, which is great So, you know if the results vary from model to model

89
00:08:23 --> 00:08:40
You know, there's an opportunity there to to experiment with kind of trying to iterate with that right as a reminder also about these There all these skills are built into like X codes agentic workflows and they work really great there But if you you know use something outside of that like some other agentic system, whatever it is

90
00:08:40 --> 00:08:49
You can always export all of these skills. There's a command I forget exactly what it is, but it's listed in What's New and in Docs and other places.

91
00:08:49 --> 00:08:50
Yeah, and then modernizing your UIKit app as well.

92
00:08:50 --> 00:08:51
Yeah, yeah.

93
00:08:51 --> 00:08:53
So you can grab those skills and use them elsewhere.

94
00:08:53 --> 00:08:54
And also, they're fun to read through.

95
00:08:55 --> 00:08:58
There's a lot of really cool tidbits in there for humans too.

96
00:08:58 --> 00:09:02
And obviously targeted at LLMs, but that doesn't mean people can't learn from them.

97
00:09:02 --> 00:09:10
So the second part of this question is about using the glass effect modifier on buttons to apply a glass effect.

98
00:09:10 --> 00:09:22
And I want to take a second to talk a little bit about what's in the human interface guidelines and what we've seen in the various workshops we've done around the world about adoption of the new design.

99
00:09:22 --> 00:09:36
When you put buttons in toolbars in the navigation bar, the top toolbar, they'll take on the appropriate glass appearance automatically.

100
00:09:37 --> 00:09:41
And so the glass effect is really for stuff that is not appearing in those locations.

101
00:09:42 --> 00:09:51
And in general, we would encourage people to not use liquid glass inside the content area of the view.

102
00:09:51 --> 00:09:59
Think of the glass control layer as sort of hugging the content layer, and the content layer underneath is flat.

103
00:09:59 --> 00:10:07
Because if there's not any content scrolling underneath the glass, the glass effect really isn't providing any benefit.

104
00:10:07 --> 00:10:08
There's nothing for it to refract.

105
00:10:09 --> 00:10:10
It's just static.

106
00:10:10 --> 00:10:15
And so think about whether you actually need Glass effect in some of these cases.

107
00:10:15 --> 00:10:18
Often the simpler design is actually better in the content area.

108
00:10:18 --> 00:10:23
I'm going to take a dangerous venture down trying to describe controls.

109
00:10:23 --> 00:10:27
I don't work too frequently in controls, so Frank, please correct me if you're watching.

110
00:10:29 --> 00:10:35
But if you're trying to put Glass on a button specifically, there is API for that, I'm pretty sure.

111
00:10:36 --> 00:10:36
There's a Glass button style.

112
00:10:36 --> 00:10:39
Yeah, you want glass button style or glass prominent.

113
00:10:40 --> 00:10:44
I think FaceTime uses glass prominent where they've got that green button in there.

114
00:10:45 --> 00:10:46
Like this is the button.

115
00:10:46 --> 00:10:48
This is the magic one.

116
00:10:48 --> 00:10:50
So, yeah, you can use it to good effect there.

117
00:10:50 --> 00:10:54
And one error I see people making, because it's very natural to do, is to reach for glass effect.

118
00:10:54 --> 00:10:56
You're like, oh, button, glass effect.

119
00:10:56 --> 00:11:01
And alluding to the question, that will get you a button on glass, but it won't look exactly right.

120
00:11:01 --> 00:11:03
So you probably want button glass effect.

121
00:11:03 --> 00:11:06
And then on top of that, you want button border shape.

122
00:11:06 --> 00:11:09
A lot of times you want button border shape to, like, make it a circle or a triangle.

123
00:11:10 --> 00:11:12
Yeah, and the other thing I will say is, like, if you're...

124
00:11:12 --> 00:11:14
A triangle.

125
00:11:15 --> 00:11:16
A capsule.

126
00:11:16 --> 00:11:17
Yeah, a capsule.

127
00:11:17 --> 00:11:18
Yeah, a warning button.

128
00:11:18 --> 00:11:18
There we go.

129
00:11:19 --> 00:11:19
Port and button.

130
00:11:20 --> 00:11:21
Try your warnings.

131
00:11:22 --> 00:11:24
We're going to hear about this one.

132
00:11:25 --> 00:11:27
Anna, you had something useful to add.

133
00:11:27 --> 00:11:36
The other thing I will add, though, on top of, like, when to use liquid glass is reminder that if you put, like, a button in a toolbar item, like, that will already have a glass background.

134
00:11:36 --> 00:11:38
So, like, you don't need to add one on top of that.

135
00:11:38 --> 00:11:43
And if you want it to take on a color like a prominent glass button might, you can just use a tint.

136
00:11:43 --> 00:11:47
Can you put content not on glass in the toolbar?

137
00:11:47 --> 00:11:48
Yes, you can.

138
00:11:48 --> 00:11:52
So, you use, I believe it's the, oh, goodness.

139
00:11:52 --> 00:11:56
It's shared background hidden true.

140
00:11:56 --> 00:11:58
Shared background hidden true.

141
00:11:58 --> 00:11:59
A lot of debate went into that one as well.

142
00:11:59 --> 00:12:00
Yes.

143
00:12:01 --> 00:12:03
We think a lot about names of things.

144
00:12:04 --> 00:12:11
And the main case for hiding the glass background, for example, you can see this in the books app.

145
00:12:11 --> 00:12:16
It shows a profile photo of the current person who's logged in.

146
00:12:17 --> 00:12:22
And putting glass around and behind a photo of a person actually doesn't look great.

147
00:12:22 --> 00:12:26
And so this causes that to be flat and not have the glass treatment over the top.

148
00:12:27 --> 00:12:30
But we have something for that now, don't we?

149
00:12:30 --> 00:12:35
Yeah, we do have something that makes it a little bit easier to make kind of like the padding and other things.

150
00:12:35 --> 00:12:36
It's not good in that case.

151
00:12:36 --> 00:12:40
So because one thing that can look kind of cool is if you have like a little glass refraction around the edge.

152
00:12:40 --> 00:12:47
Like you don't have a huge piece of glass, but you have like just enough to kind of reflect, but it's mostly the profile photo.

153
00:12:47 --> 00:12:54
And we've added API this year so that you can remove that content margin that normally toolbar buttons will add around the edge.

154
00:12:54 --> 00:12:56
So the refraction is right at the edge of the photo.

155
00:12:56 --> 00:12:56
Yeah.

156
00:12:57 --> 00:12:57
Super cool.

157
00:12:57 --> 00:13:01
Because removing the background used to be the only way to remove that padding.

158
00:13:01 --> 00:13:02
It removed both.

159
00:13:02 --> 00:13:03
And then your view could take up the whole space.

160
00:13:03 --> 00:13:05
So it looked the same size as like the other buttons.

161
00:13:06 --> 00:13:10
But now you can remove just the padding and leave the background and keep your image inside.

162
00:13:11 --> 00:13:12
And even if it filled, then it can fill it.

163
00:13:12 --> 00:13:18
And then you get the rim and it's like still made of glass, which then also means it mitosis better when you like push and pop.

164
00:13:18 --> 00:13:22
Because the mitosis like looks more natural of everything as glass.

165
00:13:22 --> 00:13:25
So it's like it just it all looks much better now.

166
00:13:25 --> 00:13:27
I want all of the avatars to have like glass.

167
00:13:27 --> 00:13:35
Mitosis, for those who haven't had a biology class recently, is when the shapes sort of blob together or blob apart.

168
00:13:36 --> 00:13:39
It's like they have this sort of liquid, really cool effect.

169
00:13:39 --> 00:13:39
Yes.

170
00:13:40 --> 00:13:40
Also called schlerp.

171
00:13:41 --> 00:13:42
Schlerp, yes, also called schlerp.

172
00:13:44 --> 00:13:49
I believe schlerp is the technical term, which is why we tend to stick with mitosis, which is a lot easier to understand.

173
00:13:50 --> 00:13:51
Yes, it's so easy.

174
00:13:53 --> 00:14:00
So the next question, I think I'll start with you, Russell, and then you might be interested in jumping in also.

175
00:14:01 --> 00:14:01
Or Nick.

176
00:14:01 --> 00:14:03
There's lots of us who have interests here.

177
00:14:04 --> 00:14:05
But we'll start with Russell.

178
00:14:05 --> 00:14:17
So what's the best way to manage screen sizes now in our apps with vertical and resizable and split views and all the different sizes that screens can take now, the resizable simulator?

179
00:14:18 --> 00:14:23
Like what API should we think about using as Windows to handle Windows resizing?

180
00:14:23 --> 00:14:30
Definitely. So one, if you're hard coding for specific window sizes, don't do that.

181
00:14:32 --> 00:14:49
That's been a losing battle for a long time. Really, just if you can lay out at any size, you should be able to do that. And then look at size classes. I think we're leaning like even more into them than we were before. And I think of them as kind of defining different experiences.

182
00:14:50 --> 00:14:58
So at regular size classes, you get kind of your full, like, iPad or Mac-like multi-column experience.

183
00:14:59 --> 00:15:08
And in compact sizes, either vertically compact or horizontally compact, then you get a more, like, you're constrained in that environment, so you just want to display less.

184
00:15:09 --> 00:15:15
And sometimes that means just changing your number of columns, and sometimes it's much more dramatically changing your app.

185
00:15:15 --> 00:15:15
Right.

186
00:15:16 --> 00:15:18
But if you lean into size classes, you should be okay.

187
00:15:18 --> 00:15:22
Yeah, but remember that just supporting size classes isn't fully sufficient, right?

188
00:15:22 --> 00:15:27
Like, you know, Windows can be many different sizes that, like, go in between where you'd want those size class transitions.

189
00:15:27 --> 00:15:30
So make sure that you're dealing with true flexibility, right?

190
00:15:30 --> 00:15:34
In addition to doing, like, different UI looks and feels per size class.

191
00:15:34 --> 00:15:39
Yeah, I mean, I will also say, like, using system containers, if that's, like, an option for your app, can be really nice.

192
00:15:39 --> 00:15:43
Because then we're the ones doing the work for you to have to support that.

193
00:15:43 --> 00:15:48
But the other thing I will plug is something that, I don't know.

194
00:15:48 --> 00:15:52
I feel like it's just layouts in SwiftUI are also really cool and really powerful.

195
00:15:52 --> 00:15:57
There's a good WWDC talk from 22 on that.

196
00:15:57 --> 00:15:59
Using custom layouts.

197
00:16:01 --> 00:16:02
We're all thinking, oh, yeah, that's Paul's talk.

198
00:16:03 --> 00:16:07
Internally, we all think of who gave the talk because we know these people.

199
00:16:07 --> 00:16:11
And it turns out the stuff is very helpful to those of you who are trying to find them.

200
00:16:11 --> 00:16:11
That was the title, Paul's talk.

201
00:16:13 --> 00:16:16
But it is the session on building custom layouts in SwiftUI.

202
00:16:17 --> 00:16:24
Yeah, because, like, one thing that people, I think, is something that people will reach for is, like, doing an if statement off of size class.

203
00:16:24 --> 00:16:29
And the thing that can be, like, not great about doing that is when it changes, it'll tear down the view on the other side.

204
00:16:29 --> 00:16:34
So you lose all your state or you have to do a ton of work to make it so that the state is, like, synced on either side.

205
00:16:34 --> 00:16:36
And that can just be hard to manage.

206
00:16:37 --> 00:16:45
So, like, the nice thing about a layout is, like, if you're genuinely, like, changing, like, the position of the views and, like, they're, like, flexibly resizing, then you don't have to, like, throw away that state.

207
00:16:46 --> 00:16:49
Also, a plug for while we're here, custom containers.

208
00:16:49 --> 00:17:00
Like, the combination of custom layouts and custom containers is a really great way to make super flexible reusable controls that can, like, have all of these adaptations to different size classes, different layouts, like, all sorts of different things.

209
00:17:00 --> 00:17:03
And demystify SwiftUI containers from WW24.

210
00:17:04 --> 00:17:04
Fantastic.

211
00:17:04 --> 00:17:08
Matt's talk is a good one to go to for that.

212
00:17:09 --> 00:17:14
I think we're all talking about, like, different ways to maintain structural view identity.

213
00:17:14 --> 00:17:19
And the one that we almost hit was like the any layout as well.

214
00:17:19 --> 00:17:27
So if you're really doing something custom but you want a custom layout in compact and then you want a different layout in regular, you can use any layout.

215
00:17:27 --> 00:17:30
And all of those views will maintain their structural identity.

216
00:17:30 --> 00:17:32
And any layout wraps each of those.

217
00:17:32 --> 00:17:33
It's any layout, right?

218
00:17:33 --> 00:17:34
All the any layout?

219
00:17:34 --> 00:17:35
I think it is.

220
00:17:35 --> 00:17:36
It wraps.

221
00:17:36 --> 00:17:38
It can switch between those two layouts.

222
00:17:38 --> 00:17:39
It can animate between them too.

223
00:17:39 --> 00:17:40
It's very, very cool.

224
00:17:40 --> 00:17:41
It's all the structures.

225
00:17:41 --> 00:17:42
And you can use the system ones too.

226
00:17:42 --> 00:17:43
Yeah.

227
00:17:44 --> 00:17:49
Another couple of things to think about in this space, view that fits is handy.

228
00:17:49 --> 00:18:02
You want to not use view that fits in a whole bunch of views at once because it has to measure successive views to find the one that fits.

229
00:18:02 --> 00:18:05
It literally tries to ball and says, which of you fits in this container?

230
00:18:06 --> 00:18:08
But it can be at a higher level.

231
00:18:08 --> 00:18:10
It can be super handy for that.

232
00:18:10 --> 00:18:14
The other thing that I would plug is I think tab views are great for this.

233
00:18:16 --> 00:18:20
Two releases ago, it was going to be iOS 18 and aligned releases.

234
00:18:20 --> 00:18:32
We, Anna really, built tab views that are, along with others, tab views that are adaptive and can have a sidebar at regular size classes.

235
00:18:33 --> 00:18:36
And that's optionally collapsible, and they have all kinds of configuration options.

236
00:18:37 --> 00:18:46
And that's a really nice container that adapts nicely to narrow, compact-sized classes like on the phone with just the tabs or to larger ones.

237
00:18:46 --> 00:18:53
And so I think that's a great container view that's maybe less appreciated than it should be on larger devices.

238
00:18:53 --> 00:18:55
One question, though.

239
00:18:55 --> 00:18:58
Like, a lot of people will, you know, you think, okay, I need to respond to sizes.

240
00:18:58 --> 00:19:00
I want to use a geometry reader.

241
00:19:00 --> 00:19:12
I was wondering, like, Stephen, can you talk a little bit more about, like, when to think about using a geometry reader or, like, what the performance implications are and, like, how, you know, if an app is thinking I need to do something a little more precise, like, what they might want to think about when they're doing that?

242
00:19:12 --> 00:19:29
Yeah, so I'm actually a big fan of on geometry change specifically because I feel like most of the use cases where people are trying to, you know, react to size changes for things like resizing grid items, for instance, what they really want is, like, breakpoints for their various sizes.

243
00:19:30 --> 00:19:35
You know, like, if the view is, like, wider than 320, then I want to have, like, this particular layout for my cell.

244
00:19:36 --> 00:19:38
And if it's, like, in this range, then I want to have another kind of layout.

245
00:19:39 --> 00:19:42
And the nice thing about on geometry change is that it actually has two closures.

246
00:19:42 --> 00:19:48
The first one is the one that actually gets the real-time geometry information that fires on every frame.

247
00:19:48 --> 00:19:54
But then whatever you return from that is the thing that actually determines whether the action gets called.

248
00:19:54 --> 00:20:09
So basically, like, if you had a range, you know, where you're saying, like, 320 to 480, like, I want to use this size, then you can specify that logic and then, like, only need to relay out or, you know, change things when you actually cross over those various thresholds.

249
00:20:09 --> 00:20:13
So you take something that's running at frame rate and suddenly it just fires twice as you cross the thresholds.

250
00:20:13 --> 00:20:30
- Right, typically if I were on an older release using a geometry reader, I would really only do it in a background view for the most part because putting things inside of a geometry reader, it ends up being really expensive for the views that are subviews of the closure for that geometry reader.

251
00:20:30 --> 00:20:35
In general, on geometry change is gonna be a better tool to reach for. - Awesome.

252
00:20:35 --> 00:20:39
- Yeah, basically you don't want your views to be invalidating on every single frame.

253
00:20:39 --> 00:20:47
- Right, and for things where you're trying to do more complex stuff where you have the bounds and then want to do very specific placements, and I mentioned custom layouts, that's the tool I would go to there.

254
00:20:47 --> 00:20:51
So the combination of on-geometry change and custom layouts is going to get you so far.

255
00:20:51 --> 00:20:59
And there's a super cool thing in the talk on advanced graphics in SwiftUI this year, Hao-Chien's talk.

256
00:21:00 --> 00:21:22
He has such a good explanation of alignment guides and has this really neat example where he uses alignment guides to do something that I would have thought you could only do with a custom layout but um his explanation of it is just so neat what you can achieve with that and that is it's super efficient it's like layout once and it's done i mean guys

257
00:21:22 --> 00:21:42
are so powerful and so underused yeah it's like that layout once thing that just kind of made me think of something that the with on geometry change it's still even like the couple times that it fires like even if your condition mostly means it doesn't fire very much but the the second enclosure the only thing you can really do in there is still set another set some state or

258
00:21:42 --> 00:22:08
something so this whole system will take at a minimum of like two passes to lay out it's like one initial one and then one additional one once you change that state it needs to like run again whereas with like a custom layout you can really lay everything out in one one pass um so the next question i think we'll um we'll start with you on this one uh sam um the uh developer asks um any

259
00:22:08 --> 00:22:33
tips on how to properly learn data management and passing data between views uh in swift ui um are there good projects that would help us like strengthen our skills in that or how should we like increase our knowledge there it's a really good question um like the the at its core at least in in my mind uh data management in swift ui is really like making pieces of information and then

260
00:22:33 --> 00:22:53
trying to make your views as lightweight as possible such that they map that information to like view data um so honestly my my biggest recommendations in terms of like getting good at structuring that is like even just like building like self-contained like state machines and and logical components that you can use all throughout your app to uh to drive things and then

261
00:22:53 --> 00:23:06
anytime you see that like your view code is getting messy trying to like sort of bridge things back and forth try to see if that means you can like pare down that that like model or logic layer data so that it only has the things that it needs.

262
00:23:06 --> 00:23:11
I harp a lot on the fact that SwiftUI is very architecture agnostic.

263
00:23:11 --> 00:23:16
And the reason for that isn't there's no-- we don't want to make a choice.

264
00:23:16 --> 00:23:22
It's that we think there isn't one correct choice, and it really depends on the shape of the data for your specific app.

265
00:23:22 --> 00:23:27
So as far as learning goes, I'd say just build a lot of things that are trying to model very different kinds of data.

266
00:23:27 --> 00:23:47
And you'll quickly find you'll need very different patterns for each one of those cases to map them nicely onto these yeah i mean the thing the thing i think about there is um it's helpful to build sample apps in a domain that you're familiar with right so build an app for something that you care about you know maybe it's keeping track of your

267
00:23:47 --> 00:24:12
watercolor paints or um you know how often you have to feed your fish or whatever it is that you really care about um because then you're solving a problem that you have so you're motivated to work on it um and you understand the data and so you're not spending brain cycles trying to figure out what the data is about um you're just spending it on trying to solve the

268
00:24:12 --> 00:24:28
problem i i think one of the challenges of like going through a can tutorial that is about something someone else is excited about is you have to learn what they're excited about and what you're doing in your tutorial i run into all the time yeah it turns out people don't want to look at my apps about, like, floor tiles.

269
00:24:29 --> 00:24:34
I try to have just not good sample code to look through different terrazzo patterns.

270
00:24:34 --> 00:24:35
People aren't into that.

271
00:24:35 --> 00:24:38
You just need to find someone who, like, has your same interest in floor tiles.

272
00:24:41 --> 00:24:42
I will say that...

273
00:24:42 --> 00:24:43
Still looking, still looking.

274
00:24:43 --> 00:24:46
I'm also a big fan of using instruments for this.

275
00:24:47 --> 00:24:48
You know, not necessarily...

276
00:24:48 --> 00:25:05
Like, people typically use instruments for debugging performance, but the SwiftUI instrument is actually really powerful because it shows you a timeline of all of the things that are being updated so you can actually understand how the places that you're updating your data are affecting, you know, which view bodies are running

277
00:25:05 --> 00:25:09
and sort of it can kind of show you how the data is flowing through your app in that way as well.

278
00:25:09 --> 00:25:15
And you can also see the work that SwiftUI is doing as a result of the changes that you're making to your data.

279
00:25:16 --> 00:25:19
And that can kind of help you understand how things work and how stuff flows.

280
00:25:19 --> 00:25:24
I've also been peddling dynamic property to all who will listen this year.

281
00:25:25 --> 00:25:27
I finally learned the power of dynamic property.

282
00:25:28 --> 00:25:34
And it's hard to explain on a panel, but a lot of the times on change is very helpful in a pinch.

283
00:25:35 --> 00:25:45
But as your views grow and you have lots and lots of on changes, a good rule of thumb that I like any on change can probably be replaced by good usage of dynamic property.

284
00:25:46 --> 00:25:54
And then going back to Russell's point, that also saves you an extra render cycle because dynamic properties, their update method is called right before body.

285
00:25:55 --> 00:25:59
And so you take what was an update of the on change, updating state, and then re-rendering the view.

286
00:26:00 --> 00:26:04
And you move that into the dynamic property updates and the view renders, and you're done.

287
00:26:06 --> 00:26:16
Anytime you have any kind of on change or action-based closure in some extra state, you're incurring both extra memory to store that state, but then extra time to process that.

288
00:26:16 --> 00:26:18
Yeah, that's a great point.

289
00:26:19 --> 00:26:25
We had a question in the previous SwiftUI lab, which was about anti-patterns.

290
00:26:25 --> 00:26:29
And on-change isn't always the wrong move.

291
00:26:30 --> 00:26:42
Sometimes I'll be, like, looking at code in a workshop or something, and a developer will be using on-change in order to do a bunch of calculations and then update some piece of state.

292
00:26:42 --> 00:26:47
And it's sort of like trying to get back to imperative code and do the work there.

293
00:26:47 --> 00:26:55
And ideally, you'd have a data model that's doing that work, and it's just vending the new value as the other values of that data model change.

294
00:26:55 --> 00:27:16
you make it observable it updates efficiently um so there there are cases where on change makes sense because you have to broadcast to some external system or whatever but if you're using on change to mutate the state of the view that's almost always not the right move yeah there you know i i see a lot of people in various forums saying like oh you know x api is bad or

295
00:27:16 --> 00:27:35
whatever it is like you shouldn't use it generally if we we shipped an api it's because we think like it is useful and like you know yeah like you know unchanged any viewer things that i hear a lot of people saying like oh they're you know spooky you don't touch them um but i wouldn't so much put it that way i would say when you see uses of apis like that it's a sign that you should like look

296
00:27:35 --> 00:27:53
critically and make sure that the usage is justified like for any view are you actually erasing the type of the view are you using like a you know heterogeneous collection or are you just like you know wanting to make it easy instead of thinking through the generics and for unchanged like are you actually doing some react something reactive and that needs to be in like an imperative breakout point or you just using this to

297
00:27:53 --> 00:28:10
Like plum things through that you otherwise could use the environment or a dynamic property or something else to better We haven't mentioned on a pier and I feel like that's even more common than then on change actually And this is my favorite use case for dynamic property, which is that you know commonly if you're loading something like an image

298
00:28:10 --> 00:28:28
There's the possibility that you'll already have it in your cache And in that case you don't want to wait until on a peer to load your image because you're basically going to be guaranteed to Have a full rerender of your view Even if the image was already available and so because the update method of a dynamic property fires before the body renders

299
00:28:28 --> 00:28:47
You can actually turn a dynamic property into a property wrapper And then to the cache to value if you have it and then it's available to a body on its first run And then you don't have to do that round trip and that can make a really big difference difference. And there's a great example of that in the talk this year at DubDub on Lazy Stacks.

300
00:28:48 --> 00:29:12
So Renz talks about how you can actually, it's this exact example, how you can move that loading earlier in the process. And SwiftUI is smart enough that it can instantiate a view, it can process the layout, and then it can do the rendering in separate frames. And so as you're scrolling interviews about to come on screen if we can have it all laid out before it gets there

301
00:29:12 --> 00:29:49
can render super fast and avoid glitching when you scroll it's it's super cool and i the talk is so good i just love it um it is a really good talk there are a lot of a lot of depth in that talk while we're here and on up here my favorite feature of the year what the poet It's really subtle, but it was a lot of attention to detail paid by one of the engineers on the team to take advantage of new Swift concurrency features to make task and on appear have the same timing.

302
00:29:49 --> 00:30:00
So there was this really subtle behavioral difference where if you stack like a task and an on appear on a view, so you have view.task.onappear, the on appear would fire first because the task incurred in isolation.

303
00:30:00 --> 00:30:10
hop, and now that doesn't happen with the latest Swift concurrency features, and so they'll execute top-down, which is a nice ordering improvement.

304
00:30:10 --> 00:30:11
Awesome.

305
00:30:13 --> 00:30:17
So the next one, who should I throw this at?

306
00:30:18 --> 00:30:23
I think we'll start with Russell, because he raised his eyebrows at me.

307
00:30:26 --> 00:30:27
Let's go.

308
00:30:27 --> 00:30:29
And feel free to bounce it to someone else if you don't want it.

309
00:30:31 --> 00:30:42
A developer asks, I've heard in multiple places, including the last two Swifts UI group labs this week, that I shouldn't be using if to conditionally hide elements.

310
00:30:43 --> 00:30:44
So what's Apple's recommendation?

311
00:30:44 --> 00:30:47
And part of why I love this question is the example that was provided.

312
00:30:48 --> 00:30:53
So, for example, only at 9.41 should I show that it's 9.41.

313
00:30:53 --> 00:30:55
Otherwise, I shouldn't show something.

314
00:30:56 --> 00:30:58
Somebody pays attention to their Apple demos.

315
00:30:59 --> 00:31:01
It's always 9.41 somewhere.

316
00:31:01 --> 00:31:26
in apple park in apple park um if if you wanted to show 941 only at 941 uh then the best thing would be to add like an opacity modifier and or whatever transition you wanted which is part of why i love animations it's like one of the things i've worked on for so many years because the uh the animations often inform much more than just what happens when it moves they can inform the

317
00:31:26 --> 00:31:51
structure of the whole whole thing you're building anyway back to 941 um if you wanted to fade in and out at that time just use an opacity with a conditional in that modifier and uh have it be one at 941 and zero at any other time um so then it's always there uh but it it doesn't it disappears at the other times now if you need to like actually remove it from your layout so that it like doesn't

318
00:31:51 --> 00:32:08
take up space um i might pop to see who else has a good suggestion for how to handle if you're if you're doing something like that then it is fine to to conditionalize it right like if it actually you're actually removing the view and putting it back in right then you're gonna have to do you know new layout and all these things uh the reason to avoid doing that is we don't want to

319
00:32:08 --> 00:32:29
like you know have to reflow layout and like uh you know re-initialize state and things like that if the view is not actually going away and reappearing it's just like visually changing like with opacity um but if it if it really is being removed from the hierarchy and not just like hidden then yeah that's that's the time but what about in view modifiers like conditionals for

320
00:32:29 --> 00:32:54
for the content of a view modifier for instance like where people like if you had a 941 view modifier and like inside it was like you know like if 941 content dot overlay your text otherwise just content um what are the what are the implications of that so i mean for conditionalizing view modifiers right generally you want to make sure the view modifiers take arguments such that

321
00:32:54 --> 00:33:11
like you can pass in an argument that will do nothing and that's you see that with a lot of our hopefully most of our modifiers where like there will be like a value that we consider inert as in it will do nothing um just like one was for opacity yeah exactly right like if you call opacity one on something it doesn't modify the content at all and you want to be putting like

322
00:33:11 --> 00:33:31
the conditional expression not like an if statement inside of the the arguments and one of the the challenges and we see it all the time is if um a a conditional is inside the body of a view modifier then that view modifier gets applied all over the place and you just made those views relay out and as the person applying the modifier you don't really

323
00:33:31 --> 00:33:49
know that you're doing that and if it happens upstream you're very confused if you ever see the pattern of making an if modifier that basically is just a modifier around a conditional branch it's really dangerous for exactly that reason you can end up with all sorts of conditionals in places you don't expect them and that can cause

324
00:33:49 --> 00:34:09
state to get reset because if you switch a conditional branch you lose state because it's a new view and so you want to be very careful with that kind of stuff just uh like an interesting pattern when you're designing modifier apis is sometimes you have the inert one because it makes sense and then there are other swift drive modifiers that won't have an inert version and that's not an

325
00:34:09 --> 00:34:33
oversight it's more so uh in the spirit of that particular api so like certain styles are static in that you pass a type to them that's generic and that binds to that modifier and so you can't change that. And that's kind of intentional because SwiftUI uses that static information to render more efficiently. And so switching styles like that on the fly is something that

326
00:34:33 --> 00:34:37
you might want to do. If you really need to do that, that's more of an if-else type of thing.

327
00:34:39 --> 00:34:52
I think the place where it came up earlier in the week about avoiding if in particular was an if statement at the top level of a view that's in like a lazy stack or a for each.

328
00:34:52 --> 00:34:57
Because in that case, SwiftUI is counting the number of views.

329
00:34:58 --> 00:35:02
It's identifying the views and position based on how many there are.

330
00:35:03 --> 00:35:11
And if a view is, like, scrolled out of the screen but it had a conditional at the top level of it, we don't know whether that view should be there or not.

331
00:35:11 --> 00:35:17
And so we have to keep it around so that we can tell whether the conditional state changed.

332
00:35:18 --> 00:35:24
And so, again, Renz talks about that in the Lazy Stacks video this year.

333
00:35:24 --> 00:35:33
But that's a case where, you know, if you really do need that conditional, you should maybe wrap the whole thing in a stack so that there's always a view there and it's empty sometimes.

334
00:35:33 --> 00:35:47
Yeah, to provide a little additional clarity on that, since it can be hard to get a mental model for these things, the problem case for, like, lazy containers like List and Lazy Stack is when you have a view that could resolve to multiple subviews.

335
00:35:47 --> 00:35:49
So if you just wrote, like, a view builder, text, text, text.

336
00:35:50 --> 00:35:56
If instead you wrap that whole thing in a VStack, right, now that VStack is one resolved view, so you're always going to get one view out of it.

337
00:35:57 --> 00:35:59
And the same advice applies for things like AnyView.

338
00:35:59 --> 00:36:04
AnyView conceptually can have any type under the hood, so it could produce one view, it could produce 10 views.

339
00:36:04 --> 00:36:06
Custom layout is the same.

340
00:36:06 --> 00:36:07
Yeah, exactly.

341
00:36:07 --> 00:36:17
So if you have any of those things, if you wrap them in a unary container like a VStack or some layout, you'll be guaranteed to always produce one view, and then you'll get the fast path for those lazy containers.

342
00:36:17 --> 00:36:21
But if you just make your own view type, that doesn't do that, right?

343
00:36:21 --> 00:36:23
Like if you return something from body.

344
00:36:23 --> 00:36:26
No, that is, yeah, you can return multiple views from body.

345
00:36:26 --> 00:36:26
Exactly.

346
00:36:27 --> 00:36:27
Right.

347
00:36:27 --> 00:36:28
And that's still multiple views.

348
00:36:28 --> 00:36:30
And that's still multiple views.

349
00:36:30 --> 00:36:30
It's still multiple views.

350
00:36:30 --> 00:36:31
Right.

351
00:36:31 --> 00:36:32
And like group is like that as well.

352
00:36:32 --> 00:36:33
It doesn't flatten.

353
00:36:33 --> 00:36:34
And there's a reason for that, right?

354
00:36:34 --> 00:36:44
Like you could imagine you'd make a custom view that you want to be able to pass to a list and have it expand out to multiple list rows instead of just like one list row that's surrounded in like a stack or something like that.

355
00:36:44 --> 00:36:44
Right.

356
00:36:44 --> 00:36:47
So that's important functionality and it's very intentional.

357
00:36:47 --> 00:36:49
we have to be careful about it when working with lazy containers.

358
00:36:50 --> 00:37:04
Yeah, well, the other data flow thing that I think not as, like, people, if you've been developing in SwiftUI, I think most people have heard of the environment, but I think something that people have sometimes heard about less are preferences and traits, which can flow upward.

359
00:37:04 --> 00:37:08
And the other thing is traits are accessible to kind of like your surrounding container.

360
00:37:09 --> 00:37:11
And so if you put it- Traits, you mean container values?

361
00:37:11 --> 00:37:12
Yes, container values.

362
00:37:12 --> 00:37:13
Sorry, thank you.

363
00:37:15 --> 00:37:16
More naming.

364
00:37:16 --> 00:37:16
More naming.

365
00:37:16 --> 00:37:17
More naming, yeah.

366
00:37:17 --> 00:37:22
Container values, so they're accessible to your surrounding container.

367
00:37:22 --> 00:37:30
So the one thing to know is if you're wrapping something in a container, like a VStack, that will mean that a container outside of it can't read those container values.

368
00:37:30 --> 00:37:35
So that might be desirable, or that might not be, but that is one thing to take a look at when you're debugging.

369
00:37:36 --> 00:37:47
And that actually, both of these last two things, the counting the number of views that a view actually expands to, and the container values API are covered in that demystify containers.

370
00:37:48 --> 00:38:13
session from 24 which is just it's super cool um it's also karaoke themed so it is we love karaoke um the next one i'm going to throw to throw to you sam um and this is a bit a bit of a segue you mentioned any view any view in that conversation we moved on from it but let's go back to it um what's the what's the concrete cost of type erasing with any view um if it's in

371
00:38:13 --> 00:38:33
a deeply nested or frequently updating view like yeah when should i worry about it when should i not so um you know i mentioned with uh like the if else case right like it's sometimes you need to like switch the view that you're showing and then that's okay um with any view in general there are places where you need to type erase views right it could just be that you know at compile

372
00:38:33 --> 00:38:52
time you don't know what the type of something is going to be um and in cases like that any view is a fine thing to reach for the case to look out for is when the the underlying type would change because when if the underlying type were to change like let's say it starts out as a text and then you change it to, you know, my special view, when that change happens, just like if else,

373
00:38:52 --> 00:39:11
you have to sort of tear down the view hierarchy and then rebuild it. And that is not super efficient. So if you can, try to avoid changing the underlying type as much as possible, and that'll get you in good performance cases. And then the other one is exactly the thing which I was just mentioning, which is if you're in a lazy container, you need to be careful because it won't be able to

374
00:39:11 --> 00:39:17
tell the surrounding lazy container how many views it resolves to. As mentioned, you can resolve that by wrapping it in a stack.

375
00:39:19 --> 00:39:19
All right.

376
00:39:23 --> 00:39:28
So I'm going to throw this one to you, Nick, and you can throw it to someone else if you'd like.

377
00:39:30 --> 00:39:38
So the question is, are there any tips for making SwiftUI layouts more adaptable now that Xcode previews are resizable?

378
00:39:38 --> 00:39:47
We talked about this a little bit, but is there anything with custom layouts that maybe we should think about differently along these lines?

379
00:39:48 --> 00:39:50
I'm going to need some time to think about this.

380
00:39:51 --> 00:39:52
This is like an abstract question.

381
00:39:52 --> 00:39:57
It's kind of one of those, like, give me a little more about kind of what you're doing.

382
00:39:58 --> 00:40:00
Yeah, it's tough to say without, like, specifics here.

383
00:40:01 --> 00:40:02
But, yeah, sorry, go ahead.

384
00:40:02 --> 00:40:02
Oh, go ahead.

385
00:40:02 --> 00:40:05
I think you have more to say about this than I do, so I'll let you start.

386
00:40:06 --> 00:40:11
I mean, I feel like when you're writing layouts, so sometimes in your app you may be writing, like, a really bespoke layout.

387
00:40:11 --> 00:40:14
And you may have been thinking, like, okay, this is going to be a particular size.

388
00:40:14 --> 00:40:14
Like, I know.

389
00:40:15 --> 00:40:16
Maybe I hard-coded some things.

390
00:40:17 --> 00:40:17
It happens.

391
00:40:17 --> 00:40:18
We've done it.

392
00:40:19 --> 00:40:20
But it's, what?

393
00:40:21 --> 00:40:21
Not me.

394
00:40:23 --> 00:40:26
Stephen is perfect and Obi writes amazing code.

395
00:40:27 --> 00:40:29
But, like, the size is an input parameter.

396
00:40:30 --> 00:40:41
So if you set up your layout such that you're kind of prepared to accept any size and do something that is reasonable in any of these sizes, then, like, you're really already well on the way.

397
00:40:41 --> 00:40:43
And I would probably, you know, test that.

398
00:40:43 --> 00:40:45
Like, you have, you know, now a way to test that.

399
00:40:45 --> 00:40:49
I would kind of test it at all the different sizes, see do you like the results that you're getting.

400
00:40:49 --> 00:40:51
If not, then maybe, you know, evaluate what you're doing.

401
00:40:51 --> 00:41:11
but honestly you're probably in a pretty good place if you're writing a custom layout right and that that actually i i think is a brilliant way to think about this is like actually the question is kind of backwards right now that you've got resizable previews resize your app and see what happens find out where you made assumptions find out where the layout breaks

402
00:41:11 --> 00:41:31
and then go in and find where you put that magic number that causes this to break and and figure out how you need to react to it and also just like use your app in context where it is resizable like on ipad and right you know it you really quickly find out when you're trying yeah iphone mirroring you really quickly find out when you're like trying to resize your app and use it like when

403
00:41:31 --> 00:41:50
it doesn't quite work in certain situations and then it's much easier to patch that stuff you use other people's apps i like to like apps and apps and websites just resize them and see how different people deal with this it's one of my most favorite parts of like looking at a lot of indie apps is any developers in particular they get to do their own you know they're not

404
00:41:50 --> 00:42:10
bound to a product team or a design team and so they get to be really creative with uh how their app adapts and so there are a lot of apps out there that do something really special and compact and then you you wind them up and you're like oh this is very creative use of space so cop out answer figure out what your app should do by looking at other apps and figuring out what you like

405
00:42:10 --> 00:42:38
inspiration right take inspiration from other apps all right ah this is uh this is a uh challenging one. We'll start with you on this, Anna. You might choose to throw it to Russell because this is a topic all three of us have discussed, I think. But the question is, what's your advice for presenting full screen SwiftUI overlays on top of everything else in the app, including sheets?

406
00:42:39 --> 00:42:43
Is there any way to do this that doesn't interfere with modal presentation?

407
00:42:43 --> 00:42:44
Look at the wheel turning.

408
00:42:45 --> 00:42:47
This is a hard question.

409
00:42:47 --> 00:42:50
All three of them go into, like, loading spinner mode.

410
00:42:51 --> 00:42:56
I can elaborate more because this has come up in our workshops sometimes.

411
00:42:56 --> 00:43:05
A common case where people face this problem is they'll want to present a login interface over the app.

412
00:43:05 --> 00:43:16
And so if you happen to get logged out because of a network disconnect or something like that, teams will want to bring up a UI over the top of the existing UI.

413
00:43:17 --> 00:43:20
And I'm not actually sure there's a way to accomplish that in SwiftUI.

414
00:43:21 --> 00:43:26
Or I think you would have to be probably pretty coordinated about your navigation model and your data model.

415
00:43:26 --> 00:43:36
Because a full-screen cover, once you've presented it, that will take up the whole screen provided kind of if you think of it as a little hierarchy.

416
00:43:36 --> 00:43:41
If you're at zero and you want to present something on top of zero and that's a full-screen cover, you're good to go.

417
00:43:41 --> 00:43:49
but if you have, like this question said, another modal presentation, and maybe you have another modal presentation presented, like that's gonna be trickier.

418
00:43:49 --> 00:43:56
So I think, at least if you wanna be using SwiftUI with the APIs currently as is, I would probably try to track the presentations and see.

419
00:43:56 --> 00:44:01
But we'd also probably welcome the feedback for your use case so we can make this better.

420
00:44:01 --> 00:44:02
- Yeah, for sure.

421
00:44:02 --> 00:44:06
- I mean, this is where I, I know it's a SwiftUI lab.

422
00:44:06 --> 00:44:08
- No, it's a UI frameworks lab too.

423
00:44:08 --> 00:44:11
- We love interop, we love frameworks being friends.

424
00:44:11 --> 00:44:12
We love frameworks.

425
00:44:12 --> 00:44:14
Drop some UI window on us.

426
00:44:15 --> 00:44:16
Thank you for the invitation.

427
00:44:17 --> 00:44:19
I feel safe saying that now.

428
00:44:21 --> 00:44:37
Yeah, I mean, because even in UIKit, even if you were to do UIKit applications with UI at all, it's difficult to present something or to get something on top of other presentations in your app, especially if you don't have a central way to manage all your presentations and don't know if there's anything there.

429
00:44:38 --> 00:44:52
And so the easiest way maybe to drop down to UIKit and you can make a new UI window if you're using UI kits, scene lifecycle, then you have access to the scene and you can edit it with a scene and you can position it on top of things.

430
00:44:52 --> 00:44:58
And so if you have a HUD or a very specific use case that really needs to be over something, then you can drop down and do that.

431
00:44:59 --> 00:45:04
And you can put SwiftUI in there so you could make its root view controller a UI hosting controller and then do anything you want.

432
00:45:05 --> 00:45:07
And it probably wouldn't be so bad.

433
00:45:07 --> 00:45:08
It should work.

434
00:45:08 --> 00:45:10
There's plenty of people doing it.

435
00:45:10 --> 00:45:19
It does then kind of lead you into another area where, well, what if something wants to draw on top of that?

436
00:45:19 --> 00:45:25
I do find this way of thinking can sometimes feel like, well, I'm the number one thing.

437
00:45:25 --> 00:45:31
Of course, this thing should always be on top of everything, which is true until you think of something else that needs to be on top of everything.

438
00:45:32 --> 00:45:37
Or a person on your team who didn't know that you thought your view needed to be on top comes along.

439
00:45:37 --> 00:45:38
Right.

440
00:45:38 --> 00:45:42
And then if you don't, I think it'll be last UI window wins.

441
00:45:43 --> 00:45:48
So if you have two teammates making UI windows, whichever one is last wins.

442
00:45:48 --> 00:45:50
And that's the whole thing SwiftUI is trying to solve.

443
00:45:50 --> 00:45:55
And I think that actually gets at a really interesting way of thinking about problems like this sometimes.

444
00:45:57 --> 00:46:00
It's kind of a reducto ad absurdum.

445
00:46:00 --> 00:46:09
It's like, oh, actually, we can't have a distributed decision-making process about which window's on top, right?

446
00:46:09 --> 00:46:14
There has to be some source of truth for what layering we're doing there.

447
00:46:14 --> 00:46:21
And if it's just all the different pieces of code can say, no, now it's me, that's a bug waiting to happen.

448
00:46:22 --> 00:46:25
And so think about whether the design brief is right here.

449
00:46:25 --> 00:46:28
Maybe that's not the right way to design the app.

450
00:46:28 --> 00:46:38
Maybe whatever you would present the full screen cover for should actually be a more elegant flow, right?

451
00:46:38 --> 00:46:45
Maybe it is rewinding the navigation stack to some base view and then restoring that navigation when they log in.

452
00:46:45 --> 00:46:47
There's other ways to approach that problem.

453
00:46:48 --> 00:46:52
I'm not saying it's always wrong to present a full screen cover, but think about it.

454
00:46:52 --> 00:46:53
It feels very disruptive to the user.

455
00:46:54 --> 00:47:01
And in general, for any of these things where you do need to drop down to UIKit for these sort of things, that is not a bad thing.

456
00:47:01 --> 00:47:05
We build our frameworks with real interop in mind for exactly this reason.

457
00:47:05 --> 00:47:08
There are cases that will work better in one framework, better in the other.

458
00:47:08 --> 00:47:16
But if you find things that you really want to accomplish in SwiftUI and you need the drop-down to UIKit for them, follow the feedbacks about that.

459
00:47:16 --> 00:47:20
We want to hear where you're running into those corners so we can ship some APIs to make them better.

460
00:47:21 --> 00:47:22
Excellent.

461
00:47:23 --> 00:47:25
So this question is along a similar line.

462
00:47:25 --> 00:47:27
I think I'll start with you on this one, Anna.

463
00:47:27 --> 00:47:33
So the question is, what's the best way of building a pure SwiftUI custom control?

464
00:47:33 --> 00:47:36
And I think we'll come back to pure SwiftUI before we're done with this.

465
00:47:36 --> 00:47:38
But for now, let's take it at face value.

466
00:47:38 --> 00:47:48
A pure SwiftUI custom control, like a dropdown, that can expand on top of other elements and be interacted with, but doesn't impact the layout of those other elements.

467
00:47:49 --> 00:47:52
So how can I go about building a custom control like that?

468
00:47:53 --> 00:47:54
Yeah, I mean, definitely.

469
00:47:54 --> 00:47:57
So I think first, like, I would think about, does this need to be custom?

470
00:47:57 --> 00:47:58
Does this not need to be custom?

471
00:47:58 --> 00:48:00
Kind of like going back to what we were talking about.

472
00:48:01 --> 00:48:13
If there's a control that exists in UIKit or exists in AppKit and SwiftUI doesn't have it and you're considering building your own custom one in SwiftUI, I would probably think about using the framework interop and just using that UIKit or AppKit control.

473
00:48:13 --> 00:48:24
Because then if things change in the framework, like this year we made some tweaks to the new design, you just get that for free and you don't have to then go and make updates in your app.

474
00:48:24 --> 00:48:27
But if you want to do that, that's definitely a very fun question.

475
00:48:27 --> 00:48:47
SwiftUI is like definitely pretty compositional and so you can kind of compose a lot of those building blocks to build really powerful things I think one principle I would say that is like good to think about here because the question is kind of like impacting the layout of other elements is so like the way that SwiftUI layout works is you know like the parent asked the child for

476
00:48:47 --> 00:49:06
its size but it's kind of a proposal the child can still decide you know to draw like bigger to draw smaller and it like reports its size so one thing that you can do here and I would love a double check just to make sure this can get kind of complicated but you can effectively like you know you get proposed a size but you might you know tell your child actually to draw a bit

477
00:49:06 --> 00:49:24
bigger than that hey like you're cool to draw this big but then you tell your parent I'm only this size so like so you might be lying a little bit but that means that you know you can like lay outside you can lay outside of those bounds effectively and do an overlay that isn't going to affect the layout because you're just reporting a stable size.

478
00:49:24 --> 00:49:29
But also you can use SwiftUI overlays or kind of like a higher level convenience on top of that.

479
00:49:31 --> 00:49:33
Yeah, I'd go so far as to say that's not even like lying.

480
00:49:34 --> 00:49:44
That's kind of the intent of SwiftUI's layout system, right, is that parents propose sizes to children and then the children get to look at the proposal and say, no, I'm actually this size or I do fit in those bounds and I'm going to go here.

481
00:49:45 --> 00:49:46
Or rather, I do fit in this size.

482
00:49:46 --> 00:49:48
I'm going to be this big and the parent can place it.

483
00:49:48 --> 00:49:54
There is one case where that can, well, there may be more than one, But I know it's one case where that can get you into trouble.

484
00:49:54 --> 00:50:11
If you're drawing outside of your reported bounds or outside of the layout frame that you've been given and you're in a scroll view, the system may remove your view from the view hierarchy because it thinks your layout bounds are off screen.

485
00:50:11 --> 00:50:16
But you have a protuberance sticking out into the screen and suddenly it disappears.

486
00:50:16 --> 00:50:18
And so combined – Talk about that.

487
00:50:18 --> 00:50:19
Yeah, it does.

488
00:50:20 --> 00:50:22
Yeah, and so I learned that this year.

489
00:50:23 --> 00:50:23
It's a good time.

490
00:50:23 --> 00:50:25
So that's even a gotcha with overlay, too.

491
00:50:25 --> 00:50:31
Because I was going to go with overlay just because overlay, you know that you won't affect the surrounding layout.

492
00:50:32 --> 00:50:34
You're very isolated in that render.

493
00:50:34 --> 00:50:42
But also, to that point, if you're, like, overlaying a view big and your host view is small and it scrolls up off the screen, that's a tricky one.

494
00:50:42 --> 00:50:43
Yeah, yeah.

495
00:50:43 --> 00:50:44
I need to watch Renz's talk.

496
00:50:45 --> 00:50:45
Yeah.

497
00:50:46 --> 00:50:47
It's really good.

498
00:50:47 --> 00:50:48
All these design talks that were really good this year.

499
00:50:49 --> 00:50:52
And that's, I mean, that's another reason to stick with custom controls.

500
00:50:52 --> 00:50:56
Like there are all these interesting corner cases you have to consider.

501
00:50:57 --> 00:51:03
And so, you know, maybe the dropdown menu should actually collapse when the view that's hosting it scrolls, right?

502
00:51:03 --> 00:51:08
Because the person's demonstrated intent not to interact with that because they're scrolling it away.

503
00:51:09 --> 00:51:13
Or maybe it should stay and they're trying to scroll it so they can see more information.

504
00:51:13 --> 00:51:20
There are these really fine corners that you need to consider when you build your own custom controls.

505
00:51:20 --> 00:51:21
Swipe actions are a really good example of this.

506
00:51:22 --> 00:51:28
The container for swipe actions is responsible for coordinating all of the various actions.

507
00:51:28 --> 00:51:37
And when you scroll, you'll notice that if it's swiped, basically, that it'll dismiss so that it's detecting that intent that you're scrolling away.

508
00:51:37 --> 00:51:40
And it's all those fine little details like you were saying.

509
00:51:40 --> 00:51:46
And thank you for bringing up swipe actions because it is my favorite API that's new this year.

510
00:51:47 --> 00:51:52
And Stephen and Julia's What's New in SwiftUI talks about this.

511
00:51:52 --> 00:51:59
But previously you could apply swipe action modifier to rows in a list but nowhere else in SwiftUI.

512
00:52:00 --> 00:52:06
And now you can make any container a container that supports swipe actions by putting swipe action container on it.

513
00:52:06 --> 00:52:07
I think it's a very literal name.

514
00:52:08 --> 00:52:12
And now you can add swipe actions there, which I think it's so much fun.

515
00:52:13 --> 00:52:18
Before we move on fully, I do want to add one note, which is, you know, some of the things we said are like, oh, use system standard controls.

516
00:52:18 --> 00:52:19
And I totally agree.

517
00:52:20 --> 00:52:23
But I also don't want to discourage people from making their own controls.

518
00:52:23 --> 00:52:24
Like, yes, it's complicated.

519
00:52:24 --> 00:52:25
There are a lot of things to consider.

520
00:52:26 --> 00:52:27
It's why it's a serious investment.

521
00:52:28 --> 00:52:33
But sometimes they can be, like, you know, the little touches of magic that, like, turn an app from, like, good to great.

522
00:52:34 --> 00:52:37
But I encourage you to think very critically when you're doing this.

523
00:52:37 --> 00:52:43
Like, am I adding, like, real value to the experience by designing this as a custom control, or is it better as system standard UI?

524
00:52:43 --> 00:52:49
Because using system standard UI means you get, like, an experience people are immediately familiar with.

525
00:52:49 --> 00:52:55
But if your app has a very specific case where you can do something more, then don't be afraid to drop down and get your hands dirty.

526
00:52:55 --> 00:53:15
And the other thing I bring up, and it's something that Jason mentioned in our last panel, if there is an ability to add custom styles for that, reach for a custom style before you wrap a control and make a custom control because you're going to get all the accessibility support and everything, and you're just controlling how it looks.

527
00:53:16 --> 00:53:31
And so that's a great way to approach sort of that style that you might want to go for, not to overload the term, I guess, while still getting all the built-in behaviors you would expect from a built-in control.

528
00:53:31 --> 00:53:47
Well, the other thing that I thought was interesting about what he said in that was not only do you get that, but also you can use all of the normal control initializers, so you don't have to create a million ones. That was a point I just think styling is really cool because of the points you just mentioned, but also that initializer

529
00:53:47 --> 00:53:48
benefit, not having to maintain it, is really nice.

530
00:53:49 --> 00:53:53
How many initializers does Button have? A lot.

531
00:53:53 --> 00:53:53
Many.

532
00:53:54 --> 00:53:58
There's one fun thing I want to share about swipe actions in particular.

533
00:53:59 --> 00:54:16
I don't know the technical term, but the small circle buttons that exist As you swipe it open if you're using that outside if you're using the swipe actions modifier those can be any view now So it's not just a label. It's not just a text, but you can I wouldn't recommend it But you can put a navigation split view in there

534
00:54:25 --> 00:54:35
Like your own your own custom view it does again it won't just be limited to my window Wouldn't UI window a UI view subclass?

535
00:54:35 --> 00:54:39
I mean, dangerous experiments in SwiftUI.

536
00:54:41 --> 00:54:42
Don't return one from your view representable.

537
00:54:44 --> 00:54:45
All right.

538
00:54:45 --> 00:54:48
I think we can take a couple more questions.

539
00:54:48 --> 00:54:49
We're getting near the end of our time.

540
00:54:50 --> 00:54:52
Stephen, I'm going to direct this one to you.

541
00:54:53 --> 00:54:55
So it's well known, and thanks.

542
00:54:55 --> 00:54:57
We've done our good job of communicating.

543
00:54:57 --> 00:55:02
The breaking large SwiftUI views into smaller views benefits SwiftUI's performance.

544
00:55:02 --> 00:55:05
But how small should they be?

545
00:55:05 --> 00:55:15
And is there a best practice when we should make new struct views versus just adding at view builder computed properties?

546
00:55:15 --> 00:55:15
Right.

547
00:55:15 --> 00:55:23
So the way that I like to think about this is that breaking things into smaller views is not just about making smaller pieces.

548
00:55:23 --> 00:55:28
It's actually about isolating the pieces of data that are actually responsible for those views updating.

549
00:55:29 --> 00:55:40
So the example that I like to use is, like, let's say that you have a view that has a header and some content and a footer, and the header could update independently, the content could update independently, same with the footer.

550
00:55:40 --> 00:55:50
You wouldn't want to be in a situation where the entire view is being invalidated and the body is running for everything when only one of those pieces of data changes.

551
00:55:50 --> 00:55:55
And so that's a case where it would make sense to maybe split that into three different views for, you know, header, content, footer.

552
00:55:56 --> 00:56:07
If you're in a situation where the dependencies are kind of shared across everything, then splitting things up isn't really going to give you any benefit at all because you're doing the same amount of work.

553
00:56:07 --> 00:56:22
So the way to think about it is that the goal is to, at the initialization point in a view body, to provide a boundary where SwiftUI can basically do its comparison and say, actually, there's nothing new here, so I don't need to actually do this work.

554
00:56:22 --> 00:56:32
And that's why using, you know, view builders as computed properties instead of views, you don't get that benefit because you're basically declaring the same thing as one giant view body.

555
00:56:33 --> 00:56:33
Right.

556
00:56:34 --> 00:56:40
You're, like, taking your implementation and you're, like, moving it out of line, but it's functionally the same as if it's all just combined into one.

557
00:56:40 --> 00:56:42
And so all the dependencies are still there.

558
00:56:43 --> 00:56:43
Right.

559
00:56:43 --> 00:56:53
I love this question, so I sort of like even like summarize it for myself a little bit, that you want to like, if you can, break things into smaller groups of dependencies.

560
00:56:54 --> 00:57:07
So you could think of a view as like it runs as often as any of its inputs change, like any of the things you pass into it, bindings, environment, like whatever you read in body, when any of those things change, the entire thing is going to be re-evaluated.

561
00:57:07 --> 00:57:28
And so if there's, like, one thing that changes all the time and eight things that change very infrequently and only a tiny part of your view body that changes based on that one thing that's changing frequently, if you can move that bit out, then when that frequent thing is changing and the original body doesn't need it anymore, then it won't run at all.

562
00:57:29 --> 00:57:37
Now, if you, like, had to pass it through, then that body would still run because it's being passed through.

563
00:57:37 --> 00:57:39
But there's other ways to jump over.

564
00:57:39 --> 00:57:44
That's a case where observable comes in handy because the pointer to the observable doesn't change.

565
00:57:44 --> 00:57:46
And so it doesn't change the super view.

566
00:57:46 --> 00:57:52
But the property that that little tiny sub view is reading out does cause it to redraw.

567
00:57:52 --> 00:57:55
So you can skip the big stuff up above and just do the little one up.

568
00:57:56 --> 00:57:57
And don't be afraid of having lots of views.

569
00:57:57 --> 00:57:59
Views are structs.

570
00:57:59 --> 00:57:59
They're value types.

571
00:57:59 --> 00:58:00
They're cheap.

572
00:58:01 --> 00:58:04
We shouldn't be afraid of having lots of smaller pieces.

573
00:58:05 --> 00:58:06
All right.

574
00:58:06 --> 00:58:09
We're nearing the end, but I wanted to get to this question.

575
00:58:09 --> 00:58:12
I think it might be a good closer for us.

576
00:58:13 --> 00:58:16
So, Sam, I'm going to start with you, and we'll just swing across the panel.

577
00:58:16 --> 00:58:16
Okay.

578
00:58:17 --> 00:58:25
So, having used SwiftUI since day one, SwiftUI 1, maybe, the framework has truly come of age.

579
00:58:25 --> 00:58:26
Thank you.

580
00:58:27 --> 00:58:38
Looking back at the evolution from the early state and the navigation pain points to the mature declarative engine we use today, what are y'all most proud of?

581
00:58:39 --> 00:58:42
Oh, man, that's really tough.

582
00:58:43 --> 00:58:45
What am I most proud of?

583
00:58:47 --> 00:58:50
I would say it's a combination of two things.

584
00:58:51 --> 00:58:58
I think the first one is just like a smaller thing that I got to work on that I'm really proud of, which is the custom containers APIs.

585
00:58:58 --> 00:58:59
I talk about them a lot.

586
00:59:00 --> 00:59:05
And it's a very complicated thing to express that I think we managed to distill down to its essence.

587
00:59:05 --> 00:59:10
I'm really happy with how that turned out and how the cool things people have made with it.

588
00:59:11 --> 00:59:14
And then the other is more about the overall, like, shape of the framework.

589
00:59:14 --> 00:59:29
We've done a really good job of making sure that we think about things in terms of, like, a little bit, like, more semantic pieces rather than, like, oh, this is going to appear in this specific way on the device, thinking, okay, what is the thing?

590
00:59:29 --> 00:59:30
Like, you know, a tab view.

591
00:59:30 --> 00:59:34
A tab view can either be the standard thing you've seen or it can be, like, a paginating scroll view.

592
00:59:34 --> 00:59:39
but ultimately they're the same pieces data flow wise.

593
00:59:39 --> 00:59:49
And it's been really cool getting to rethink the platform's UI systems in terms of this new way of thinking about them, how is their data flow arranged.

594
00:59:49 --> 00:59:52
And I'm really proud of the job we've done with putting the pieces together there.

595
00:59:52 --> 00:59:54
- Russell, do you have something?

596
00:59:54 --> 01:00:05
- Yeah, I can just quickly say that I'm most proud of how it's grown and how we've been able to use it more and more internally as someone who's worked on UIKit for a very long time.

597
01:00:05 --> 01:00:22
You could poke around inside of like, UI navigation controller and the debugger and you would see that we're starting to use SwiftUI to build some of the new core UI components that are used across all of the apps, even apps that have not been rebuilt using SwiftUI.

598
01:00:22 --> 01:00:28
It's really nice to see it reach a level of maturity that we can use it in such broad systems.

599
01:00:28 --> 01:00:34
- I'm blank and the only thing that's coming into my head is just like, I'm just really proud of the team.

600
01:00:34 --> 01:00:46
A lot of people on the team have been around since day one and a lot of people are newer and it's just such a nice group of people and the way they think together and I just love seeing people like laugh about horrible API things.

601
01:00:48 --> 01:00:49
Like tonight.

602
01:00:49 --> 01:00:49
Yeah.

603
01:00:51 --> 01:00:58
It's like building that over some amount of years is just as hard as building the framework and so that's something that I really like about it.

604
01:00:58 --> 01:00:59
Nice, I love that.

605
01:00:59 --> 01:01:17
I like how we've taken, you know, things that end up being suboptimal like for instance, Geometry Reader, figuring out the reasons why people are using them, and actually providing new APIs that solve those problems, things like on geometry change, on scroll visibility change, all of these places where people would reach for something,

606
01:01:17 --> 01:01:20
like really figuring out those cases.

607
01:01:20 --> 01:01:22
I really like seeing that adaptation.

608
01:01:22 --> 01:01:32
- I think it's just been really cool to see over the course of the years how more of the lower level building blocks have been exposed to allow people to be able to build their own custom things.

609
01:01:32 --> 01:01:36
'Cause I feel like some of the stuff that we get to use internally developing for SwiftUI are really cool.

610
01:01:36 --> 01:01:41
And it's cool to also give those tools to the broader world and see what people build.

611
01:01:41 --> 01:01:45
- Also, of course, now that we've all said this, I'm sure all of us are thinking of like 10 better answers.

612
01:01:45 --> 01:01:46
- No, no, no.

613
01:01:46 --> 01:01:47
- Bring it around again.

614
01:01:47 --> 01:01:49
- Like they're just flooding into my mind.

615
01:01:49 --> 01:01:51
Hopefully that'll suffice.

616
01:01:51 --> 01:01:52
- We'll save those for the next group loud.

617
01:01:52 --> 01:01:53
- Exactly.

618
01:01:53 --> 01:01:55
- So we've reached the end of this one.

619
01:01:55 --> 01:02:01
And we're so thankful that you all joined us today and hope you found this helpful and maybe entertaining.

620
01:02:01 --> 01:02:07
A big thanks to our panelists and to all the folks We're working hard behind the scenes to make this happen this evening.

621
01:02:07 --> 01:02:21
If we didn't get to your questions, please visit the developer forums at developer.apple.com slash forums to continue the discussion and visit feedbackassistant.apple.com to file any bugs or feature requests and let us know about your use cases.

622
01:02:21 --> 01:02:28
Speaking of feedback, you should receive an email with the survey link to let us know about your experience with all of WWDC.

623
01:02:28 --> 01:02:31
We'd love to incorporate your feedback into future events.

624
01:02:31 --> 01:02:33
Again, thanks for joining us.

625
01:02:33 --> 01:02:35
I hope you have a great WWDC.
