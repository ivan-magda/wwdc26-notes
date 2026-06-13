---
title: visionOS Group Lab
source: https://developer.apple.com/videos/play/wwdc2026/8004/
session: 8004
collection: wwdc2026
duration: 01:03:19
fetched: 2026-06-12
via: whisper.cpp ggml-large-v3 (session SD video audio)
transcription: machine-generated, no speaker diarization
---

> Machine transcription of the **visionOS Group Lab** SD video via whisper.cpp (`ggml-large-v3`).
> Group labs ship no Apple transcript; this is generated from the audio. Cues are timestamped segments, **not** speaker-attributed turns.

## Transcript

1
00:00:03 --> 00:00:06
Hello, everyone. Welcome to the VisionOS Group Lab.

2
00:00:07 --> 00:00:11
My name is Adarsh Pavani. I'm a technology evangelist for Apple Vision Pro.

3
00:00:12 --> 00:00:16
I'm a part of the worldwide developer relations team over here at Apple.

4
00:00:16 --> 00:00:22
I'll be your host for today, and I'm joined by an expert panel on VisionOS.

5
00:00:23 --> 00:00:33
These are folks from the Vision Products Group, which is the engineering team that was responsible for building Apple Vision Pro and all the subsequent VisionOS versions.

6
00:00:33 --> 00:00:35
Let's go through a quick round of introductions.

7
00:00:35 --> 00:00:36
You want to start off, Katie?

8
00:00:36 --> 00:00:37
Yeah, fantastic.

9
00:00:37 --> 00:00:38
Hi, Katie.

10
00:00:38 --> 00:00:42
I help developers build apps for Apple Vision Pro and Vision OS.

11
00:00:42 --> 00:00:43
Hi, my name is Norman.

12
00:00:43 --> 00:00:49
I work on all the technologies related to rendering, simulation, and gaming on Vision OS.

13
00:00:49 --> 00:00:58
This includes RealityKit, Reality Composer Pro, USDKit, and the latest 4V streaming.

14
00:00:58 --> 00:00:59
Hi, I'm John.

15
00:00:59 --> 00:01:01
I'm an engineer on the RealityKit team.

16
00:01:01 --> 00:01:06
And this year, I also had the privilege of working on the new USDKit framework.

17
00:01:06 --> 00:01:07
Hi, I am Matt.

18
00:01:07 --> 00:01:16
I sit within the Vision Products Group focused on everything related to professional and enterprise applications, as well as gaming and interactive with third parties.

19
00:01:16 --> 00:01:25
I'm Travis. I work in Vision Products Group, and I focus on software standardization strategy, and I work quite closely on technologies like USD.

20
00:01:26 --> 00:01:28
I also have a background in media and streaming.

21
00:01:29 --> 00:01:30
Thank you, everyone.

22
00:01:31 --> 00:01:40
In addition to the ones that you see on screen, we have a team behind the scenes that is going to help us answer your questions.

23
00:01:40 --> 00:01:44
I already see some questions rolling in, so thank you for submitting those.

24
00:01:45 --> 00:01:50
If you have code-specific questions, please do take those to developer forums.

25
00:01:51 --> 00:01:53
That's developer.apple.com slash forums.

26
00:01:53 --> 00:01:57
If you have bug reports or feedback, that's very welcome.

27
00:01:58 --> 00:02:02
please do share that with us on feedbackassistant.apple.com.

28
00:02:02 --> 00:02:09
It's the feedback that you've shared over the years ever since the launch of Vision Pro that has made it into an amazing platform.

29
00:02:10 --> 00:02:15
And we have some exciting new features that we have announced with Vision OS 27.

30
00:02:16 --> 00:02:17
That's the latest Vision OS version.

31
00:02:18 --> 00:02:22
We have a completely redesigned Reality Composer Pro.

32
00:02:22 --> 00:02:25
We have many new features in Reality Kit.

33
00:02:25 --> 00:02:29
Brand new frameworks like Spatial Preview Framework.

34
00:02:29 --> 00:02:53
or USDKit. We also have a foveated streaming framework alongside support for enhanced object tracking and accessory tracking. So there's a lot. I probably won't be able to read out all of the new things that we have introduced this year, but it has become possible only because of the feedback that you've submitted to us. So please keep that going. So with that, let's kick

35
00:02:53 --> 00:03:15
it off uh i'll start with what was some of the uh what were some of the features that were your favorites uh katie do you want to kick it off and uh share what was your favorite feature in vision 27 yeah okay i've thought about this a lot since yesterday um i have two i'm gonna go with one spatial panos on vision 27 unbelievable to like view your panos that you took on iphone

36
00:03:15 --> 00:03:33
in a whole brand new way on vision 27 fantastic and then separately um just like a general apple thing. Xcode is amazing this year. Fully embraced agentic development. It's unbelievable. I'm very happy to get away from the command line and get fully back into Xcode. Fantastic updates there.

37
00:03:34 --> 00:03:46
I don't have to cheat like Katie. One of my favorites from VisionOS 27 is physical space lighting. We came a long way in terms of being able to understand the environment you're in.

38
00:03:47 --> 00:03:53
So when ARKit was first released, we have the ability to understand the room you're in, right?

39
00:03:53 --> 00:04:00
The physical light direction, color temperature, light positioning, and being able to generate an environment map.

40
00:04:00 --> 00:04:11
But this is the first year we actually are able to affect the lighting you see here in your physical space by introducing the new physical space lighting API.

41
00:04:11 --> 00:04:18
So you'll be able to create that immersion that, to the customers, you wouldn't be able to tell what's real versus what's fake.

42
00:04:18 --> 00:04:30
When I first try out this feature, some of the demo I've seen with a flashlight coming out of the portal, shining on my walls, shining around me, I almost had a sense of I can feel the warmth of these lights on my face.

43
00:04:30 --> 00:04:38
So that was really, really delightful for me to see how the developers, like you all, are able to take it to the next level.

44
00:04:38 --> 00:04:39
That's a great one.

45
00:04:39 --> 00:04:41
John?

46
00:04:41 --> 00:04:47
I'm really excited about USDKit this year.

47
00:04:47 --> 00:05:08
USD is such a fantastically expressive 3D model format, and I'm really excited about it being much more easy to use, easy to pull into an app that works with 3D models, a lot easier to make your app export an expressive USD scene on all kinds of Apple platforms.

48
00:05:09 --> 00:05:11
So I'm really excited to see what people do with that.

49
00:05:12 --> 00:05:14
Katie did too, so I'm doing too.

50
00:05:14 --> 00:05:16
That's fine. That's cool. I set the precedent.

51
00:05:17 --> 00:05:22
Sometimes I think that, like, the most magical features are just these little ones, the quality of life.

52
00:05:22 --> 00:05:30
So I'll start there, and I think that, for me, that's the ability to do high-quality recording on Vision OS directly in device for the first time.

53
00:05:30 --> 00:05:43
So for those less familiar, today when you do a screen recording on Apple Vision Pro, historically it has been foviated, meaning in the same way that when we're showing content to the user, we optimize the content in the region where the user is looking.

54
00:05:44 --> 00:05:57
Now, when you are taking a screen recording, that screen recording won't be embedded with that foveation, meaning that if you want to do a high-quality capture, whether it's for marketing materials or it's to put on your App Store page, you actually have the ability to do that directly on Vision Pro.

55
00:05:58 --> 00:06:07
Historically, the way that you would do that is you plug in via the developer strap and you use the developer capture feature, but a lot of people don't have developer straps, and it's an additional piece of equipment you have to bring with you.

56
00:06:07 --> 00:06:19
So now, within settings, you can actually toggle that on so you can do up to a three-minute fully unfoviated capture that makes just capturing the beauty of your VisionOS experience for traditional platforms that much easier and that much cleaner.

57
00:06:20 --> 00:06:20
So that's one.

58
00:06:21 --> 00:06:23
I think the other one, I'm sure we'll be talking more about it.

59
00:06:23 --> 00:06:32
We launched two new features this year really focused around how you can expand applications that exist on the computer to Vision Pro.

60
00:06:32 --> 00:06:36
So one of those two, a personal favorite of mine, is the foviated streaming framework.

61
00:06:36 --> 00:06:42
Back, actually, in 26.4, a few months ago, we announced foveated streaming with NVIDIA's CloudXR.

62
00:06:42 --> 00:06:45
And we're expanding upon that this year with VisionOS 27.

63
00:06:46 --> 00:06:56
And really what that's all about is in the professional sector specifically or really high-end gaming workflows, people have always agreed that Vision Pro is this incredible standalone device.

64
00:06:56 --> 00:07:05
But for people that had existing workflows or existing workstation setup to play high-end simulations and games, they didn't have the ability to use that with their Vision Pro.

65
00:07:05 --> 00:07:26
they had two different devices. So now thanks to foveated streaming, we can actually stream wirelessly from content that's being rendered offline to Apple Vision Pro. And thanks to this foveation, we're still able to maintain the privacy of the user. The app is still not aware of where the user is looking, but we can benefit and actually optimize the streaming based on the

66
00:07:26 --> 00:07:41
approximate region where the user is looking. So this opens up a tremendous amount of possibilities for bringing additional content to Vision OS. And it's also not either or. It's not like if you're going the foveated streaming route, you can't continue to use reality kit content. You have the ability to blend the best of both worlds, and it's a truly magical experience.

67
00:07:41 --> 00:08:05
Awesome. Yeah, for me, it's spatial preview. It's a really exciting opportunity to see the Mac and Vision Pro kind of working together to kind of step function what you can do in terms of visualization and experience. And plus, I have a soft spot in my heart because preview at one time at Apple was my product. And then the other thing I think is really interesting is, you know,

68
00:08:05 --> 00:08:14
Over 10 years ago, we made an investment in USD as a technology, and it's excellent for expressing scenes with complexity and advanced materials.

69
00:08:14 --> 00:08:20
But there's an aspect of USD that's often forgot that's fundamental to technology, which is fostering collaboration.

70
00:08:21 --> 00:08:33
And you're seeing that in the ability for people to manipulate objects in Vision Pro and have that seamlessly sync back to apps like Preview or other developer opportunities there.

71
00:08:34 --> 00:08:45
So it's going to be really fantastic to see that as a first step along the way of not only introducing new collaborative paradigms, but then additionally exploring the full capabilities of a technology like USD.

72
00:08:46 --> 00:08:50
Excellent. Those were a lot more than what I started out with.

73
00:08:51 --> 00:08:53
So thank you for sharing all of your favorite features.

74
00:08:53 --> 00:08:57
Let's turn to some of the questions that we are receiving from the community here.

75
00:08:58 --> 00:09:04
I have a high-voted question, highly upvoted question, here from Wes Matlock.

76
00:09:04 --> 00:09:17
It says, as an independent developer, what is the best way to gain access to the front-facing cameras for learning and trying something for an app?

77
00:09:18 --> 00:09:22
I understand that you need to be a part of an enterprise development group.

78
00:09:22 --> 00:09:28
Am I missing something, or is it just impossible for an individual developer to gain access?

79
00:09:28 --> 00:09:30
Matt, do you want to take that one?

80
00:09:30 --> 00:09:30
Yeah, absolutely.

81
00:09:30 --> 00:09:38
So when we launched Vision Pro, we knew that there would be desire from certain developers to be able to leverage the cameras.

82
00:09:38 --> 00:09:41
And we first announced the ability to access the main camera.

83
00:09:42 --> 00:09:46
We actually initially just launched access to the left main camera.

84
00:09:47 --> 00:09:50
And then last year with Vision OS 26, we launched access to the stereoscopic pair.

85
00:09:50 --> 00:09:51
So you have the ability to do it.

86
00:09:52 --> 00:10:05
The primary way in which we've provided the ability for developers to approach this is really through the demonstrating what their application is going to be doing, primarily with a professional and enterprise-oriented target.

87
00:10:05 --> 00:10:07
So it's a little bit nuanced.

88
00:10:07 --> 00:10:10
I do want to suggest that we have a few different types of developer programs.

89
00:10:10 --> 00:10:13
This is not limited to folks in the Apple Developer Enterprise Program.

90
00:10:14 --> 00:10:19
This is available to developers who want to apply who have standard Apple Developer Program accounts.

91
00:10:20 --> 00:10:25
Usually that's for people that have incorporated a business or have their developer account attached to a business.

92
00:10:25 --> 00:10:29
But it doesn't mean that you yourself have to be within the enterprise program themselves.

93
00:10:30 --> 00:10:37
But if you are yourself having issues and you have a desire and you have a real use case on why you want to use this and you can't access it today, please just post in the forum.

94
00:10:37 --> 00:10:41
We're always looking for new ways to provide access to these features for developers.

95
00:10:42 --> 00:10:54
For the folks who have tuned in, if you have use cases that you think will benefit from having access to the front-facing cameras, please do share that with us on Feedback Assistant.

96
00:10:55 --> 00:10:58
That will be of good learning for us.

97
00:10:59 --> 00:11:00
So appreciate that.

98
00:11:01 --> 00:11:02
All right, moving on to the next question here.

99
00:11:03 --> 00:11:06
It's from Thomas Bastable.

100
00:11:06 --> 00:11:09
It's also a highly upvoted question.

101
00:11:09 --> 00:11:38
hitting a breakpoint in an immersive space means standing inside a frozen universe of your own making squinting at xcode through pass through i would love to know how how you guys debug on device as i feel like i'm missing a trick here wants to take that one it's a really good question you've really liked xcode go tell me about it i'm getting made fun of non-stop by my team i imagine

102
00:11:38 --> 00:11:56
so there is a developer setting as of I think last year that allows you to use Mac virtual display within an immersive environment so for example if this developer is like building an app where they're surrounded by I don't know some sort of virtual world like it sounds like they are they could you know be building and running

103
00:11:56 --> 00:12:20
to Xcode and have Mac virtual display up as well so when they hit that breakpoint they can very easily stay where they are and be safe because it is a safety issue of course at the end of the day and then also triage and debug it's very helpful I mean, internally, generally, like, I think a lot of us, like, when we are working in Xcode or we're exploring other applications, the workflow is primarily in Vision Pro with Mac Virtual Display and the immersive content alongside, right?

104
00:12:20 --> 00:12:21
That has been one of the common use cases.

105
00:12:21 --> 00:12:23
Yeah, it's a huge way to do that.

106
00:12:23 --> 00:12:25
I think it's so efficient as well, right?

107
00:12:25 --> 00:12:31
Because otherwise you're donning and doffing quite frequently, and Mac Virtual Display is, like, the best display that you can have.

108
00:12:31 --> 00:12:33
So why not do it all in one place?

109
00:12:33 --> 00:12:35
Plus a huge display.

110
00:12:35 --> 00:12:36
Yeah, it's incredible.

111
00:12:36 --> 00:12:37
Ultra-wide, right?

112
00:12:37 --> 00:12:37
Amazing.

113
00:12:38 --> 00:13:03
all right um moving on to the next question here um it's someone with the username uh judo tree uh the question is are any of the agent skills apple's providing this week especially relevant for vision os development okay do you want to take that one yeah for sure um i mean there are a couple discrepancies between an ios app and a vision os app right just by the nature of the

114
00:13:03 --> 00:13:22
different platforms and so i think of the skills that have shipped thus far um the one that's most directly relevant is to help resize your existing iOS app to VisionOS, right? Like if you're running an app on a 2D iOS screen, it's naturally going to be expecting a certain form factor, a certain size, maybe a certain amount of information density. And I believe that we shipped a skill

115
00:13:22 --> 00:13:31
that will enable pretty seamless translation of that UI for iOS to VisionOS in a way that, you know, maintains some of the best practices and principles for VisionOS.

116
00:13:31 --> 00:13:32
Great. Excellent.

117
00:13:33 --> 00:13:39
All right. Next question from Sismagia.

118
00:13:40 --> 00:13:42
I'm sorry if I'm mispronouncing something here.

119
00:13:42 --> 00:13:48
But the question is, what is your usual workflow for exporting 3D models into USD?

120
00:13:49 --> 00:13:58
Some tools apply different transforms or remove certain materials on complex models, the usual models being FBX or GLB files.

121
00:14:00 --> 00:14:01
Travis, do you want to take that one?

122
00:14:01 --> 00:14:19
Yeah, I mean, the best option is to start back in your 3D CC where you're actually creating the applications, and rather than transiting through another file format, which may put its particular format fingerprints on the file, is to see if you can start as far back as there and get an export out of that.

123
00:14:20 --> 00:14:32
And that may help, but there's going to be, obviously, variants in what 3DCCs do, how they build any of these files, not just USD, that sometimes can be a little bit complicated.

124
00:14:32 --> 00:14:42
And part of that is representative of where the state of the industry is as it increasingly uptakes USD as a format that's used for scene description and interchange.

125
00:14:43 --> 00:14:46
There is some variability there, unfortunately, and it can be challenged.

126
00:14:46 --> 00:14:56
But just looking at the question, I think trying to start with USD as early in the process or straight out of the 3DCC is what we recommend.

127
00:14:57 --> 00:15:16
One thing I will add is depending on your workflow, if you are trying to iterate your 3D models, I think looking at the loose file format of USDA versus USDC, those are really good to give you the ability to modify individual files without having to repackage up the entire scene.

128
00:15:17 --> 00:15:21
So that truly will accelerate some of the iteration process that you're trying to do.

129
00:15:21 --> 00:15:36
Yeah, and one thing that's actually kind of interesting and has a little bit of a standard segue is that one of the important things that's happening in the standards space in general is that a standard is a human-readable representation that normally a human reads and then translates for the computer.

130
00:15:37 --> 00:15:42
And now with modern LLMs, AI, they're able to do that in some ways for themselves.

131
00:15:43 --> 00:15:54
And so one of the important things that's recently happened in the USD community is the core specification for USD came out, which details in depth about the essential parts of USD.

132
00:15:54 --> 00:16:00
And now that's a way for LLMs to kind of understand that and then potentially apply that to your model.

133
00:16:01 --> 00:16:04
And so you might be able to ask your favorite LLM to help out there.

134
00:16:04 --> 00:16:05
That's right.

135
00:16:06 --> 00:16:06
Great.

136
00:16:06 --> 00:16:08
Moving on to another upvoted question here.

137
00:16:10 --> 00:16:14
It's with someone with the name Eric in the username.

138
00:16:15 --> 00:16:19
Can spatial accessories be tracked outside of the range of direct view?

139
00:16:19 --> 00:16:27
I'm imagining designing foot trackers, but obviously the user will not always be looking down at their feet.

140
00:16:29 --> 00:16:31
I'll take that one if I can add it as my third favorite feature.

141
00:16:33 --> 00:16:34
I'm going to have to have a third now.

142
00:16:35 --> 00:16:37
We're going to heart all the features in the release.

143
00:16:39 --> 00:16:46
We're super, super excited by all of the updates that we've made to both object tracking and accessory tracking with VisionOS 27.

144
00:16:47 --> 00:16:51
So specifically, I think specifically this question asks about spatial accessories.

145
00:16:52 --> 00:16:56
And one of the big things that we did with VisionOS 27 is we opened the spec up.

146
00:16:56 --> 00:17:01
Last year when we announced spatial accessories, there were two accessories that we supported.

147
00:17:01 --> 00:17:07
We supported the PlayStation VR 2 Sense controllers, and we supported the Logitech Muse, the stylus device.

148
00:17:07 --> 00:17:28
We've gone well beyond that now because we know that there are all these use cases, Whether they're more consumer-facing use cases being used for games or interactive experiences or on the enterprise side, there's a tremendous amount of use cases, whether they have to do with the alignment of a physical scene or physical prop to a digital environment or something that you need to hold in your hand and have high precision, even maybe if you're in a low-light environment.

149
00:17:28 --> 00:17:35
So one of the big things we announced yesterday is we've actually opened up the spec for new accessories to come to life.

150
00:17:36 --> 00:17:49
So there's a few companies, DF Robot, Micro E, that are actually selling now accessories that allow you to actually append an accessory onto an existing object, which makes it super easy.

151
00:17:49 --> 00:17:58
You can take a physical prop and attach, for example, the cap accessory to the rear of it, and now you have the ability to track a relative point relative to the physical device.

152
00:17:58 --> 00:18:09
But then we're also – these companies are also providing the boards themselves that have the infrared trackers on them as well as all the other components that would be required to be able to build your own accessory.

153
00:18:09 --> 00:18:11
If you are in the business of building hardware, you're 3D printing a prop.

154
00:18:12 --> 00:18:16
It makes it super easy to build your own accessory.

155
00:18:16 --> 00:18:17
So there's a great session on that.

156
00:18:17 --> 00:18:23
I highly recommend you take a look at the What's New in Object Tracking and Spatial Accessory session.

157
00:18:24 --> 00:18:30
But this question looks like it talks specifically around tracking objects that might be outside of direct views, for example, foot trackers.

158
00:18:30 --> 00:18:36
So I think the first thing I would say is that these accessories use a combination of two things primarily for tracking.

159
00:18:37 --> 00:18:49
There's obviously a Bluetooth connection between Vision Pro and the accessories, but then there is an IMU on board, an initial measurement unit, that is doing a very high-frequency tracking.

160
00:18:49 --> 00:18:56
And there's also this constellation of infrared emitters that the Vision Pro, the infrared cameras on Vision Pro are able to see, which is amazing because the user can never see those.

161
00:18:56 --> 00:19:01
And also in a low light environment, the Vision Pro can still see the IR light.

162
00:19:02 --> 00:19:14
So I'd say that like in a position where maybe they are occluded by either your body or they're further down, it really depends on what fidelity of tracking is required.

163
00:19:14 --> 00:19:22
I would say that, like, same thing goes if you're using a PlayStation VR 2 control and you put it behind your hand for a second, you're not going to fully lose tracking because the IMU takes over.

164
00:19:22 --> 00:19:33
But obviously, you want, ideally, based on the size of an accessory and the distance to that accessory, as well as the Vision Pro's infrared cameras being able to see it, you want to optimize those things to be able to track to the highest fidelity.

165
00:19:33 --> 00:19:49
And also new with VisionOS 27 to make this easier is that we actually have provided a debugging mode where you can see the actual infrared feed that Vision Pro can see so that you can actually see whether or not the Vision Pro is capable of seeing the accessories at the specified position that you're looking for.

166
00:19:50 --> 00:19:51
So that, I'd say, is a long way.

167
00:19:51 --> 00:19:53
We'd love feedback on this.

168
00:19:53 --> 00:19:56
We've heard from other folks' desire in being able to track feet.

169
00:19:56 --> 00:19:58
I'm really interested in understanding why that is.

170
00:19:58 --> 00:20:04
Is that really to do more like breakthrough, which we obviously provide for your upper body today, but we don't provide today for your lower body?

171
00:20:04 --> 00:20:08
So we'd love to also understand this use case a bit if you can file a feedback item for us.

172
00:20:09 --> 00:20:17
Yeah, and like Matt pointed out, there's a dedicated session that we have released with WWDC set of sessions yesterday.

173
00:20:18 --> 00:20:22
It's, I think, something that says explore enhanced object tracking.

174
00:20:22 --> 00:20:28
It doesn't call out accessory tracking in the title, but it does cover accessory tracking in the actual session.

175
00:20:28 --> 00:20:29
So do check it out.

176
00:20:30 --> 00:20:31
All right, moving on to the next one here.

177
00:20:32 --> 00:20:43
Again from Eric, when using Gaussian splatting, we noticed that the splats are culled if you move your face close or inside of the splat.

178
00:20:43 --> 00:20:51
You're trying to use GS to, or Gaussian splatting, to reconstruct an immersive scene through sensor data for telepresence.

179
00:20:52 --> 00:20:54
So the culling is not ideal.

180
00:20:54 --> 00:20:56
Is there a way to disable this?

181
00:20:56 --> 00:20:58
John, do you want to take this?

182
00:20:58 --> 00:20:59
Yeah, that's a great question.

183
00:21:00 --> 00:21:08
I'm not aware of any way to disable that vignette effect that you're talking about right now.

184
00:21:09 --> 00:21:12
I would definitely recommend filing a feedback.

185
00:21:13 --> 00:21:20
That sounds like a really interesting use case, and we'd love to work with you to try to find a way to make that work.

186
00:21:20 --> 00:21:22
Yeah, and I think that's an important aspect.

187
00:21:23 --> 00:21:24
You've heard give us feedback.

188
00:21:25 --> 00:21:28
A lot of these are new features, so they're the first version of the feature.

189
00:21:28 --> 00:21:29
It's beta 1.

190
00:21:30 --> 00:21:34
Yeah, and some of the stuff is in beta, which gives us the opportunity to potentially course correct.

191
00:21:34 --> 00:21:48
But I think new features, particularly things like Gaussian splats, which are very recent areas of visualization, although technically the technique is actually older than people think, it's really interesting to find out how people are going to use it.

192
00:21:48 --> 00:21:56
And on the basis of how they want to use it and deploy it in their application, the type of things they want to create are going to inform the ultimate feature set over time.

193
00:21:56 --> 00:21:59
And so, yeah, definitely get us a feedback request on that.

194
00:21:59 --> 00:22:03
We want to hear your voice on particular areas, new areas.

195
00:22:03 --> 00:22:10
I will also just add this vignetting effect is not just what we're limiting to Gaussian splats only.

196
00:22:10 --> 00:22:12
For any of the 3D models, we have the same level of treatment.

197
00:22:13 --> 00:22:25
One of the motivations is when the object is getting a little bit too close to the user's face, we want to have the ability to have an unobstructed user field of view.

198
00:22:25 --> 00:22:31
But obviously, if you have a different creative intent, please do let us know what Travis has mentioned.

199
00:22:31 --> 00:22:35
We would love to figure out how to incorporate this feedback for future releases.

200
00:22:35 --> 00:22:35
Yeah.

201
00:22:35 --> 00:22:44
And I think just to reiterate one thing that you all both said, when you're filing feedback, the more context we get in terms of the types of use case you're trying to solve, the better, right?

202
00:22:44 --> 00:22:48
Like, I want to use Gaussian splatting in RealityKit as one thing, but, like, those can take so many different forms, right?

203
00:22:48 --> 00:22:56
People want to use Gaussian splats to reconstruct this cup, which is very different than people wanting to use Gaussian splats to recreate an entire environment the size of an airplane hangar, right?

204
00:22:56 --> 00:23:05
So the more context we can get on what you're exactly trying to achieve, the more we can actually help use that feedback to motivate our engineers and priorities in terms of what we're building.

205
00:23:05 --> 00:23:12
Yeah, and if you run into a bug, a really simple sample project also goes a very long way because that helps us reproduce issues and then ultimately triage them and fix them.

206
00:23:12 --> 00:23:18
In addition to the sample project, like a video or a screenshot of what you're seeing oftentimes helps.

207
00:23:18 --> 00:23:23
Even if it's a feature request, like this is a perfect example where you're seeing some culling.

208
00:23:23 --> 00:23:32
It might be good to see at what distance were you with your object that you were using origin splatting with for us to better address that feedback.

209
00:23:32 --> 00:23:38
So please do attach any screen recordings or screenshots alongside a sample project if you can.

210
00:23:38 --> 00:23:42
And of course, as much detail as you can give in the feedback ticket.

211
00:23:42 --> 00:23:44
All right, moving on to the next question.

212
00:23:44 --> 00:23:48
It's a highly upvoted question again from Ethan.

213
00:23:49 --> 00:23:54
Is Gaussian splat rendering supported on platforms other than VisionOS?

214
00:23:55 --> 00:24:00
The documentation shows support for iOS, but Xcode does not show the symbols available.

215
00:24:02 --> 00:24:04
John, do you have an idea?

216
00:24:05 --> 00:24:06
Yeah, that's a great question.

217
00:24:06 --> 00:24:21
I definitely recommend filing a feedback if you have any issues or if the documentation doesn't appear to match what's-- or you can't get it working in Xcode, this is the perfect time for us to help you out.

218
00:24:21 --> 00:24:22
Right.

219
00:24:22 --> 00:24:27
Things are still in beta, so if something's not working as expected, yeah, do file for feedback.

220
00:24:27 --> 00:24:31
In general, Gaussian splatting should be supported across both iOS and VisionOS.

221
00:24:31 --> 00:24:37
But if you find things otherwise, do let us know.

222
00:24:37 --> 00:24:42
Right, next question here by Oliver Colon.

223
00:24:42 --> 00:24:44
Why aren't there user profiles like on Mac?

224
00:24:45 --> 00:24:49
I want to put on the headset and choose who I am.

225
00:24:49 --> 00:24:51
Owner, child one, child two, grandma.

226
00:24:53 --> 00:24:54
Matt, do you have?

227
00:24:54 --> 00:24:56
Yeah, I mean, I'll take that one.

228
00:24:56 --> 00:25:00
I think that there are many, many scenarios where people are always interested in sharing devices.

229
00:25:00 --> 00:25:03
And there's definitely the family example, which I think was called out here.

230
00:25:04 --> 00:25:07
And there's definitely, we hear similar requests within the professional workplace as well.

231
00:25:08 --> 00:25:10
I think generally, like, Vision Pro is an incredibly personal device.

232
00:25:11 --> 00:25:23
And in order to create the quality of experience that has been paramount to this overall product, it's really about, like, how do we ensure that we're providing the best experience for the primary user?

233
00:25:23 --> 00:25:24
That's where we started, at least.

234
00:25:24 --> 00:25:31
But the more feedback that we've gotten from folks being interested in sharing, we've started to make some progress towards that goal.

235
00:25:31 --> 00:25:37
The flip side of a product being very personal is that it's often not, by design, always the easiest thing to share.

236
00:25:38 --> 00:25:39
So there's some features we've done here.

237
00:25:39 --> 00:25:44
I think we look at it maybe more akin to like an iPhone or an iPad than maybe like a Mac that has profiles.

238
00:25:45 --> 00:25:53
But one thing I know we always get questions about the enrollment time that it takes when you're going through system enrollment to calibrate your eyes and hands.

239
00:25:53 --> 00:25:54
Oh, my God, this takes some time.

240
00:25:55 --> 00:25:56
I want to quickly share it between a friend and I.

241
00:25:57 --> 00:26:00
We've actually done huge progress, made huge progress towards that.

242
00:26:00 --> 00:26:10
So what I do when I'm sharing my device with my family or my colleagues is last year we announced the ability to actually save your enrollment to your iOS device.

243
00:26:11 --> 00:26:18
So if you have an iOS device running iOS 26 or later, you can actually, once you go through the enrollment process, you can transfer that enrollment to your iOS device.

244
00:26:19 --> 00:26:24
And now every time I put on somebody else's device and it's in guest user mode, it's as easy as putting on the device.

245
00:26:24 --> 00:26:27
My phone automatically pops up with the card with my app clip code on it.

246
00:26:27 --> 00:26:29
I look at it and I'm in their device immediately.

247
00:26:30 --> 00:26:52
So that's, I'd say, like the biggest lift, one of the biggest friction points that I think we've been able to alleviate quite a bit, as well as other things like the guest user with nearby device where if you have an iOS device, an iPhone or an iPad paired with the same iCloud as a Vision Pro, you can actually let somebody into your device without, fortunately, without needing to tell them your passcode, being able to screen mirror through AirPlay and see exactly what it is they're seeing.

248
00:26:52 --> 00:26:58
So that's how we've solved some of these friction points that exist with trying to share devices with your family or colleagues.

249
00:27:00 --> 00:27:01
Great. Thank you.

250
00:27:01 --> 00:27:02
All right.

251
00:27:02 --> 00:27:05
Next question.

252
00:27:05 --> 00:27:16
Is there any API to detect the lips and face of the owner and mimic that on 3D, like Persona, but differently?

253
00:27:16 --> 00:27:22
Do we have something like face tracking that we have on iOS, John, with Vision OS?

254
00:27:22 --> 00:27:27
The face tracking provided by ARKit is an iOS-only feature.

255
00:27:27 --> 00:27:31
But this sounds like a really fun use case.

256
00:27:31 --> 00:27:42
And I definitely recommend filing a feedback about this because the more use cases we have like this, we're very interested in this kind of thing.

257
00:27:42 --> 00:27:53
I think the only thing you can get, we're not providing raw face data or anything, but as a third-party app, you can call on the feed from what would be the equivalent of like a front-facing camera on an iPhone.

258
00:27:53 --> 00:27:58
And you can get a, it's not the full spatial persona, but you can get the, like a simulated video.

259
00:27:58 --> 00:28:00
It's a matted version of the persona, right?

260
00:28:00 --> 00:28:03
So it's a video feed with your persona in front of a background.

261
00:28:04 --> 00:28:08
So you could use that for like a video conferencing app or anything like that.

262
00:28:08 --> 00:28:13
But you can't, you know, do some sort of lens style effect on someone's face or anything like that.

263
00:28:13 --> 00:28:13
That's right.

264
00:28:13 --> 00:28:20
Another upvoted question here by someone with the username Dilliam.

265
00:28:22 --> 00:28:35
The question is, for visual intelligence on Apple Vision Pro, when Siri AI answers questions about content the user is looking at, "either app windows or real world objects.

266
00:28:35 --> 00:28:44
"Does a third party app need access to the main camera "or is visual intelligence system handled entirely "at the system level?"

267
00:28:44 --> 00:28:45
Katie?

268
00:28:45 --> 00:28:50
- Yeah, sure, that's a system level capability, so no information is shared with third parties, yeah.

269
00:28:50 --> 00:28:52
Totally done in a privacy preserving way.

270
00:28:52 --> 00:28:53
- It's actually pretty magical too.

271
00:28:53 --> 00:28:54
- Yeah.

272
00:28:54 --> 00:28:56
- You look at something and immediately get an answer.

273
00:28:56 --> 00:28:57
- Yeah, it's cool.

274
00:28:57 --> 00:29:00
- I was actually quite thrilled with like, do these shoes fit my bag?

275
00:29:00 --> 00:29:02
- Yeah, that was very cool.

276
00:29:02 --> 00:29:05
I've got to say, as a sneakerhead, that was a nice little thing to see personally.

277
00:29:05 --> 00:29:10
But also the new representation of Siri AI on VisionOS 27 is pretty fantastic.

278
00:29:10 --> 00:29:10
It's beautiful.

279
00:29:11 --> 00:29:12
Excellent.

280
00:29:13 --> 00:29:15
Another question here.

281
00:29:15 --> 00:29:26
For apps that support game controllers but aren't games, how does Apple envision controllers fitting into the long-term interaction model of VisionOS alongside eyes, hands, and voice?

282
00:29:27 --> 00:29:33
Are there new controller capabilities in VisionOS 27 that developers should be designing for today?

283
00:29:34 --> 00:29:35
Norman, you want to take that, or you want to?

284
00:29:35 --> 00:29:35
Sure.

285
00:29:36 --> 00:29:40
I think for us is like what Matt said, right?

286
00:29:40 --> 00:29:45
Initially, we opened PSVR, the Sense Controller, as well as the Logitech Muse.

287
00:29:46 --> 00:30:01
And it's not only we provide that as a standard sort of API by using both game controller framework and ARKit to get the orientation as well as button presses, but we also have the ability to fully integrate all these input devices fundamentally at the system level.

288
00:30:02 --> 00:30:12
So being able to use that to control a launcher app versus control how the window Chrome behaves, it is deeply integrated as part of the Vision OS.

289
00:30:12 --> 00:30:19
As what Matt said, we also, this year, opened up the spatial accessory as a new form of tracking capabilities, right?

290
00:30:19 --> 00:30:35
So being able to add all the simple ingredients and to be able to build your own accessory, get really high frame rate tracking, really accurate pose, that's one of the brand new capabilities that will continue down this path to support at a system and SDK level.

291
00:30:35 --> 00:30:40
But we also feel it's important, still, you know, gaze and pinch the things you already have.

292
00:30:40 --> 00:30:41
That's right.

293
00:30:41 --> 00:30:47
To make sure that if you're designing a solution that might want an alternate control, that you're very thoughtful about it.

294
00:30:47 --> 00:30:54
And it's adding some extended capability to the user to use something that's not what they already have.

295
00:30:54 --> 00:30:55
Yeah.

296
00:30:55 --> 00:30:56
That's what I was going to add, right?

297
00:30:56 --> 00:30:59
Like, the type of input depends on the type of experience.

298
00:30:59 --> 00:31:02
I feel like we keep hearkening back to it depends on what you're building, right?

299
00:31:02 --> 00:31:20
Like we want to know what you're building to be able to answer this question more thoroughly So please if you have specific questions post to the Apple developer forums But in general it really depends you could do sort of refined input with the Logitech Muse that Matt referenced You could build a great game using the the PlayStation VR controllers or you could just use natural input to build them

300
00:31:20 --> 00:31:32
You know different type of productivity. Yeah, or even like regular game controllers Potentially more integrated into the system on Vision Pro than on almost any other device in terms of being able to even move windows around to do things.

301
00:31:32 --> 00:31:40
I said this to a group yesterday, and there was a few ophthalmologists in the room, and they verified what I said, so I'm going to say it again in the case it sounds smart.

302
00:31:40 --> 00:31:40
Actually accurate.

303
00:31:41 --> 00:31:52
But, like, when you think about how we set out to build input for Apple Vision Pro, right, when we think about spatial computing, we wanted to best mimic the interaction systems that we use for the real world, right?

304
00:31:52 --> 00:32:04
If I want to grab this cup, the first thing that indicated that I wanted to grab this cup, even before the neurons fired to move my muscles to grab the cup, was the fact that my eyes led me to the cup.

305
00:32:04 --> 00:32:11
I looked at it, which is exactly why focusing on using gaze in your eyes for initial input as essentially your cursor.

306
00:32:12 --> 00:32:19
And then your hands, same way you interact with physical objects, no need to put down a controller as an abstraction layer between you and your virtual goods.

307
00:32:19 --> 00:32:24
if you want to be able to translate between moving with virtual and physical items, right?

308
00:32:25 --> 00:32:26
So that's why we started there.

309
00:32:26 --> 00:32:32
But then at the same – like the flip side of that token is in this world, like we use tools all the time, right?

310
00:32:32 --> 00:32:34
Like none of us are finger painting beyond a certain age.

311
00:32:35 --> 00:32:36
Well, speak for myself.

312
00:32:38 --> 00:32:41
So like that was the whole idea behind Spatial Accessories.

313
00:32:41 --> 00:32:45
It's like, well, yes, hands and eyes are fantastic for most things.

314
00:32:45 --> 00:32:48
There are times in which you want a device for precision inputs.

315
00:32:48 --> 00:32:57
There's a time where you want a device for haptics or there's a time where you want a device that allows you to have more easily can pull up multiple different features or menus like you'd have on a device like the Logitech Muse.

316
00:32:57 --> 00:32:59
So that was really the intro there.

317
00:32:59 --> 00:33:08
And the whole idea of opening up the spec with Vision OS 27 is really just to let developers and creators and tinkerers build everything that their brains can imagine.

318
00:33:09 --> 00:33:09
Awesome.

319
00:33:10 --> 00:33:13
You mentioned something about eyes and hands.

320
00:33:13 --> 00:33:20
One cool feature that I really love is in Safari, or actually in most other apps as well, there's look to scroll.

321
00:33:20 --> 00:33:23
So if you look at the bottom of the page, it scrolls automatically.

322
00:33:23 --> 00:33:26
It feels so magically, like somehow it knows that it will read fine.

323
00:33:28 --> 00:33:32
All right, moving on to the next question here, another upvoted question.

324
00:33:33 --> 00:33:37
Are there any updates to WebXR in Safari with VisionOS 27?

325
00:33:37 --> 00:33:40
In particular, immersive AR mode.

326
00:33:40 --> 00:33:46
We have just released a WebXR app, which we would love to run with immersive AR mode enabled.

327
00:33:47 --> 00:33:49
But we are currently limited to immersive VR only.

328
00:33:50 --> 00:33:57
The app for interest, and there's a link in here, but do we support this feature, WebXR in Safari?

329
00:33:58 --> 00:34:07
We do support WebXR in Safari, but it is an immersive-only rendering mode, right?

330
00:34:07 --> 00:34:12
So being able to follow the, it's part of the WebXR standardization.

331
00:34:12 --> 00:34:21
When we look at, you know, if there are specific AR-based use case, I think, Travis, this is something your domain to think about for the future.

332
00:34:21 --> 00:34:22
Yeah.

333
00:34:22 --> 00:34:27
And then also I think an important thing is, you know, obviously there's web API and web experience.

334
00:34:27 --> 00:34:28
Certainly appreciate that.

335
00:34:28 --> 00:34:30
And the developers may focus on it.

336
00:34:30 --> 00:34:30
Yeah.

337
00:34:30 --> 00:34:42
But we also have built a tremendously capable set of platform APIs that really utilize the full capabilities of an augmented reality style experience.

338
00:34:43 --> 00:34:59
And so while we fully understand where the question is coming from, it also may be worth just investigating the full capabilities of our platform API because there may be things you can achieve outside of the web domain that may enable you to take your idea even further than you may expect.

339
00:34:59 --> 00:35:06
Yeah, for example, this year we have released a model tag for all platforms, right?

340
00:35:06 --> 00:35:15
And also, in addition to the model capabilities within your web page, the web page can also serve as a personal environment, like being able to render that without all touching.

341
00:35:15 --> 00:35:28
And you can not only have the model element, can not only position a USD asset in page, but you can pull it out and place it in your environment, and it looks like it's there.

342
00:35:28 --> 00:35:35
And so we're already thinking along those terms, even in the web domain, but in a different pathway than WebXR.

343
00:35:37 --> 00:35:45
So if you have some specific features that you need support for, I know we have said this enough number of times, do file the feedback.

344
00:35:45 --> 00:35:52
But more importantly, what we are really looking forward to here is learning about how we can better address that.

345
00:35:53 --> 00:35:58
And knowing your use case, seeing what you're trying to build will definitely help.

346
00:35:58 --> 00:36:06
So if you can share that link that was posted with the question alongside any other context you can add, that will go a long way.

347
00:36:06 --> 00:36:06
Thank you.

348
00:36:08 --> 00:36:09
All right, moving on to the next question.

349
00:36:10 --> 00:36:14
Again, a highly upvoted question here by Raymond Yeh.

350
00:36:15 --> 00:36:23
For visual intelligence on VisionOS, is it purely camera-based or does it leverage the depth sensors under the hood?

351
00:36:24 --> 00:36:25
They want to know the magic.

352
00:36:25 --> 00:36:26
They want to know what's behind the curtain.

353
00:36:27 --> 00:36:30
Yeah, I don't know if we can get too into the weeds on that one.

354
00:36:30 --> 00:36:32
The magicians never reveal the secrets.

355
00:36:32 --> 00:36:34
Yeah, we can never steal the treats.

356
00:36:34 --> 00:36:38
But, you know, we've got some very smart folks who have combined data from multiple sources.

357
00:36:38 --> 00:36:46
One thing I would add is we aim to provide the best possible, most accurate result on all platforms as we can.

358
00:36:47 --> 00:36:47
Yeah, 100%.

359
00:36:47 --> 00:36:55
And, you know, we have different devices with different capabilities, different cameras versus different sensors on different devices as well.

360
00:36:55 --> 00:37:05
So there's not a single unique implementation, but we do look at a blend of all the sensors that's available on each unique device and provide that best result.

361
00:37:05 --> 00:37:06
And that's not unique to visual intelligence, right?

362
00:37:06 --> 00:37:07
That's right.

363
00:37:07 --> 00:37:12
All of the system-level functionality around simultaneous localization and mapping and hand tracking and everything, right?

364
00:37:12 --> 00:37:22
We're leveraging what sensor makes sense for the use case and taking that combined amalgamation of sensor data to provide the most magical experience to the user.

365
00:37:22 --> 00:37:26
100%, just in the same way that third-party developers do that when they build their app on multiple platforms, right?

366
00:37:26 --> 00:37:30
Like, you don't build the same iOS app for macOS, for vision OS.

367
00:37:30 --> 00:37:33
They're different based on the input methods, the type of platform.

368
00:37:33 --> 00:37:36
More importantly, the context of the query.

369
00:37:36 --> 00:37:36
Yeah.

370
00:37:36 --> 00:37:42
So depending on, like, whether it's a visual query, audio query, or something that involves hands, something that involves interaction.

371
00:37:44 --> 00:37:49
Moving on, Thomas Bastable has one more question here.

372
00:37:49 --> 00:37:53
The device sleeps the instant it leaves your head.

373
00:37:54 --> 00:38:00
Taking the debug session with it, What's the sanctioned way to keep it awake on a desk during development?

374
00:38:01 --> 00:38:01
Any tricks?

375
00:38:03 --> 00:38:04
We kind of covered this one earlier.

376
00:38:05 --> 00:38:06
When we were talking about what we do.

377
00:38:07 --> 00:38:11
The rationale for this isn't just like we're trying to make someone's life more difficult.

378
00:38:11 --> 00:38:16
There's a very, very strong rationale behind this, which is that there's no lock button on Vision OS or on Vision Pro.

379
00:38:17 --> 00:38:18
You take the device off.

380
00:38:18 --> 00:38:28
The expectation for anyone that's ever used the device is that that device is now locked and the next person that puts it on is going to have to authenticate, whether it's through Optic ID or it's through a passcode.

381
00:38:28 --> 00:38:38
So that's why this exists, and anything that would do something like a caffeination that you see on caffeinating a device like we have on other platforms puts that kind of like security model at risk.

382
00:38:38 --> 00:38:41
So that's why we haven't done it, but what we all do, I mean, do you want to speak more to your work?

383
00:38:41 --> 00:38:51
Yeah, I mean, I feel like we answered part of this earlier, but whenever I'm developing, I use Mac Virtual Display extensively, and that makes my build and iterate times super fast.

384
00:38:51 --> 00:39:03
It depends on what type of app I'm building, whether I'm building a predominantly SwiftUI-based app where I would use SwiftUI previews and just be building an Xcode and then deploying the device, or if I'm using Reality Composer Pro 3, any of those tools.

385
00:39:03 --> 00:39:06
Using MVD with Vision Pro is fantastic.

386
00:39:06 --> 00:39:16
I will also add, for me personally, if I have to remove my headset, having Optic IDE set up, it makes a world difference coming back.

387
00:39:16 --> 00:39:20
So if you haven't, that's one of the tips and tricks we use in addition to MVD.

388
00:39:20 --> 00:39:22
Yeah, absolutely.

389
00:39:22 --> 00:39:23
Cool.

390
00:39:23 --> 00:39:25
All right, next question here.

391
00:39:25 --> 00:39:32
Can you give us an update on automated testing for Vision OS and ARKit and RealityKit?

392
00:39:32 --> 00:39:35
Good question.

393
00:39:35 --> 00:39:38
John, you want to give that a shot?

394
00:39:38 --> 00:39:39
Yeah.

395
00:39:39 --> 00:40:03
The-- for RealityKit, I definitely recommend trying-- seeing if you can get some of your automated testing needs resolved using the RealityRenderer class, which is a pretty useful way of driving a lot of Realtek kits rendering through an automated test or programmatically in general.

396
00:40:03 --> 00:40:12
There's a couple of new videos, new session videos, this year that I would recommend checking out in general on automated testing this year.

397
00:40:12 --> 00:40:15
There is a migrate to Swift testing session.

398
00:40:15 --> 00:40:16
It's really nice.

399
00:40:16 --> 00:40:21
And there's a get the most out of Device Hub session as well.

400
00:40:21 --> 00:40:29
you can do a surprising amount of automated testing through Device Hub.

401
00:40:29 --> 00:40:34
That's definitely an angle I would recommend people investing in looking into.

402
00:40:34 --> 00:40:39
In addition to the new Device Hub, RealityKit is also written in Swift.

403
00:40:39 --> 00:40:48
So any of your existing infrastructure with Swift automated testing, Xcode infrastructure there, should all work out of the box.

404
00:40:48 --> 00:40:50
Excellent.

405
00:40:50 --> 00:40:51
All right.

406
00:40:51 --> 00:40:52
Next question.

407
00:40:52 --> 00:40:56
What are the biggest mistakes new vision-wise developers make?

408
00:40:57 --> 00:40:58
A quoted question here, Katie.

409
00:40:58 --> 00:40:59
Yeah, for sure.

410
00:41:00 --> 00:41:06
So if you guys recall, when we announced Vision Pro, there was a little bit of a lag time between the announcement and then the actual release of the product.

411
00:41:07 --> 00:41:13
And in that period, we had these workshops and labs where developers could come in and test their apps that they had been building for the simulator at that time on device.

412
00:41:13 --> 00:41:25
And I always think about that when something like this comes up because there were so many folks who came into those labs and were just astonished at how different it was to have their app go from the simulator to on-device.

413
00:41:25 --> 00:41:27
Things like information density, right?

414
00:41:27 --> 00:41:34
Like when you have this large window right in front of your face, you don't necessarily want the same amount of text or information density that you would have on other Apple platforms.

415
00:41:35 --> 00:41:37
So those sort of design considerations, I think, are huge.

416
00:41:38 --> 00:41:41
Also the size of your tap targets or your buttons or whatever it might be.

417
00:41:41 --> 00:41:46
It's just, it's different when you're engaging with a UI through your eyes and hands.

418
00:41:46 --> 00:41:46
It's completely different.

419
00:41:47 --> 00:41:52
We have a lot of really good design sessions over the last couple of years from the design evangelism team, your folks.

420
00:41:53 --> 00:41:55
Sarah, I think, had one last year, Linda as well.

421
00:41:56 --> 00:41:57
Just really great, useful content.

422
00:41:57 --> 00:41:58
The HIG has also been updated.

423
00:41:59 --> 00:42:04
But it's quite different to, you know, have an app that runs on a 2D platform and bring it into 3D.

424
00:42:04 --> 00:42:13
I would also say, like, outside of just a direct, you know, check the box deployment target update, you can take your app that is working great on iOS and bring new features to VisionOS, right?

425
00:42:13 --> 00:42:23
So, like, that's sort of that next step, that level up of, like, okay, if I'm making, like, an e-commerce shopping app, I might have a product display page with the information about a particular product.

426
00:42:23 --> 00:42:27
And maybe on iOS, I have an array of images that the user can kind of scroll through.

427
00:42:27 --> 00:42:31
But then on VisionOS, I have the ability to have a full 3D model that represents my product.

428
00:42:31 --> 00:42:32
Are you using your hand?

429
00:42:32 --> 00:42:32
Exactly.

430
00:42:32 --> 00:42:38
Yeah, and then point it out of the window and putting it onto my desk or putting it onto my floor, depending on what it is.

431
00:42:38 --> 00:42:42
It's such a magical way to allow a user to experience your app and experience your product.

432
00:42:43 --> 00:42:49
And it feels much more engaging when you kind of take it to that next level and you build something that is truly only possible in VisionOS.

433
00:42:49 --> 00:42:56
So I think the best VisionOS apps are ones that are only available, like that have features that are only available on VisionOS, right?

434
00:42:56 --> 00:42:57
The things that are new and novel.

435
00:42:58 --> 00:43:05
I think for, in addition to what you said on the VisionOS, is let's be creative, right?

436
00:43:05 --> 00:43:13
There are a lot of these experiences, I think, just barely scratching, we're in the phase of barely scratching the surface of spatial computing.

437
00:43:14 --> 00:43:20
Being able to have the superpower, understanding the physical room you're in, changing the lighting of your room, right?

438
00:43:20 --> 00:43:27
Being able to give you customers, your players, that special capability is like, I'm here.

439
00:43:27 --> 00:43:29
I now have superpowers.

440
00:43:29 --> 00:43:35
And being able to explore that rather than keep thinking about where we come from, technology, right?

441
00:43:35 --> 00:43:39
So we also seen this transition when we first roll out the iPhone, right?

442
00:43:39 --> 00:43:41
We introduced multi-touch.

443
00:43:41 --> 00:43:55
So rather than-- we also seen apps going through the transition of having these on-screen virtual controller game pad to truly take advantage of the touch surface, Like what we're seeing with Angry Birds years ago, right, being able to truly innovate on the space.

444
00:43:55 --> 00:43:59
So I would say this is just the journey has just started, right?

445
00:43:59 --> 00:44:03
We are barely scratching the surface, lots of new unique capabilities.

446
00:44:03 --> 00:44:08
We don't have to get limited to where we all come from previously.

447
00:44:09 --> 00:44:14
One thing, there may be just like content creators or media developers out there.

448
00:44:14 --> 00:44:28
And there's an interesting thing that I've seen is that when, you know, a lot of the encoding settings and visual quality that we build towards on our other platforms or for other delivery, such as television, you know, that display is at some distance from you.

449
00:44:29 --> 00:44:32
And so you will optimize for that viewing distance.

450
00:44:32 --> 00:44:37
But in Vision Pro, you have a flexible screen that you can make as big as you want and you can bring it quite close.

451
00:44:38 --> 00:44:51
And one thing I've seen quite frequently is encoding settings that look awesome normally when you deploy them on Vision Pro because it has such a high fidelity media reproduction that you begin to notice things like that.

452
00:44:51 --> 00:44:54
And this is not just in things like video content or movies.

453
00:44:55 --> 00:44:58
It could also be things in like the bitmap images that you're using in your app.

454
00:44:58 --> 00:45:00
They may work great in other regimes.

455
00:45:00 --> 00:45:03
You bring them in and you start seeing macro blocking and artifacts.

456
00:45:03 --> 00:45:04
So always look for that.

457
00:45:04 --> 00:45:10
Look critically at your, particularly your compressed content, and make sure it looks as awesome as it can on Vision Pro.

458
00:45:10 --> 00:45:19
I want to take a moment and point to Norman, your session from WWDC this year, Build Next Generation Experiences for VisionOS 27.

459
00:45:20 --> 00:45:27
That session covers different pathways that someone can take to bring existing experiences or maybe brand new experiences to VisionOS.

460
00:45:28 --> 00:45:33
It doesn't just cover all the native tools and frameworks like RealityKit, Reality Composer Pro.

461
00:45:34 --> 00:45:37
They are great, but you're not limited just by that.

462
00:45:37 --> 00:45:53
There is an option for you to use Unity or Unreal or Godot, or maybe if you already have an app on Mac or PC, you can stream those or stream some of the content using Spatial Preview or 4-way streaming.

463
00:45:53 --> 00:46:02
So all of this is covered in detail in your session, so do check out that session, Build Next Generation Experiences for VisionOS 27.

464
00:46:03 --> 00:46:07
It's a pretty good pathway discussion there.

465
00:46:08 --> 00:46:09
I have one more thing to add.

466
00:46:09 --> 00:46:10
Norman reminded me of something.

467
00:46:10 --> 00:46:15
I started developing apps March of 2008 when the iOS SDK first came out, right?

468
00:46:15 --> 00:46:16
I was so excited.

469
00:46:16 --> 00:46:21
And one of the things that was so interesting to me was that there was no right way to do things at that point in time, right?

470
00:46:22 --> 00:46:23
iOS had just come out.

471
00:46:23 --> 00:46:26
Apple had a perspective on what was correct for the platform.

472
00:46:26 --> 00:46:34
But then that perspective was affected by third-party developers who started building these amazing apps that I'm sure were not even considered by the folks at Apple at that time, right?

473
00:46:35 --> 00:46:36
Yeah, of course.

474
00:46:36 --> 00:46:37
A thousand percent.

475
00:46:37 --> 00:46:38
Yeah, absolutely.

476
00:46:38 --> 00:46:39
I think we can name a few in our heads.

477
00:46:39 --> 00:46:40
It might fit that.

478
00:46:41 --> 00:46:45
But I feel like very much we're in that stage now with this platform, right?

479
00:46:45 --> 00:46:47
Like what makes a great spatial app?

480
00:46:47 --> 00:46:51
We have one perspective, but I would leave it to the developer community as well to like develop their perspective.

481
00:46:52 --> 00:46:56
And when we have these events in person, we get to see the work that comes from the developer community.

482
00:46:56 --> 00:46:59
Like for me, it's some of the best times at Apple.

483
00:46:59 --> 00:47:10
Yeah, because you get to see, like, oh, wow, we shipped this API, and we expected it to be used in this one particular way, and it is being used in a completely different way, and it's, like, resulted in this amazing experience that we just hadn't thought about.

484
00:47:10 --> 00:47:13
So, again, when we ask for use cases for feedbacks, it's because we can't come up with everything.

485
00:47:14 --> 00:47:15
That's a good one.

486
00:47:15 --> 00:47:16
Yeah, 100%.

487
00:47:16 --> 00:47:18
Another upvoted question here.

488
00:47:19 --> 00:47:45
Agent-ic coding in VisionOS, this is regarding agent-ic coding in VisionOS, I tried agent-ic coding in Xcode to generate a VisionOS project, just hello world entity in a reality view following a local llm um wbdc 26 session but it generated the app with wrong syntax is there any way to fix these or teach the ai to know more

489
00:47:45 --> 00:48:02
about vision os code yeah i feel like i have to take that one because i was standing next to it really um so a couple things one we all know code generated by uh an llm might have errors i would think it's like pretty important to be deliberate about how we're prompting things i myself have run into this.

490
00:48:02 --> 00:48:03
Always double-check the output.

491
00:48:03 --> 00:48:05
Always double-check the output, for sure.

492
00:48:05 --> 00:48:07
Trust but verify, I would say.

493
00:48:07 --> 00:48:16
And then secondarily, we have a really robust library of sample projects that could be potentially used to feed as almost training data to the local LLM that this developer is using.

494
00:48:16 --> 00:48:17
So those would be the two things I would recommend.

495
00:48:17 --> 00:48:18
I don't know.

496
00:48:18 --> 00:48:19
You guys have any additional thoughts?

497
00:48:19 --> 00:48:44
I think for me, some of the LLM project I tried, it was quite amazing with VisionOS, being able to instantly create a game like Tetris using reality kit and all the APIs was all compiled out of the box I was really surprised how far we come along in terms of these AI development right but I think was was what Katie said is because everybody's evolving so fast right we

498
00:48:44 --> 00:48:58
have the API SDK new capabilities coming out some of the existing models might be trained on the previous years of API so there's a little bit of overlapping period that we might have to transition together.

499
00:48:58 --> 00:48:59
- So your LLM may vary.

500
00:49:01 --> 00:49:02
- That's right.

501
00:49:02 --> 00:49:02
- Double check.

502
00:49:02 --> 00:49:03
- Yeah.

503
00:49:03 --> 00:49:05
- I mean, to Katie's point, we have some amazing samples.

504
00:49:05 --> 00:49:17
We got Hello World, Petite Asteroids, the CanyonCrosser sample, and new in VisionOS 27, there's a few more amazing developer samples that might be super relevant to what people are specifically building.

505
00:49:17 --> 00:49:19
Don't remember the name just offhand.

506
00:49:19 --> 00:49:20
Maybe, Darsh, you can help me.

507
00:49:20 --> 00:49:25
But there's a great session that was done that really talks about complex 3D object manipulation.

508
00:49:26 --> 00:49:29
And it includes sample code and explains how you can take...

509
00:49:29 --> 00:49:30
Oh, the model manipulator sample.

510
00:49:30 --> 00:49:31
Thank you, the model manipulator sample.

511
00:49:32 --> 00:49:39
And especially on the professional enterprise side, we have so many companies really wanting to push the limits of what's possible with collaborative 3D design reviews.

512
00:49:40 --> 00:49:48
And we took some of our best practices in learning, the way that we use Vision Pro internally to literally design our products and our retail stores, et cetera.

513
00:49:48 --> 00:49:55
And we took those learnings and turned that into an internal sample that makes it so much easier for everybody to do super complex hierarchy manipulations, right?

514
00:49:55 --> 00:50:03
I mean, it's super cool to be able to go in and the example in the video in the session is an AirPods case.

515
00:50:03 --> 00:50:17
Super cool to go in and reach in and pull a piece, a component out, but when you've got hundreds of components in a hierarchy, it's important to be able to go down and tab down a hierarchy view or do an exploded view or do a cross-sectioning so you can grab the right component.

516
00:50:17 --> 00:50:29
So we, for example, in that use case where people are doing complex 3D object manipulation, Providing an LLM-specific sample there, where we provide best practices on here's how to build an app in this space, would be probably even further helpful.

517
00:50:29 --> 00:50:29
Yeah.

518
00:50:29 --> 00:50:33
And that sample, in particular, uses a lot of new APIs, including-- you mentioned cross-sectioning.

519
00:50:33 --> 00:50:35
That's a new reality kit thing that's coming out.

520
00:50:35 --> 00:50:35
Yeah.

521
00:50:35 --> 00:50:43
The session you were referring to is Collaborate on Structured 3D Models in Vision OS.

522
00:50:43 --> 00:50:43
Wonderful.

523
00:50:43 --> 00:50:47
So yeah, do check that one out.

524
00:50:47 --> 00:50:49
Another question, probably this one's for you, Matt.

525
00:50:49 --> 00:50:53
For object tracking, how fast can it get now?

526
00:50:53 --> 00:50:55
What will be a possible frame rate and latency?

527
00:50:55 --> 00:51:03
Maybe I'll start with I was looking at the demo when this was getting made for the session.

528
00:51:03 --> 00:51:11
And the presenter literally like spins the globe and it retains the orientation of the globe as it completes the spin.

529
00:51:11 --> 00:51:13
And it was just mind blowing to see how fast it does.

530
00:51:14 --> 00:51:19
But Matt, if you have any specifications that you can share with the community here, that'll be great.

531
00:51:19 --> 00:51:32
Yeah, I mean, I mentioned this earlier, but we're super, I think for the first time, for me at least, like object tracking and this accessory tracking has gotten to a place where it's crossed the threshold, where it's just magic at this point.

532
00:51:32 --> 00:51:34
So I'm super excited to see how everybody uses this.

533
00:51:35 --> 00:51:47
In VisionOS 2.0, I believe it was that we were tracking, we were primarily focused on tracking stationary objects, mostly stationary objects, and therefore we were focused, the tracking was about like 5 hertz, 5 frames per second, if I recall correctly.

534
00:51:47 --> 00:51:56
But new in VisionOS 27, we have this high frame rate mode where you actually can go up to 30 frames per second at significantly lower latency.

535
00:51:56 --> 00:52:02
And I do have to also call out the engineers that worked on this and worked tirelessly for the past handful of months.

536
00:52:02 --> 00:52:08
There was also this amazing change just a few weeks ago where they were able to do some fantastic smoothing on the algorithm.

537
00:52:08 --> 00:52:10
And things truly feel attached in your hand.

538
00:52:11 --> 00:52:20
We knew we'd be able to achieve that when it comes to the spatial accessories where you have the onboard IMU and the constellation of IR, the infrared emitters.

539
00:52:20 --> 00:52:30
But to be able to just have a device that is fully disconnected with no electronics on board that you can go like this and the 3D asset is tracked directly to the physical model is pretty magical.

540
00:52:31 --> 00:52:32
So we're super excited.

541
00:52:32 --> 00:52:34
It's gotten to a really amazing place.

542
00:52:34 --> 00:52:39
And also for the first time in iOS 27, we're also expanding this capability.

543
00:52:39 --> 00:52:42
The exact same models that you train can now also work on our iOS devices.

544
00:52:43 --> 00:52:46
So we've created more places to experience this.

545
00:52:46 --> 00:52:50
We've created an extended training mode within CreateML that enables even higher accuracy and lower latency.

546
00:52:50 --> 00:52:52
So a lot of improvements on this front.

547
00:52:52 --> 00:53:03
I will also add to what Matt said is this year we also made changes so that the object tracking updates that you get is actually a frame time aligned timestamp.

548
00:53:03 --> 00:53:08
so that you will not try to guess, oh, is this the previous one?

549
00:53:08 --> 00:53:09
Is it the next one?

550
00:53:09 --> 00:53:13
Sometimes when they are not perfectly aligned, you could have a plus one, minus one frame delay.

551
00:53:14 --> 00:53:17
So now we are always frame display time aligned.

552
00:53:17 --> 00:53:24
And if you want to dig into the details of what exactly that means for your specific use case, our developer documentation have a lot of details on that.

553
00:53:26 --> 00:53:26
Great.

554
00:53:27 --> 00:53:27
Great.

555
00:53:28 --> 00:53:29
Next question here.

556
00:53:30 --> 00:53:36
The Vision Pro has awesome sensors and impressive frameworks like PersonaKit.

557
00:53:36 --> 00:53:39
and Core IK running under the hood.

558
00:53:40 --> 00:53:49
Will Apple expose more of these to developers, particularly for accessibility-focused telepresence apps for disabled users working in hybrid environments?

559
00:53:51 --> 00:53:52
I can take that one.

560
00:53:52 --> 00:53:54
Yeah, the standard Apple line.

561
00:53:54 --> 00:53:57
We don't speak to future plans, as you folks all know.

562
00:53:58 --> 00:54:00
But I would encourage you, again, to file a feedback.

563
00:54:01 --> 00:54:02
And we read these things.

564
00:54:02 --> 00:54:03
We take them seriously.

565
00:54:03 --> 00:54:04
Particularly for accessibility.

566
00:54:05 --> 00:54:05
Of course.

567
00:54:05 --> 00:54:09
You know, as a company, accessibility is a very central kind of principle.

568
00:54:09 --> 00:54:20
And we think, you know, Vision Pro has a lot to say there, a lot of capabilities that could be, you know, brought into being by developers or ourselves.

569
00:54:20 --> 00:54:28
So we're also very interested in understanding, you know, what that looks like in order to enable either a developer or a feature.

570
00:54:29 --> 00:54:30
So, yeah, definitely give that feedback.

571
00:54:31 --> 00:54:39
Yeah. Some of our accessibility even benefit a normal person like me, You know, with the magnifying glass feature, being able to give me the superpower to be able to read.

572
00:54:39 --> 00:54:42
I really appreciate our accessibility team.

573
00:54:42 --> 00:54:44
I have to call out favorite feature number four.

574
00:54:44 --> 00:54:44
Oh, no!

575
00:54:45 --> 00:54:46
Look at you, you're stacking them up.

576
00:54:48 --> 00:54:53
I'm allowed to have this one because it didn't come to mind because this feature was actually announced before yesterday.

577
00:54:53 --> 00:54:58
But I believe it's part of Vision R27, and there's some amazing videos and stories on this.

578
00:54:58 --> 00:55:09
But we announced just a few weeks ago as part of, I think, it was the Global Accessibility Day, exactly, some amazing features for wheelchairs being powered by Apple Vision Pro.

579
00:55:09 --> 00:55:19
So if you haven't seen that video, it's incredibly heartwarming, and it's amazing to see what a technology like Vision Pro can do to truly restore mobility and make people's lives a little bit easier.

580
00:55:20 --> 00:55:23
I think I missed an A. It's Global Accessibility Awareness Day.

581
00:55:23 --> 00:55:23
Thank you.

582
00:55:24 --> 00:55:25
Got to give it its proper.

583
00:55:27 --> 00:55:29
Question from Christopher at work.

584
00:55:31 --> 00:55:38
Mac virtual display seems tied to the Vision Pro's iCloud account, blocking use on shared or enterprise Macs.

585
00:55:38 --> 00:55:43
We want Vision Pros to drive multiple shared photogrammetry and Gaussian splatting Macs.

586
00:55:43 --> 00:55:45
Is there a supported way today?

587
00:55:45 --> 00:55:49
And is native shared device support planned?

588
00:55:49 --> 00:55:53
Thank you.

589
00:55:53 --> 00:55:55
Any takers on this one?

590
00:55:55 --> 00:55:58
I'll take the first part of the question because I understand that part the most.

591
00:55:58 --> 00:56:01
Maybe someone can take the photogrammetry and Gaussian splatting.

592
00:56:01 --> 00:56:06
But yes, today, your ability to use Mac virtual display is tied to the iCloud.

593
00:56:06 --> 00:56:08
So both devices do have to be on the same iCloud.

594
00:56:09 --> 00:56:19
There are other ways, I believe, though I do need to confirm, where you can actually do some sort of reverse airplay and screen mirroring from Mac to Vision Pro.

595
00:56:19 --> 00:56:23
But it's not as good as the Mac virtual display experience.

596
00:56:23 --> 00:56:26
So today those two things are quite linked.

597
00:56:26 --> 00:56:30
We'd be really interested in feedback in terms of where there are use cases.

598
00:56:30 --> 00:56:34
I know you're saying here in more enterprise and shared Macs.

599
00:56:35 --> 00:56:42
So I'd love to understand that a little bit more as we look at expanding the abilities to use Apple to Mac virtual display.

600
00:56:42 --> 00:56:46
I would just add that this sounds like an amazing app, right?

601
00:56:46 --> 00:56:56
Like being able to share a Gaussian splats model across a bunch of different devices, maybe even using SharePlay.

602
00:56:56 --> 00:56:59
That sounds like-- get on that.

603
00:56:59 --> 00:57:00
Yeah.

604
00:57:00 --> 00:57:05
The spatial preview functionality that we announced is not iCloud dependent.

605
00:57:05 --> 00:57:07
you can spatial preview to other devices.

606
00:57:07 --> 00:57:17
So if what you are referring to here is being able to share something that falls within a USD-compliant spec and is more of this spatial preview functionality, you can do that.

607
00:57:17 --> 00:57:20
We also announced something last year, which is the remote immersive space.

608
00:57:21 --> 00:57:35
So for users that want to be able to actually handle the rendering on the Mac and stream that over stereoscopically from the Mac to Vision Pro, you can host this remote immersive space is on the Mac side.

609
00:57:35 --> 00:57:40
And that, too, is not specific to requiring the same iCloud on both devices.

610
00:57:40 --> 00:57:41
Yeah.

611
00:57:41 --> 00:57:44
I'm just also echoing what Adarsh said earlier.

612
00:57:44 --> 00:57:50
It's just there are many ways nowadays to be able to bring your content to Apple Vision Pro.

613
00:57:50 --> 00:57:52
This includes 4-bit streaming as well.

614
00:57:52 --> 00:58:01
If you have Gaussian splat content with your creative pipeline that's running on a PC, you also have that ability in addition to the spatial preview framework to bring that in the Vision OS.

615
00:58:01 --> 00:58:16
And on the 4-bit streaming stuff, One thing I want to call out is that your Vision OS app can still host a bunch of capability that is native to Vision OS, like SharePlay or pairing up with SwiftUI for input and so on.

616
00:58:16 --> 00:58:26
So, forwarded streaming doesn't necessarily have, it's not just one-way communication between the PC instance and Vision Pro.

617
00:58:27 --> 00:58:28
You can actually send data back.

618
00:58:28 --> 00:58:34
Yeah, there was a developer I was talking to the other day, and they're building basically like a pilot simulation, right?

619
00:58:34 --> 00:58:52
for an airplane pilot and they're actually rendering the cockpit itself natively in Vision OS as a reality kit entity. And then everything out the window, they're streaming in from a cloud server. So it's this really awesome experience because there's certain things in like pilot training that maybe only require you to, if everything out the window is dark, that's okay,

620
00:58:52 --> 00:59:09
right? Because you just want to do your instrument controls, et cetera. But then you actually want to take off. And that's when the streaming starts. Now you can actually take off. And this blending of the content, I mean, three pieces of content being blended simultaneously, The real world, virtual elements being rendered directly on Apple Vision Pro on device,

621
00:59:09 --> 00:59:14
and then rendered content being streamed in from other devices, which is really like all working harmoniously together.

622
00:59:14 --> 00:59:15
It's pretty magical.

623
00:59:15 --> 00:59:17
And that's all sorting properly within the 3D space.

624
00:59:17 --> 00:59:18
Exactly, yeah.

625
00:59:18 --> 00:59:21
It's not like one is always at a higher depth level than the others.

626
00:59:21 --> 00:59:23
They can all have a shared across the entire depth spectrum.

627
00:59:24 --> 00:59:24
Yeah.

628
00:59:24 --> 00:59:25
Cool.

629
00:59:26 --> 00:59:27
Next question here.

630
00:59:27 --> 00:59:36
Can Gaussian splats be part of a USDZ file, perhaps with other meshes, and used in a website environment.

631
00:59:36 --> 00:59:36
Travis.

632
00:59:36 --> 00:59:39
So it's a little complicated.

633
00:59:39 --> 00:59:55
So as an OpenUSD feature, support for what they call particle fields, which is a generic kind of form of Gaussian splat, is actually available within OpenUSD.

634
00:59:55 --> 01:00:00
Now, OpenUSD is the standard and open source.

635
01:00:00 --> 01:00:11
And then separately, there's what we've announced in Reality Kit, which is its ability to have a splat inside Reality Kit.

636
01:00:11 --> 01:00:18
Those planes didn't land at the same time, so they're not currently hooked up together.

637
01:00:18 --> 01:00:23
But that is something that Norman and I were just talking about moments before we started this show.

638
01:00:23 --> 01:00:27
So obviously, that's something that in the future we're looking to close that gap.

639
01:00:28 --> 01:00:34
And so that's just kind of an artifact of standards development and process and then OS development process.

640
01:00:34 --> 01:00:37
Sometimes the trains – I'm using transportation analogies all the time.

641
01:00:37 --> 01:00:39
The trains don't run on the same time.

642
01:00:39 --> 01:00:47
I think this year we added the fundamental capabilities of understanding what a SPLAT is within re-advocate technology glossary.

643
01:00:48 --> 01:00:57
But now I think the next step is when we look at the harmony of expanding the Gaussian SPLAT ecosystem, we are going to think about the end-to-end story.

644
01:00:57 --> 01:01:05
that include what is the standardization, like what Travis said in AOUSD, Apple is playing a really big part in driving that standard.

645
01:01:05 --> 01:01:07
We drove actually particle fields.

646
01:01:07 --> 01:01:07
That's right.

647
01:01:07 --> 01:01:14
It was Apple and a couple other companies, I believe Adobe and others, were pretty instrumental in driving that through.

648
01:01:15 --> 01:01:19
It's just standards process runs at one clock rate and then Apple runs at another clock rate.

649
01:01:19 --> 01:01:27
Yeah, so we want to definitely look at the alignment between the specification to the runtime capability and to the authoring story as well, right?

650
01:01:27 --> 01:01:38
Because there has to be a way for you to capture the object you wanted to capture in SPLAT format and be able to store it and be able to have the runtime KB API to display it.

651
01:01:39 --> 01:01:39
Yeah.

652
01:01:39 --> 01:01:44
And, you know, USD is able to express multiple different things in a scene.

653
01:01:44 --> 01:01:47
And so to their question, they're asking, you know, can I mix and match?

654
01:01:48 --> 01:01:50
I mean, yes, you can contain them in the same scene.

655
01:01:50 --> 01:01:50
That's right.

656
01:01:51 --> 01:01:51
Excellent.

657
01:01:51 --> 01:01:52
Talking about landing planes.

658
01:01:52 --> 01:01:53
From a pure UI.

659
01:01:53 --> 01:01:54
Oh, pretty much.

660
01:01:54 --> 01:01:56
We're out of time here.

661
01:01:57 --> 01:02:04
But I wanted to say that, like, on that particular question, it seems like a very interesting use case, and we would love to hear more.

662
01:02:04 --> 01:02:06
So please do share that feedback.

663
01:02:07 --> 01:02:10
But we are out of time right now to go into deeper discussion there.

664
01:02:10 --> 01:02:18
Thank you so much for all the questions that, amazing questions, I should say, that all of you brought to us today.

665
01:02:18 --> 01:02:32
I really appreciate the enthusiasm and the passion for the VisionOS development, and the community brings in so much excitement and is helping us make this platform better year after year.

666
01:02:32 --> 01:02:33
Thank you so much.

667
01:02:33 --> 01:02:36
If we couldn't get to your question, please don't be disheartened.

668
01:02:37 --> 01:02:44
We have an active team that's looking at forums this entire week, and it also keeps an eye out through the rest of the year as well.

669
01:02:45 --> 01:02:48
But please do post your questions on forums.

670
01:02:48 --> 01:02:51
And I'm sure you'll get answers to your questions.

671
01:02:52 --> 01:02:58
If you have feedback, I think that was something that was mentioned many times during our conversation.

672
01:02:58 --> 01:03:00
Please do share that with us as well.

673
01:03:00 --> 01:03:03
It's your feedback that makes this platform awesome.

674
01:03:03 --> 01:03:05
Thank you so much for tuning in.

675
01:03:05 --> 01:03:11
And thanks to the panelists, as well as the team that has been helping us answer your questions behind the scenes.

676
01:03:11 --> 01:03:13
We really enjoyed this session.

677
01:03:13 --> 01:03:17
And we would look forward to what you build on VisionOS.

678
01:03:17 --> 01:03:18
Enjoy WWDC.

679
01:03:18 --> 01:03:19
Thank you.

680
01:03:19 --> 01:03:19
Thanks.
