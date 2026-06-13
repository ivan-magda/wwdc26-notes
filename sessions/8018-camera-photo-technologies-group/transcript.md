---
title: Camera and Photo Technologies Group Lab
source: https://developer.apple.com/videos/play/wwdc2026/8018/
session: 8018
collection: wwdc2026
duration: 00:59:31
fetched: 2026-06-12
via: whisper.cpp ggml-large-v3 (session SD video audio)
transcription: machine-generated, no speaker diarization
---

> Machine transcription of the **Camera and Photo Technologies Group Lab** SD video via whisper.cpp (`ggml-large-v3`).
> Group labs ship no Apple transcript; this is generated from the audio. Cues are timestamped segments, **not** speaker-attributed turns.

## Transcript

1
00:00:03 --> 00:00:05
Welcome to the Camera and Photos Group Lab.

2
00:00:05 --> 00:00:06
My name is Sergey.

3
00:00:06 --> 00:00:09
I'm part of the Developer Relations team here at Apple.

4
00:00:09 --> 00:00:14
Today, I'm joined by a panel of experts from Camera and Photos Engineering team.

5
00:00:15 --> 00:00:16
I'll let them introduce themselves.

6
00:00:16 --> 00:00:17
Matt, let's start with you.

7
00:00:18 --> 00:00:21
Hi everyone, I'm Matt Decoff and I work on the Photos Frameworks team.

8
00:00:21 --> 00:00:23
I'm Brad Ford.

9
00:00:23 --> 00:00:30
I work in Camera Software and have had the good fortune of working on every single iPhone in my 25 years at Apple.

10
00:00:32 --> 00:00:34
Hi, my name is Ivan Cabero-Belabonde.

11
00:00:34 --> 00:00:42
I work in camera software as well as Brad, and I specialize on the camera, the still capture pipeline.

12
00:00:43 --> 00:00:45
My name is Davide Conchon.

13
00:00:45 --> 00:00:50
I work in a team where we do file format compression and RAW.

14
00:00:51 --> 00:00:56
And I had, you know, I'm lucky enough that I've been working at Apple for 19 years now.

15
00:00:57 --> 00:00:58
I still love it.

16
00:00:58 --> 00:01:00
There is every day something new to learn.

17
00:01:01 --> 00:01:02
Amazing.

18
00:01:03 --> 00:01:04
I'm Jake.

19
00:01:04 --> 00:01:08
I work on camera performance and I have not been here for 19 years, just five years.

20
00:01:09 --> 00:01:10
Not too bad.

21
00:01:11 --> 00:01:16
So in addition to those on screen, there is a team behind the scenes helping us triage the questions.

22
00:01:17 --> 00:01:21
We are so excited to talk about building exceptional experience using camera and photos frameworks.

23
00:01:22 --> 00:01:26
And today we want to focus on questions that benefit everyone watching.

24
00:01:27 --> 00:01:35
So for code specific questions or if you somehow didn't get to answer your questions today, go to the developer forums to continue the conversation.

25
00:01:36 --> 00:01:41
Also, you can use Feedback Assistant to file bugs or submit feedback requests.

26
00:01:41 --> 00:01:45
And to get started, I would like to ask a question to all our panelists.

27
00:01:45 --> 00:01:51
So today, camera and photos apps on the iPhone are packed with features.

28
00:01:51 --> 00:01:53
There's so many of them.

29
00:01:53 --> 00:01:58
And I'm just curious, what is your favorite feature on camera and photos app?

30
00:01:58 --> 00:01:59
Maybe Matt, maybe you can start.

31
00:01:59 --> 00:02:01
Sure, yeah, there are a lot.

32
00:02:01 --> 00:02:25
I honestly the one I love the most is probably live photos I think the you know especially with kids and nieces and nephews that live you know far away getting it's this sort of like slice of time photo plus video and sound um it really like brings those moments to life so live photos is probably my favorite yeah it's great right what about you yeah live photos is kind of the og

33
00:02:25 --> 00:02:32
isn't it it's uh an easter egg my mom didn't know for years that she was taking live photos she says How do I turn on live photos?

34
00:02:32 --> 00:02:36
And I said, you already have live photos in your photo roll, Mom.

35
00:02:37 --> 00:02:41
I, like a lot of us, spend a lot of time on my Mac in meetings.

36
00:02:41 --> 00:02:48
And so I love the video effects, the system-wide video effects like Portrait, Studio Light, Center Stage.

37
00:02:49 --> 00:02:52
The fact that you get them for free in any app as a user is very powerful.

38
00:02:53 --> 00:03:01
Recently, we've added background replacement, gestures, whimsical, delightful, and, you know, add value to conferences.

39
00:03:01 --> 00:03:08
so I really love those, and all the new features on the iPhone 17 front-facing camera, which I hope we get to answer some questions about.

40
00:03:08 --> 00:03:09
Yeah, for sure. Yeah, thank you, Brett.

41
00:03:10 --> 00:03:11
Ivan, what about you?

42
00:03:12 --> 00:03:32
The one that's near and dear to my heart is we refer to it as opportunistic depth capture or portrait in photo, and this is the feature that I introduced a few years back where as long as there's a person in the scene, you'll see a little F in the camera app, and when you take a photo, we will get depth for you, and you can go and add the portrait effect afterwards

43
00:03:32 --> 00:03:36
if you so desire with all the controls that you want.

44
00:03:36 --> 00:03:49
That release, we did a ton of work to actually make the depth processing a lot more efficient and available at all kinds of zoom levels and be able to take advantage of deferred processing.

45
00:03:49 --> 00:03:58
So these are all things that are available for developers to adopt in their camera applications as well.

46
00:03:58 --> 00:04:28
that's great over there yeah for me it has to be pro raw for two reasons number one i've i've been working on that feature for so long you cannot even imagine and the second reason is that is a vehicle for the rest of us to access features from the camera that usually are just for you know the pros the people that know how to edit with the pro raw format which is available on camera and even

47
00:04:28 --> 00:04:56
for third party. You can actually collect the full spectrum of what the iPhone can give us in terms of image quality. And then on the other side, if you like to do so, you have the latitude to embellish these images or modify them the way you like. Because even though images coming out from the iPhone are definitely amazing, every one of us has a little bit of a different taste.

48
00:04:56 --> 00:04:57
- Sure.

49
00:04:57 --> 00:05:01
- So ProRAW is actually allowing you to do so.

50
00:05:01 --> 00:05:02
- Yeah, makes sense.

51
00:05:02 --> 00:05:03
Thank you for sharing.

52
00:05:03 --> 00:05:04
Jake, what about you?

53
00:05:04 --> 00:05:08
- Yeah, I think I've been having a lot of fun with the new Tele on the iPhone 17 Pro.

54
00:05:08 --> 00:05:10
Like the zoom on it is insane.

55
00:05:10 --> 00:05:12
Like it'd be like 40X.

56
00:05:12 --> 00:05:18
We were at Yosemite like a few months ago, hiked up to the top of Glacier Point and like I was zooming in all the way through the valley.

57
00:05:18 --> 00:05:19
It was like incredible.

58
00:05:19 --> 00:05:21
- Were you able to see-- - Yeah, you can actually see people.

59
00:05:21 --> 00:05:24
Yeah, they kind of look like ants from that far away, but it was pretty cool.

60
00:05:24 --> 00:05:25
- I should try that.

61
00:05:25 --> 00:05:26
- Yeah, yeah. - That's amazing.

62
00:05:26 --> 00:05:51
thank you thank you for sharing your personal stories everyone um and with that i would just i want to jump straight into the questions because we have so many of them and uh you know i want to give as much time to answer them so the first question is from user uh with the name florent in inf um and the question is when reframe our clean up is used to modify a photo does ios 27

63
00:05:51 --> 00:05:58
and tag it with any metadata or content credentials so someone can tell the image was edited by AI?

64
00:05:59 --> 00:06:00
That would like to take this one.

65
00:06:03 --> 00:06:07
It's just because we do work with metadata every day.

66
00:06:08 --> 00:06:09
So yes, the answer is yes.

67
00:06:10 --> 00:06:17
There is the metadata in the file is being modified with IPTC metadata together with EXIF.

68
00:06:18 --> 00:06:30
And the IPTC gets updated even based on which of the AI modification that you're doing, spatial reframe or even cleanup.

69
00:06:30 --> 00:06:35
So yes, there is a way to understand from the metadata what has been done today.

70
00:06:35 --> 00:06:36
That's great.

71
00:06:36 --> 00:06:53
Just to add on a little bit of that, in the Photos app, when you do one of these edits, in the info panel-- so on iOS, when you swipe up at the bottom of info panel, we display this information about which edit was used.

72
00:06:53 --> 00:06:55
- Great, thank you for sharing.

73
00:06:55 --> 00:07:00
All right, so the second question is about keywords in photos.

74
00:07:00 --> 00:07:05
So the question is, keywords were mentioned as a feature common to photos.

75
00:07:05 --> 00:07:10
Will there be an API for third-party apps to use or edit keywords?

76
00:07:10 --> 00:07:12
- Yep, I can take this one as well.

77
00:07:12 --> 00:07:23
Yes, so the feature, I think Ben's referring to there, is on iOS now, it's a feature that's existed on Mac for a long time in the Photos app.

78
00:07:23 --> 00:07:28
You can see and edit keywords, again, in the info panel that sort of UI I was just mentioning.

79
00:07:28 --> 00:07:33
There's also UI for sort of managing these keywords and searching by them.

80
00:07:33 --> 00:07:47
These will be reflected in IPTC metadata when you export, but there's currently no sort of PhotoKit level API for fetching or querying based on these keywords.

81
00:07:47 --> 00:07:49
- Gotcha, okay, thank you.

82
00:07:51 --> 00:07:56
So the next question is from a user with a name sjk_27.

83
00:07:59 --> 00:08:08
When showing thumbnails of images in a lazy grid view that also have a matched geometry effect to a full image detail view when tapped.

84
00:08:08 --> 00:08:13
So what's the optimal way to load the thumbnails images for performance?

85
00:08:13 --> 00:08:41
For example, respective smaller size stored with OG use some ci filter to scale them down on init i don't know who is this i can i can take this one so on the core graphics side there is a there is a property when opening images called open open image with thumbnails internally core graphics will understand if the image has

86
00:08:41 --> 00:09:17
already a thumbnail to be to be utilized or it will try to decode and scale down as fast as possible the the main image on the on the core image side we have ways to to request a scale factor when we open images and depending on your ui you can ask the the scale factor to be as small as you wish and this internally instructs Core Image to actually scale down the image

87
00:09:17 --> 00:09:23
as soon as possible during the process so that everything that happens after then is going to happen as fast as possible.

88
00:09:23 --> 00:09:26
Okay, that's great.

89
00:09:26 --> 00:09:27
Thank you.

90
00:09:27 --> 00:09:35
We have one more question from Ben on the first start and maybe, Jake, you can take that.

91
00:09:35 --> 00:09:36
Yeah, absolutely.

92
00:09:36 --> 00:09:56
So the question is, "Can the first start cause issues where the user may attempt to capture photo before the capture photo output has attached to the session yeah it's it's a good question um so actually talk about this exact point in my dub dub session this year called a responsive camera app that launches quickly um so yeah with deferred start basically what you're doing is just pushing

93
00:09:56 --> 00:10:18
out initialization so if you only do that uh to like the asker's point is yeah you may actually still miss the shot um so the photo capture output actually has an is responsive capture enabled property you set that to true while also deferring it well actually the system will add some buffering so you can launch quickly get the capture even if we haven't fully initialized the

94
00:10:18 --> 00:10:40
output so you don't end up missing that that moment we still queue up that shot this is great and uh by the way that session is awesome um you know if you haven't watched it please go ahead and you know find it on a developer app it's called um let me find the name uh built a responsive camera apps it launches quickly and we have a cool dominoes effect yeah you should watch me play with

95
00:10:40 --> 00:11:00
dominoes and just for background for anyone who hasn't watched it isn't sure what we're talking about that deferred start api was introduced in ios 26. it's a way of telling uh your av capture session outputs which of them should start up deferred and which are most important to start right away so usually you could say i want the preview to start first everything else can be

96
00:11:00 --> 00:11:29
be deferred yeah exactly yeah all about that fast launch experience perfect all right so the next question from uh yelion i hope i pronounce the name correct uh what is the best way to get the depth map and the image for a live viewfinder and for the taken image to create a nice 3d effect with balls okay i can take that go ahead uh so uh thanks for the question yelion so um there's

97
00:11:29 --> 00:11:55
two parts to this part is the preview side and part is the still capture side for the preview side uh you want to use uh depth data output enabled on on your preview stream uh and you want to use the avi capture synchronizer so that so so that it's it's synchronized with the rgb stream on the still capture side uh you you want to uh what you want to do is uh enable uh depth

98
00:11:55 --> 00:12:23
the enable uh depth on your on the the av capture photo output and that will on that that will provide ensure that you get depth with the still capture so that you can use uh so then uh so then you can use uh filters like in core image uh to uh to add the add the blur based on the depth that's captured into the still yeah there are levels to it if all you're uh interested in is having a

99
00:12:23 --> 00:12:46
live depth effect in your preview, there's a shortcut. You could just turn on the cinematic video capture API, which we introduced last year, which lets you replicate exactly what we do in the cinematic mode in the camera app. So if you just use a video preview layer and you set cinematic video capture enabled on your device input, that will give it to you for free. So if

100
00:12:46 --> 00:13:08
you just want something cheap and easy, you got that. What Yvonne talked about is absolutely right if you want access to the actual depth samples um and you want to kind of do it yourself then video data output plus depth data output and then use a data output synchronizer to make sure that you get the depth and the video for the same time stamp in the same callback and then you can

101
00:13:08 --> 00:13:31
render them together using a core image filter makes sense and the specific thanks brad and specifically about the specific api for the still capture side that you want to be looking for is In the AV capture photo settings, for the AV capture photo output, you want to go ahead and set depth data delivery enabled to true.

102
00:13:32 --> 00:13:34
Okay, that's good to know. Thank you.

103
00:13:35 --> 00:13:38
So much useful info, thanks.

104
00:13:40 --> 00:13:45
Next question is on photo quality prioritization from Eric.

105
00:13:45 --> 00:13:59
So the question is, when photo quality prioritization is equal to quality, the AV capture device often overrides the manual exposure duration and ISO settings that it has been set when capturing a photo.

106
00:13:59 --> 00:14:04
Is there a way ahead of time to find out what the final exposure setting will be before the photo is captured?

107
00:14:06 --> 00:14:06
You want to take it?

108
00:14:07 --> 00:14:07
Sure.

109
00:14:07 --> 00:14:13
So from an API standpoint, actually it's not just quality, but also balanced.

110
00:14:16 --> 00:14:31
We choose, because we're doing processing algorithms, most of which include fusion, which need to do things like capture different exposures, like an underexposed image, as well as a normally exposed image.

111
00:14:32 --> 00:14:37
We do not give control to any of the manual controls.

112
00:14:38 --> 00:14:42
The manual controls, in fact, are only supported when you capture with speed.

113
00:14:43 --> 00:14:51
If it looks like balance or quality are respecting your settings, that's because, you know, that you're getting lucky.

114
00:14:51 --> 00:15:02
But in general, there's no guarantee that the capture stack will make that determination depending on the scene, the overall brightness, and so on to figure out how to expose.

115
00:15:03 --> 00:15:13
Whenever you choose balance or quality, you're essentially handing off the decision-making about how to expose this to us.

116
00:15:13 --> 00:15:36
And and if you really need that level of control, you should use manual. Yeah, we realize it's a little opaque That you signal to the framework. I am willing to take this quality prioritization So I'm I'm willing to wait or I want to balance or I just want it as fast as possible But we we use that as a hint So depending on which format you're using if it's the photo format

117
00:15:36 --> 00:15:57
We are going to use our best algorithms our best fusion algorithms and we're gonna ignore your manual settings For some of the video formats where we choose not to use those fusion formats because we don't want to disrupt movies and preview, you may still get a speed capture, and so you might still have your manual settings preserved.

118
00:15:58 --> 00:16:04
So the rule is if you're using photo mode, quality and balanced are always going to override your manual settings.

119
00:16:04 --> 00:16:12
If you really care about having exactly your shutter speed, ISO, and things preserved, then you should definitely use speed for your photo quality.

120
00:16:13 --> 00:16:43
photo prioritization it's great and i believe this was pretty well covered in the implement high resolution photo capture session this year so yeah great session yeah maybe check it out and you know find more answers are great the next question is on ph asset framework api yep so Can you elaborate on PHAsset's original resource choice and what it's used for?

121
00:16:43 --> 00:16:45
There is not much documentation yet.

122
00:16:46 --> 00:16:47
Yes.

123
00:16:47 --> 00:16:50
So, yeah, this is a new API for an existing feature.

124
00:16:50 --> 00:16:56
So for a long time in the Photos app, this is related to RAW plus JPEG.

125
00:16:56 --> 00:17:06
So, you know, it's very common for DSLR cameras to have a setting where you shoot, you know, RAW plus JPEG or these days HEK compressed in a RAW image.

126
00:17:06 --> 00:17:12
And then when they're imported to the Photos app, you'll see badges that say raw R plus J.

127
00:17:12 --> 00:17:21
And when you go into edit in the Photos app, there's a way to choose, am I doing my edits on top of the raw image or am I doing my edits on top of the JPEG?

128
00:17:21 --> 00:17:22
And you just can kind of swap between them.

129
00:17:23 --> 00:17:25
So that's what this original resource choice is.

130
00:17:25 --> 00:17:32
Is it saying, is the compressed image the original or is the raw image the original?

131
00:17:32 --> 00:17:35
And what am I using to do edits on top of?

132
00:17:35 --> 00:17:40
And also, what are we using to make smaller image derivatives and thumbnails and such?

133
00:17:41 --> 00:17:43
Which resource are we sourcing those from?

134
00:17:43 --> 00:17:50
So, yeah, there's a handful of APIs there around the pH content editing input source, choosing which one.

135
00:17:50 --> 00:17:56
There's a change request on assets for toggling which resource is the original.

136
00:17:58 --> 00:17:59
Yeah, I believe those are the main ones.

137
00:17:59 --> 00:18:01
Thank you.

138
00:18:02 --> 00:18:09
Next question is on Bayer Row Capture Preview.

139
00:18:09 --> 00:18:29
So will iOS 27 support the linear scene referred previous stream for Bayer Row Capture via AV Capture Video Data Output or AV Capture Video Preview Layer without tone mapping or computational processing so it can match a linear CI Row Filter DNG conversation?

140
00:18:29 --> 00:18:33
And they actually linked a related feedback assistant, which is great.

141
00:18:33 --> 00:18:35
Thank you for submitting that feedback assistant.

142
00:18:36 --> 00:18:37
I want to take this question.

143
00:18:38 --> 00:18:39
I can take this.

144
00:18:39 --> 00:18:53
So today, the only way to actually get scene-referred linear data in camera capture is actually through the log format.

145
00:18:54 --> 00:18:55
We have two flavors of it.

146
00:18:56 --> 00:19:04
We've got log and log2 introduced last year, which is an improved gamut of the log.

147
00:19:05 --> 00:19:24
Now, in terms of, you know, if I drill in deeper into the question, I guess I'm assuming the developer would like to actually have raw data coming out from camera capture and being able to use the same filters that we use, say, for images.

148
00:19:24 --> 00:20:00
now that is not available today raw frames coming out for progress raw for instance for capturing progress raw from camera capture do come with with certain metadata and that metadata is not compatible with the metadata that CI raw filter would need for for rendering that image but I kind of like the idea and thank you for the the feedback request I believe is something that is

149
00:20:00 --> 00:20:29
worth worth exploring definitely for thank you for the future yeah it's great thank you I really love the next question it's a I think it's more holistic more high-level maybe you know everyone can take a turn and give their own suggestion so the question is for an app that lives and dies by its capture experience what are your must-have recommendations um i know jake well i mean i'm going to be biased

150
00:20:29 --> 00:20:46
here but i'm going to say you want to have that most performant app as possible um you know i think like when you have a capture app that you know people are using it to capture life's moments um and you know if you miss the shot sometimes you can't get that moment back for sure so keeping things you know fluid you launch quickly you get that shot responsive capture uh that i think uh

151
00:20:46 --> 00:21:05
we've had you know session in 2023 about uh and then mohan in the most recent session uh you know talked about that and deferred processing, like, I think, to me, those are your top three things, going to have a fast launch, responsive capture, and use deferred processing, can't go wrong with that. Sure, yeah, thank you, have a day. I think, make it pro.

152
00:21:07 --> 00:21:38
Add any capability that, you know, are available for, you know, for reaching the, you know, maybe the part of the world that would like to actually, you know, interact with those assets, uh you know with more personal uh flair um so uh keying off of that uh i'm gonna say maybe not make it pro uh i'm gonna say like it it's like the lives or dies by capture experience is one of

153
00:21:38 --> 00:22:00
these things that is you know it's it's it's it's vague enough that i we're not really sure what you mean like an optimal capture experience for uh something that's intended to capture video for social network is very very different from from a from a from a pro photography application um there you can have a photography application that's actually like tries to lean into the super

154
00:22:00 --> 00:22:28
fun like remember hipstamatic when i first launched and and and so that so so it really depends what you mean by that uh by by the the by the capture experience that said i will echo the performance stuff people they're like users really don't like to wait the performance is critical reliability is critical uh um there's lots of things that that that that you can do to

155
00:22:28 --> 00:22:55
to make it more reliable and and and and more stable uh we have tons of tooling for you to analyze your performance and and and track the kind of issues that that that you should be addressing uh but uh but uh but 100 like what i the takeaway i would say focus on the user experience based on who you think your users are going to be who is this for thank you man uh we

156
00:22:55 --> 00:23:19
realize that av foundation is not an easy framework to get into it's huge i think we're the second largest framework in ios after ui kit so it can be daunting you know you look at all of these properties, all of these classes, and you're like, where do I start? My recommendation is to not start from scratch. Use the sample code because there are best practices that you might miss out

157
00:23:19 --> 00:23:39
on if you just, you know, peruse the documentation and then start writing. A common problem we see is that people use their AV capture session on the main thread, not realizing that it blocks to do certain lengthy operations. And we, you know, you would know that if you looked at our sample code that you need to have a dedicated serial queue

158
00:23:39 --> 00:23:40
in talking with AV Capture Sessions.

159
00:23:41 --> 00:23:44
So right there, that's going to go to the performance, to the responsiveness.

160
00:23:45 --> 00:23:50
Don't lock up your UI thread doing stuff that's meant to be done in a background thread.

161
00:23:51 --> 00:23:57
Also, whether it's pro or not pro, figure out what differentiates your app from others.

162
00:23:57 --> 00:23:59
I mean, I think camera apps are a dime a dozen.

163
00:24:00 --> 00:24:14
We give you so many tools, everything from pro use cases like GenLock and locked frame duration, which we just gave you last year, to very simple record a movie file, it's all there for you.

164
00:24:15 --> 00:24:19
But what are you going to do in your app that differentiates it, that draws people to it?

165
00:24:19 --> 00:24:21
Is it a social aspect?

166
00:24:21 --> 00:24:28
Is it a key feature, a key value add that you've got that we are providing the toolbox, but we don't provide everything for you?

167
00:24:29 --> 00:24:30
Makes sense. Thank you, Brett.

168
00:24:31 --> 00:24:35
I'd be remiss if I didn't talk about the photos framework side of it.

169
00:24:35 --> 00:24:41
You know, I think it's hard to talk about, you know, a camera app is great for taking photos and then those photos need to go somewhere.

170
00:24:41 --> 00:24:54
And so, you know, the integration with the photo kit, both Photos UI and Photos Framework, it starts with the user permissions, you know, like by default, your app does not have any access to save or read back photos from the photo library.

171
00:24:54 --> 00:24:57
And you can sort of gradually increase that.

172
00:24:57 --> 00:24:59
You can request to just save assets.

173
00:24:59 --> 00:25:03
That's a very simple request that most users, you know, are fine with.

174
00:25:03 --> 00:25:07
And then if you need to read stuff back, you can upgrade your permissions through prompts.

175
00:25:07 --> 00:25:17
And there's sort of a very kind of tight coupling and, you know, performance considerations there around capture to review, whether that's in your app or someone's jumping to the Photos app.

176
00:25:18 --> 00:25:20
Yeah, that's a great advice.

177
00:25:20 --> 00:25:23
I think everyone likes the, you know, pro not pro discussion.

178
00:25:24 --> 00:25:25
You guys can arm wrestle.

179
00:25:25 --> 00:25:26
Give the battle a go, yeah.

180
00:25:27 --> 00:25:29
Oh, I love the Pro apps too.

181
00:25:29 --> 00:25:32
Like I have some near dear to my heart.

182
00:25:32 --> 00:25:38
I thought it'd be interesting to offer some contrast and look at the more fun apps as well.

183
00:25:39 --> 00:25:40
It's super helpful.

184
00:25:40 --> 00:25:41
100%.

185
00:25:41 --> 00:25:42
All right.

186
00:25:42 --> 00:25:53
The next question is, what is the optimal and recommended way for streaming video and audio simultaneously?

187
00:25:54 --> 00:25:59
My concern is that there will be a delay in transmission and the video and audio will not sync.

188
00:25:59 --> 00:26:06
Specifically, what is the most optimal way for streaming the audio, and is it safe to stream frame by frame of the video?

189
00:26:07 --> 00:26:08
I can take this one.

190
00:26:08 --> 00:26:08
Sure.

191
00:26:09 --> 00:26:12
Audio and video synchronization is no joke.

192
00:26:12 --> 00:26:13
You should pay attention to it.

193
00:26:14 --> 00:26:19
Don't have incidental or coincidental sync.

194
00:26:20 --> 00:26:25
Our video and audio on modern iPhones is synced from the same clock.

195
00:26:25 --> 00:26:39
So if you don't handle your AV sync, you might think you're okay and ship an app that is usually in sync, except when you run it on an iPad with an external camera and then the audio and video suddenly become out of sync.

196
00:26:40 --> 00:26:46
The first recommendation I would have is use AV Capture Session for both your audio and your video.

197
00:26:47 --> 00:27:05
In other words, attach a device input for a camera that you're interested in and a device input for the mic that you're interested in, and then if you get audio data output and video data output, the AV Capture Session will already do the hard work of synchronizing those two sources so that what comes out of those outputs, the PTSs are already

198
00:27:05 --> 00:27:30
on the same timeline. So whatever you want to do with them, stream them, write them to a movie file, whatever, they're already in sync. If for some reason you need to use a different audio API such as AU Remote IO, then you're going to need to do a little bit more work. Then you're going to need to delve into clocks like CM Clock. Every device on our system is backed by a time source

199
00:27:30 --> 00:27:57
or a cm clock so the video will be on one clock the audio will be on a different clock and it's important that once you get samples from those two sources you synchronize them using these clocks and there is a low level api in core media called cm synchronous cm clock convert time cm synchronization convert time something like that which lets you say from this clock to this clock

200
00:27:57 --> 00:28:17
give me the pts and it you give it the pts in the source uh time frame and it'll give you the output pts so what you're going to wind up doing usually is keeping your audio time because you don't want to have to rate convert the audio but you will synchronize your video to your audio clock get a different time stamp for that video buffer that came out and now they're both on the

201
00:28:17 --> 00:28:42
audio timeline so um as far as uh meta concerns about uh things coming out uh if you're if you're you're streaming them over a network, well, then you just have to rely on the timestamps that you had them in sync before you sent them. So when you get them on the receiving side, the audio and video have a coherent timeline, then you're going to need to take care of the playback synchronization

202
00:28:42 --> 00:29:06
on the playback side. And we have plenty of APIs in the rest of AV Foundation on the playback side, such as with AV Player, AV Sample Buffer, Display Layer, that can take care of the synchronization for the playback portion sounds straightforward yeah it's totally easy oh is audio ramping easier to perceive than video ramping or something uh i think uh the science tells us that

203
00:29:06 --> 00:29:27
people perceive changes in audio sample rate more readily than they do micro adjustments in video time so generally that's the better thing to do is adjust the video to the audio than vice versa yeah unless you're going to sample rate convert the audio audio is much more important for experience you know i mean it's a tiny glitch in audio and people will hear it yeah it's super

204
00:29:27 --> 00:30:03
sensitive one thing uh what does pts stand for oh presentation timestamp thank you thank you all right we got uh some really uh you know pro focused questions uh afforded so david your turn so next question is from a user with a user name is from you number 16 and the question is why does ProRaw support 48 megapixel output from quad buyer sensor whereas native Bayeraw

205
00:30:03 --> 00:30:37
output is limited to the binned resolution yes so to answer this question maybe we we need to differentiate what is a Bayer raw from a pro raw so Bayer raw contains Bayer data in in the file while pro raw has gone through a debayering step and photon in photonic engine merger of multiple images to get that output so at that

206
00:30:37 --> 00:31:16
point a ProRAW does not care anymore what is the format of the sensor. It may be Bayer, it may be Quadra, it doesn't really matter because the data that comes out is in RGB already linearized. Now to go to this question why ProRAW can do it and Bayer can't, it's because ProRAW is linear so we are able to do that. Now, the ability to do Bayer for QuadraSensor is not yet available. And if the developer

207
00:31:16 --> 00:31:34
would like to have actually this capability, I would, you know, highly suggest to send a request because, you know, it's something that, you know, if many developers were to be wanting, it could be something that we can look at as well.

208
00:31:34 --> 00:31:36
Yeah, so please, please submit that feedback assistant.

209
00:31:37 --> 00:31:38
We actually read them.

210
00:31:38 --> 00:31:40
Yeah, and if I can add a little bit to that.

211
00:31:40 --> 00:31:47
The other aspect is that, so, like, this raw output wouldn't be Bayer, right?

212
00:31:47 --> 00:31:48
It'd be quad Bayer.

213
00:31:48 --> 00:31:49
That's the thing.

214
00:31:49 --> 00:31:55
The Bayer output, the raw Bayer output from a quad Bayer sensor is the bin sensor.

215
00:31:55 --> 00:32:00
We bin the quad pixels into Bayer.

216
00:32:01 --> 00:32:05
And the other aspect of this is that this is an ecosystem question, right?

217
00:32:06 --> 00:32:13
We can't give you quad-bayer DNGs, raw output, without giving you a way to decode them, right?

218
00:32:13 --> 00:32:18
So it has to come with support in CI raw filter, et cetera, et cetera.

219
00:32:19 --> 00:32:22
So it's a heavier lift than what it sounds.

220
00:32:22 --> 00:32:36
Yes. And maybe to add even to that, debayering Bayer data, Bayer sensor is a technology and skills that have been developed for now 25 years.

221
00:32:37 --> 00:32:45
Debayering Quadra is a completely different beast. It's way more complicated than one may think.

222
00:32:46 --> 00:32:48
Interesting. Thank you for sharing. All right.

223
00:32:48 --> 00:33:22
we have one more question for the pro camp um what is your recommended process to generate and write iso conform gain maps and gain maps uh map metadata for hic and jpeg on ios yes wonderful question without going into the details of the api i'm going to send the developer directly to a wwdc talk that we had two years ago where exactly this question is being uh uh you know drilled

224
00:33:22 --> 00:33:54
into we have uh we have two uh major frameworks that can uh handle uh a game map and iso iso game map data one is core graphics and the other one is core image for both these cases david One colleague of us is giving exactly what are the APIs, how to do, how input need to be prepared to then create outputs that are game map compatible.

225
00:33:54 --> 00:34:01
And there is a third way that is possible.

226
00:34:01 --> 00:34:08
The specification for game map have been added to both the HIF spec and JPEG.

227
00:34:08 --> 00:34:39
we have been working apple has been working to add those those pack and they're very very clear so that's another way to even monitor understanding what this metadata does why this image is divided into uh in terms of what user what is your sdr or rgb content and what is the hdr addition to it so that's a third way to you know if the developer likes uh to to go about

228
00:34:39 --> 00:35:11
it but again for apis on the system uh that talk will give you all the information that you need and even power that you cannot even imagine this the core image side can do can can control for the output almost everything what is the look what is the headroom being utilized how how the data is is merged together so you will have you will have fun to go through that

229
00:35:12 --> 00:35:38
that presentation it's great thank you for referring to the presentation next question is about the first start people are actually care about you know launching apps quickly so the question is deferred start says hold the photo output back the new high-res guidance says that says warm it early with set prepared photo settings array a contract

230
00:35:38 --> 00:36:01
educate older than defer deferral what's the supported composition does prepare queue pass deferral or force a start and what does each reserve yeah i could i could start with that um yeah i think like the way i view deferred start is really it's just all about getting that launch up and getting preview running so you're just basically moving the initialization from

231
00:36:01 --> 00:36:26
before preview to after preview so I don't think like in terms of it like in terms of using the warm on the photo setting array like you can basically still do that after you know previews running it I don't think it really makes much of a difference with deferred start I think deferred starts just moving that initialization out yeah one way to think of it is imagine a graph of

232
00:36:26 --> 00:36:49
objects where they have branches going out for preview and for photos and for for movies or whatever you're making the deferred start just says like we don't need to resolve all of the objects buffer pools etc on all of the branches to start we just have to get preview rolling as quickly as possible these guys can get done when they get done whereas the the prepared settings

233
00:36:49 --> 00:37:13
array, which you're right, has been there for a long time, is a way for you to tell the photo output up front, here's the worst it's going to be. This is the most crazy thing that I might ask for, quality plus whatever other features. And that lets us pre-allocate for the worst case in the still image pipeline. But you can do those at any time. You can re-prepare at any time. It's

234
00:37:13 --> 00:37:21
helpful if before you start your session, you do call set prepare settings array, tell us what the worst case is going to be.

235
00:37:21 --> 00:37:24
But I think these two APIs are kind of orthogonal to one another, would you say?

236
00:37:24 --> 00:37:25
Yeah, I would agree.

237
00:37:25 --> 00:37:32
And it is actually interesting, though, that the photo output quality will impact launch time if you don't use Deferred Start.

238
00:37:32 --> 00:37:36
Like, with the speed capture, the launch is actually going to be faster versus quality.

239
00:37:36 --> 00:37:39
And to your point, we have to do all these heavy allocations.

240
00:37:39 --> 00:37:40
So yeah, I agree.

241
00:37:40 --> 00:37:42
I don't think they collide.

242
00:37:42 --> 00:37:43
Yeah.

243
00:37:43 --> 00:37:45
And they can complement one another.

244
00:37:45 --> 00:37:46
Yeah, exactly.

245
00:37:46 --> 00:37:47
Yeah.

246
00:37:47 --> 00:37:50
Sounds-- I like when people agree.

247
00:37:51 --> 00:37:57
So, let's shift gears a little bit and talk about photos for a little while.

248
00:37:58 --> 00:38:05
So, the first question I have is, will adjusted PH assets' new rating property require PH library change request?

249
00:38:05 --> 00:38:10
Yep, it's a PH asset change request.

250
00:38:11 --> 00:38:17
You'll see the rating property there that you can change per PH asset.

251
00:38:17 --> 00:38:18
And there's a new enum.

252
00:38:19 --> 00:38:22
It's like values of unset and one through five.

253
00:38:23 --> 00:38:28
So that's how you can modify PHP ratings via the API.

254
00:38:29 --> 00:38:30
Okay, thank you.

255
00:38:31 --> 00:38:39
The second question I have is, is there a native way to obtain metadata of OG file type, PNG, JPEG, et cetera, from the photo selected?

256
00:38:41 --> 00:38:49
And they clarified, I ask this because every time a photo gets imported from photo speaker, it seems to only show as PNG.

257
00:38:49 --> 00:38:56
and a follow up on it doesn't mean it's getting converted before saving it to our app folder.

258
00:38:56 --> 00:38:59
- Gotcha, so there's a few things you should check.

259
00:38:59 --> 00:39:10
So when using the transferable to set up the data, be sure you're setting the UT type as opposed to, it sounds like maybe you're leaving it as a default image type.

260
00:39:10 --> 00:39:14
So specifying UT type is one thing to look out for.

261
00:39:14 --> 00:39:20
I believe there's some sample code on developer.apple.com for the photos picker from a session a few years ago.

262
00:39:21 --> 00:39:22
that should cover this.

263
00:39:22 --> 00:39:30
And then there's also, I mean, it sounds like you're giving PNG out, so I wouldn't necessarily expect this, but there can be some conversion that happens in the photo picker.

264
00:39:30 --> 00:39:45
For example, if a user has disabled captions or location from being given to your app via the picker, we may convert from some formats like raw, but it sounds like the PNG is the output.

265
00:39:45 --> 00:39:48
So I would double check the UT type for transferable.

266
00:39:48 --> 00:39:49
Makes sense.

267
00:39:49 --> 00:39:50
Thank you.

268
00:39:51 --> 00:39:59
And the third question I have, is there a way to learn about the adjustment PList format that the Apple Photos app writes?

269
00:39:59 --> 00:40:03
And when exporting that file, is there a way to import those adjustments back into Photos?

270
00:40:04 --> 00:40:08
So there's, we do not have API for that.

271
00:40:08 --> 00:40:13
So if that's something that's just desirable for your app, definitely file a feedback request.

272
00:40:13 --> 00:40:18
There's no way to sort of decode that PList, unfortunately.

273
00:40:20 --> 00:40:22
Makes sense. Thank you.

274
00:40:23 --> 00:40:25
Let's switch gears again.

275
00:40:25 --> 00:40:32
Let's talk about some pro features again.

276
00:40:34 --> 00:40:37
What is your recommended process to generate and write ISO conf...

277
00:40:37 --> 00:40:40
Oh, we already answered this.

278
00:40:41 --> 00:40:42
Sorry, my bad.

279
00:40:44 --> 00:40:50
Oh, actually, there's one really high-level generic question you can go to.

280
00:40:51 --> 00:40:57
What are the biggest mistakes developers can make when building a camera heavy apps on iPhone?

281
00:40:58 --> 00:41:25
quitting your day job. A couple really common ones would be, like I said earlier, the AV capture session is meant to be called on a background thread, on a dedicated serial queue. It, by design, blocks and waits when it does long, you know, reconfiguration of the graph. This is clearly documented. Hopefully you've read the documentation. So, you know, the most naive thing would be to

282
00:41:25 --> 00:41:46
just call AV capture session, start adding things to it. Call start running on the main thread. It will block your UI. You will get little spinners and people will give you one star. So don't do that. The other one is when you're reconfiguring your session, usually you're going to change more than one thing at a time. You know, like if you just set one property on it, it doesn't really

283
00:41:46 --> 00:41:51
matter if you call begin configuration and commit configuration because you're just doing one thing.

284
00:41:51 --> 00:42:03
But imagine you're changing from a photo mode to a video mode, or you're changing from one high-resolution active format to a lower one.

285
00:42:03 --> 00:42:05
Usually, you're going to do several steps there.

286
00:42:07 --> 00:42:16
Implicitly, the AV capture session will reconfigure its underlying graph every time you call a property that causes a disruptive change.

287
00:42:16 --> 00:42:36
the way to prevent it from doing this each and every time you set a property like on the way to getting to what you really want to do is to call begin configuration think of it like an atm where you need to like this is the start of the transaction i'm going to do a bunch of stuff but hold on like don't do anything until i'm i'm done and i commit at the end um and and if you do it

288
00:42:36 --> 00:42:47
that way then you ensure that you do one operation or 20 operations uh the graph is not going to re-evaluate and reconfigure itself until you say commit.

289
00:42:47 --> 00:42:54
So those are the two that come to mind as rookie mistakes if you're not using those two features.

290
00:42:54 --> 00:42:56
Any other ones, guys?

291
00:42:56 --> 00:42:59
I was thinking about video data output if you're using that to render preview.

292
00:42:59 --> 00:43:09
I think sometimes it's pretty easy to-- you get the frame data and you're all excited to do some processing, but you can end up actually dropping frames if you're doing too much of heavy lifting in there.

293
00:43:09 --> 00:43:17
So using preview layer, if you capture video preview layer to render a preview, if that's just your motivation, is maybe a better option.

294
00:43:17 --> 00:43:17
Right, right.

295
00:43:17 --> 00:43:30
Yeah, the only real reason to use a video data output for preview is if you need to interact with the buffers in some way, if you need to either get metadata from them, or draw on them, or meter them for histograms or something like that.

296
00:43:30 --> 00:43:33
But yeah, our video preview layer is really, really efficient.

297
00:43:33 --> 00:43:37
I don't think you're going to do better than the optimized path that we have.

298
00:43:37 --> 00:43:39
That's great.

299
00:43:39 --> 00:43:40
Yeah, thank you, Jake and Brett.

300
00:43:40 --> 00:43:43
Any more?

301
00:43:43 --> 00:43:45
Let's move on to the next question.

302
00:43:45 --> 00:43:50
So the question is about zoom, scroll, and pan feature.

303
00:43:50 --> 00:44:03
Any suggestions on a close-to-native performance way to achieve zoom, scroll, and pan feature for photo images, for example, up to only its original max resolution without pixelating?

304
00:44:03 --> 00:44:05
Davide, you want to take this?

305
00:44:05 --> 00:44:12
So I believe the best way is actually to use Core Image.

306
00:44:12 --> 00:44:26
CoreImage, as a framework, is actually built for adding this ability of, you know, having UI movement, Zoom, Pen.

307
00:44:26 --> 00:44:36
And it does that by caching almost everything that happens throughout a decoding pipeline.

308
00:44:37 --> 00:45:01
So I do believe there is talk or a document on the developer portal that actually does explain exactly this and shows how to set up a CI filter for opening images that then would allow you to modify, you know, what you do.

309
00:45:01 --> 00:45:32
instance uh the for a zoom decode only this rectangle everything is cached and moving this rectangle around so it's really it's a really powerful uh framework for doing exactly this stuff one thing to note is uh just because you know i come from the pro work workflow um the the on the raw side core image has also given is a counterpart CI raw filter for it it has

310
00:45:32 --> 00:45:53
the same capabilities so even if you open hundred megapixel images and then try to pan around all these features all this capability of core images still are present so you're scrolling you're zooming will still stay very very smooth because of this caching capability that Core Image has.

311
00:45:54 --> 00:45:55
That's great to know.

312
00:45:55 --> 00:46:06
If I can echo something, riffing off of that, the region of interest, the RI management in Core Image is really one of the places where it really shines.

313
00:46:07 --> 00:46:23
If you, for example, have an image that has a number of heavy operations that are applied to it, and then you zoomed in a lot, and you move around, it's only going to go and recompute the area that you're zoomed into, even if the image is many times larger.

314
00:46:23 --> 00:46:34
So it will do all kinds of optimizations like that to make sure that you're not essentially wasting cycles on things that are not going to impact your application exactly.

315
00:46:35 --> 00:46:36
Yeah, thank you.

316
00:46:37 --> 00:46:41
We've got some really highly upvoted questions.

317
00:46:42 --> 00:46:44
Let's go back.

318
00:46:44 --> 00:46:45
Let's go to those.

319
00:46:45 --> 00:46:48
So how many times can I use as an intelligence feature?

320
00:46:48 --> 00:47:11
and also when using it does it affect or change my picture quality who can take this this is about the siri camera yeah yeah i believe so um i don't know if you want to take it yeah sure yeah so uh there's a on on limit uh you can use it as much as uh you want on the device as far as the the quality those captures don't go to the user's uh normal photo library uh they get saved to the the

321
00:47:11 --> 00:47:34
siri app um and the as far as quality their screen resolution aspect ratio so you're you're not getting you know the full-blown image quality that you'd get in photo mode for example yeah so don't use it if you're trying to get like the most beautiful photos it's in order to have a conversation with siri about that photo yeah i believe there is some limit on the number of

322
00:47:34 --> 00:48:04
conversational things you can say about a photo or ask about a photo sense thank you for that next question the new spatial reframe feature uses 3d modeling to reconstruct the scene from a different angle as a developer can we access that pipeline via an api or is it locked to the photo app so that yeah there's no there's no uh developer api there for using that that reframe

323
00:48:04 --> 00:48:28
capability there are some parts of the air kit framework that allow for 3d scene capture and reconstruction and manipulation but it's not a sort of out of the box uh you know photos reframe edit solution and we have if you're interested in live captures that make use of it's not exactly spatial features, but we do have depth sensing cameras that we can use. On the pro phones,

324
00:48:28 --> 00:48:51
we have the LiDAR sensing camera. So we do have an AV capture device that uses the LiDAR depth camera as well as the RGB camera and fuses them together to give you depth. And then if you're using the front-facing camera, the true depth camera, there's infrared that you can use for capturing depth. But no, that's a good feature to request as a feedback assistant.

325
00:48:51 --> 00:48:52
- Yeah, sure.

326
00:48:52 --> 00:48:54
So file that feedback assistant request.

327
00:48:55 --> 00:48:59
Next question is about rotation handling.

328
00:49:00 --> 00:49:05
When can you get the rotation issue just auto-handled by the camera capture?

329
00:49:05 --> 00:49:16
Since on iOS and macOS and iPadOS, it always seemed to handle orientation differently, I spent so much time tweaking this for any new app and always get it wrong.

330
00:49:16 --> 00:49:17
- Geometry is hard.

331
00:49:17 --> 00:49:18
- Yes.

332
00:49:18 --> 00:49:41
- I can't tell you how many times I've taken a piece of paper and drawn a smiley face, and then like drawn it in all the different orientations, and then flipped it around. And it's just reality, you know, like, you're dealing with a, an ID, a device that's got a camera in it, the camera is physically mounted a certain way, a different camera in the same ID

333
00:49:41 --> 00:50:04
might be mounted a different way. One of them might be portrait, one might be landscape, there's really no getting around the need for for dealing with orientation. It's not something that we can authoritatively do correctly for you because we don't know your intent. We could say, well, gravity is here. So probably they want this to be up, but maybe not. Maybe, maybe it's an always

334
00:50:04 --> 00:50:27
portrait app and that's the wrong thing to do. So this is why we don't just automatically change, you know, pull the rug out from under you and you do need to deal with, with rotation. But the good news is we have some good tools that we've introduced in the last, maybe like three years AV capture device rotation coordinator is something that can help

335
00:50:27 --> 00:50:54
you either dial in your what the correct rotation degrees should be for preview or to keep something horizon level upright you know so we have those those two flavors that you can ask it and based on that you can know how you need to rotate the images afterwards and then you can also just tell the framework to do it for you. By talking to your video capture connection, you can set the video rotation angle

336
00:50:54 --> 00:51:17
on it, and then we will automatically rotate it for you. The way that we rotate it may be different depending on the output. If you're dealing with video data output and you tell us to rotate the video, we will physically rotate the buffers. If you're using a photo output, we don't have to physically rotate them. We'll use EXIF data to tell it, rotate it on playback. And same thing

337
00:51:17 --> 00:51:43
with movies. It would be great if, for the asker of this question, who was it? Joshua Arzenic? No, Michael Rowan. Great question. We have the new iPhone 17s, for the first time, have a front-facing camera that's oriented differently than on previous iPhones, which has caused some people some consternation. We talked about that in, a member of my team,

338
00:51:43 --> 00:51:52
And Tracy talked about that in a session about the new center stage camera, and it's called Support the Center Stage Front Camera in Your iOS App.

339
00:51:52 --> 00:52:06
And there is a portion of that session dedicated to the vagaries of rotation and how to do it correctly so that you're defended against future changes to ID and rotation.

340
00:52:07 --> 00:52:09
You can write the code now, and it'll be correct in the future.

341
00:52:11 --> 00:52:11
That's great.

342
00:52:12 --> 00:52:12
Thank you.

343
00:52:14 --> 00:52:22
The next question is about a 24-megapixel image capture from Joshua Arzensek.

344
00:52:23 --> 00:52:24
I hope I pronounced it well.

345
00:52:24 --> 00:52:30
Is it possible to capture 24-megapixel images with depth data on the front for the capture?

346
00:52:31 --> 00:52:31
Yes.

347
00:52:32 --> 00:52:40
So we covered 24 megapixels in one of the sessions this year regarding capturing high-resolution images.

348
00:52:42 --> 00:52:44
And there's a couple of details that you need to follow.

349
00:52:44 --> 00:52:51
First, 24-megapixel processing is time-consuming enough that we require that you opt in to deferred processing.

350
00:52:54 --> 00:53:02
And second, you should make sure that you opt in into the max photo dimensions so it actually supports the device that you've selected supports 24 megapixels.

351
00:53:03 --> 00:53:18
But if you do that, if you select quality prioritization as well as enable depth data delivery, you will get captures with depth.

352
00:53:20 --> 00:53:20
Great.

353
00:53:24 --> 00:53:27
Let's go to...

354
00:53:27 --> 00:53:29
So many good questions.

355
00:53:29 --> 00:53:30
Yeah, really good questions.

356
00:53:31 --> 00:53:33
Thank you so much for submitting all these questions.

357
00:53:35 --> 00:53:37
We have a really good one here.

358
00:53:38 --> 00:53:40
At GroupLabs, shut an iPhone.

359
00:53:40 --> 00:53:45
Also, what APIs and technologies can you use to build such a multi-camera streaming apps?

360
00:53:45 --> 00:53:46
Ooh, nice.

361
00:53:46 --> 00:53:47
The answer is yes.

362
00:53:48 --> 00:53:49
I'm staring at an iPhone, right?

363
00:53:49 --> 00:53:52
There are many iPhones in tripods.

364
00:53:53 --> 00:53:54
We're seeing iPhones everywhere.

365
00:53:55 --> 00:53:59
You know, last year we did so much with iPhone 17's front camera.

366
00:53:59 --> 00:54:01
We called it the year of the selfie internally.

367
00:54:01 --> 00:54:03
But it was also the year of other cool stuff.

368
00:54:04 --> 00:54:07
It was probably the biggest year we've ever had for pro video.

369
00:54:08 --> 00:54:29
So we had a number of new features introduced for, you know, pro settings, you can now use your iPhone and get, you know, amazing quality out of it, like the ProRes and ProRAW that we've talked about, but also just for a functionality, like if you want to do a multi-cam shoot. There were two new features that were specifically great for this.

370
00:54:30 --> 00:54:53
One of them is locked frame duration. So if you're working in a pro environment, you need to make sure that your frame rate is exactly 2997, no deviation ever. It's not enough to just try to set your min and max frame rate to the same thing. We introduced a locked frame duration API that ensures that the video is rock solid 2997 or 24 or 25 or 60 or whatever you need it to be,

371
00:54:53 --> 00:55:17
and that the audio is synchronized to that. Furthermore, there is an extension to that, which is that you can ask for an external sync source to be the one that multiple iPhones synchronize to, which is called GenLock, and it's something that's used in pro environments all the time to make sure that all of the cameras are synced up, and when you go put them all in a timeline,

372
00:55:17 --> 00:55:21
there's no tearing. Everything is exactly at the same start time.

373
00:55:22 --> 00:55:29
And so using a third-party Blackmagic ProDoc, you can plug one of those into an iPhone.

374
00:55:29 --> 00:55:33
You can use an external gen locks generator.

375
00:55:33 --> 00:55:41
You can put them all on that same time source, and then all the recordings will be perfectly synchronized when you bring them into Final Cut later.

376
00:55:41 --> 00:55:44
And then lastly, we also introduced time code generation.

377
00:55:44 --> 00:55:56
So you can get time code from an external source through your AV capture session as an output now, and you can associate time code with video frames that you store to a movie.

378
00:55:57 --> 00:56:06
So in addition to having metadata, video, and audio, you've also got a time code track, which makes it much easier in post to go and say, you know, go to time code number, whatever.

379
00:56:07 --> 00:56:09
So, yeah, we're using it here.

380
00:56:09 --> 00:56:12
There's also a ton of API that you can use for that, too.

381
00:56:12 --> 00:56:13
Yeah, 100%.

382
00:56:13 --> 00:56:18
Maybe just to plug the AV Pro Video Storage API that we just released this week.

383
00:56:19 --> 00:56:27
So that's, you know, if you're using ProRes for your, like, 4K30 video captures, you know, we're writing a ton of data to disk.

384
00:56:27 --> 00:56:34
And AVPro Video Storage really helps to give you that deterministic file write speeds, so that way you're not dropping any frames.

385
00:56:34 --> 00:56:41
So we talked a little bit about it in the session that I had this week, but there's a lot of documentation on it.

386
00:56:41 --> 00:56:45
It's, I think, a pretty cool feature for people to start using.

387
00:56:45 --> 00:56:46
Great.

388
00:56:46 --> 00:56:47
Thank you.

389
00:56:47 --> 00:56:50
And I think we have time for just one more question.

390
00:56:50 --> 00:56:53
Let's just give it a brief answer.

391
00:56:53 --> 00:56:58
Is there a niche new API that may not be talked about so much?

392
00:57:00 --> 00:57:03
I think Jake just hit it there.

393
00:57:03 --> 00:57:07
That brand new one that we introduced is a great performance optimization.

394
00:57:08 --> 00:57:16
Yeah, if you are interested in ProRes, if you want to capture to ProRes, you really should use this new ProVideo Storage API.

395
00:57:16 --> 00:57:23
It gives you a pre-allocated file on your disk that you can write to.

396
00:57:23 --> 00:57:29
even if your phone is old and it's got a fragmented disk, you can be sure that it's not going to drop frames.

397
00:57:29 --> 00:57:33
So we're retrofitting older phones with the same API.

398
00:57:34 --> 00:57:36
You can use it, and we encourage you to use it.

399
00:57:36 --> 00:57:42
It may have worked before okay, but now you can ensure that it will work well for years to come.

400
00:57:43 --> 00:57:45
It's great for a professional type like this, right?

401
00:57:45 --> 00:57:45
Yeah.

402
00:57:45 --> 00:57:48
The settings UI is actually pretty nice for it too.

403
00:57:48 --> 00:57:52
Users can control exactly how much they actually want to dedicate to this.

404
00:57:52 --> 00:57:55
It's just a little better for your whole disk space.

405
00:57:55 --> 00:57:58
Another great one, Davide, is the ProRAW.

406
00:58:00 --> 00:58:30
We have a new RAW 9 engine that is on the 27 version of the OS that I think is going to blow everybody's minds away because it's an ML solution for problems like debayering, raw files from third-party cameras and the outputs are outstanding yeah and unfortunately we won't be able to double click on that one today but as there is

407
00:58:30 --> 00:58:55
a great session by David Hayward or you know veteran on the camera team enhanced raw image processing it is core image so I would you know anyone who is interested in that please watch that and it you know talks about the difference between row 8 and row 9 and that's about all the time you have today for this group lab. Thank you everyone for joining us today. And big thanks to all our

408
00:58:55 --> 00:59:17
panelists for such an insightful conversation. I hope you enjoyed it as well. As I mentioned earlier, if you have more questions, developer forums is a great place to get them answered. And don't forget, you can file bugs or feature requests with feedback assistant. We actually do read them. And actually speaking of feedback, we'll send you a survey link via email.

409
00:59:17 --> 00:59:22
And your participation in this survey is so important for us.

410
00:59:22 --> 00:59:26
It will help us make these future events better in the future.

411
00:59:26 --> 00:59:28
So please fill out that form.

412
00:59:28 --> 00:59:31
And with that, thank you again, and hope you have a great WWDC.
