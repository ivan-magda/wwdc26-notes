---
title: Accessibility Technologies Group Lab
source: https://developer.apple.com/videos/play/wwdc2026/8005/
session: 8005
collection: wwdc2026
duration: 01:00:31
fetched: 2026-06-12
via: whisper.cpp ggml-large-v3 (session SD video audio)
transcription: machine-generated, no speaker diarization
---

> Machine transcription of the **Accessibility Technologies Group Lab** SD video via whisper.cpp (`ggml-large-v3`).
> Group labs ship no Apple transcript; this is generated from the audio. Cues are timestamped segments, **not** speaker-attributed turns.

## Transcript

1
00:00:02 --> 00:00:03
Hello, world.

2
00:00:03 --> 00:00:07
My name is Cole, and I am a core technologies evangelist here at Apple.

3
00:00:07 --> 00:00:13
And I am delighted to welcome you to the Accessibility Technologies Group Lab at WWDC.

4
00:00:13 --> 00:00:16
Today, I'm joined by a panel of experts.

5
00:00:16 --> 00:00:20
These are just a few of the folks that work on accessibility at Apple.

6
00:00:20 --> 00:00:26
And we're here to chat about your questions to help you include everyone in your apps experiences.

7
00:00:26 --> 00:00:34
And in addition to those on screen, there's a team of folks behind the scenes helping with the triage of all of your inbound questions.

8
00:00:34 --> 00:00:37
So, let's kick this off with a round of introductions.

9
00:00:37 --> 00:00:42
Maybe I'll just ask each of you to introduce yourself and talk a little bit about what you're focused on at Apple.

10
00:00:42 --> 00:00:44
Let's start with Julia.

11
00:00:44 --> 00:00:45
- Hi, I'm Julia Sonnen.

12
00:00:45 --> 00:00:49
I'm the product marketing manager for accessibility, so I'm in the worldwide product marketing group.

13
00:00:49 --> 00:00:55
And I cover basically all of our accessibility features across all of our products.

14
00:00:55 --> 00:00:57
- Great.

15
00:00:57 --> 00:00:58
- Hey everyone, my name is Drew.

16
00:00:58 --> 00:01:01
I'm a software engineer on the accessibility team.

17
00:01:01 --> 00:01:08
I work on iOS, VisionOS, iPadOS, and yeah, across different disciplines in accessibility with a focus in cognitive.

18
00:01:10 --> 00:01:11
I'm Greg.

19
00:01:11 --> 00:01:16
I am a manager for a software engineering team that works on a lot of the different accessibility products.

20
00:01:16 --> 00:01:19
I'm also a user of many of our low vision products.

21
00:01:20 --> 00:01:21
Hi, everybody.

22
00:01:21 --> 00:01:25
I'm Syl, and I am part of the software engineering accessibility quality team.

23
00:01:26 --> 00:01:30
I'm a voiceover user myself and spend a lot of time testing voiceover and braille.

24
00:01:31 --> 00:01:31
Great.

25
00:01:31 --> 00:01:33
Well, thank you so much for joining me, everyone.

26
00:01:34 --> 00:01:47
So, just to kind of kick things off, I think for folks that might be listening and maybe are not familiar with everything in the 27 releases for accessibility, maybe we can just start by talking about what's new.

27
00:01:47 --> 00:02:00
Julia, I know you are just on the tail of GAD and all of the announcements that folks might have seen on the Newsroom site, but do you want to just maybe chat about some of the folks that people can find in the new releases?

28
00:02:00 --> 00:02:29
Absolutely, yeah, and for anybody who hasn't checked it out, I would definitely recommend going to Newsroom, and there's a post from Global Accessibility Awareness Day, or GAD, May 21st is the day of GAD, but I think Newsroom came out on the 19th, but anyway, it's a really good summary of all of the amazing accessibility stuff that is now, or is mostly in iOS, or 27 releases, so it's a really good year for accessibility.

29
00:02:29 --> 00:02:47
I mean, I've got to say that, right? I'm the marketing person. But we had a huge push around Apple intelligence and bringing Apple intelligence thoughtfully and purposefully into accessibility features in ways that make sense for our users.

30
00:02:47 --> 00:02:52
So we have voiceover, image descriptions and live recognition enhancements.

31
00:02:53 --> 00:02:56
We have more personalized accessibility reader.

32
00:02:56 --> 00:02:58
We have intuitive voice control.

33
00:02:59 --> 00:03:06
And then we also have some other cool intelligent features like automatic subtitles in personal videos.

34
00:03:07 --> 00:03:07
Awesome.

35
00:03:08 --> 00:03:08
What's your favorite?

36
00:03:10 --> 00:03:16
I got to say like the subtitles for the features or for videos.

37
00:03:16 --> 00:03:28
I take a lot of pictures or a lot of videos of my kid and so sending those videos to my parents and having the subtitles on there because they're always like, what is she saying?

38
00:03:28 --> 00:03:32
So now they can kind of see that and it's just really special.

39
00:03:32 --> 00:03:33
Totally. That's great.

40
00:03:33 --> 00:03:35
Maybe I'll pitch it to the rest of the team.

41
00:03:35 --> 00:03:41
Is there anything, even if it's not brand new in the 27 releases, but anything that's relatively recent that you're really excited about?

42
00:03:43 --> 00:03:45
I'm super hyped about vehicle motion cues.

43
00:03:45 --> 00:03:51
I have them turned on on my phone, and then it being brought over to VisionOS this year is just super cool.

44
00:03:52 --> 00:03:56
Having them be spatialized is great, especially while you're wearing Vision Pro.

45
00:03:57 --> 00:04:00
Totally. Greg, Syl, anything that comes to mind?

46
00:04:01 --> 00:04:11
Yeah, I think voice control, the improvements using AI now where you can describe anything on screen with much more natural language will be a game changer for users.

47
00:04:13 --> 00:04:18
I am so thrilled about the built-in image descriptions with VoiceOver that came this year.

48
00:04:18 --> 00:04:40
um i had somebody send me a photo recently of myself riding an anteater on a carousel and so just being able to um swipe up on that image with the voiceover custom action and double tap get an image description without having to activate the photo go to the share sheet send it off to some other ai model um it was it's just such a seamless experience now and really helps with

49
00:04:40 --> 00:05:04
me being able to enjoy my photos that is awesome and that's great to hear um okay uh well i think maybe let's take a look at some of the questions that are coming in from developers. I see a whole bunch, so please keep sending them in. Let's start with, there is a question about testing usability. The question is, what is your best advice for testing usability? Accessibility

50
00:05:04 --> 00:05:08
inspector to audit elements and device hub for testing across different screen sizes.

51
00:05:09 --> 00:05:21
Any other advice that comes to mind? When I see, when I think about, you know, testing your UI with different screen sizes, something that comes to mind first is dynamic type or large text.

52
00:05:21 --> 00:05:23
And I think these two things go hand in hand.

53
00:05:23 --> 00:05:35
You want your UI to be laying out dynamically so that depending on the font size the user has turned on, whether it's an accessibility font size or not, sometimes it makes sense to relay out the UI elements in your app.

54
00:05:35 --> 00:05:42
And that also is something that you should be aware of, too, if you're working with devices with multiple or different screen sizes.

55
00:05:42 --> 00:05:45
So I always think about those two things together.

56
00:05:45 --> 00:06:10
Gotcha. And actually, just on that note, I'll kind of pull this one up because it's really similar. The question is, this is an honest one. On most teams, accessibility testing means voiceover, and then they call it a day. And switch control and voice control and full keyboard access rarely get a pass. How does your team prioritize across assistive techs

57
00:06:10 --> 00:06:15
when you can't test everything, and which one do we most underestimate?

58
00:06:17 --> 00:06:19
Scylla, I know you do a lot of testing.

59
00:06:19 --> 00:06:20
What do you think?

60
00:06:20 --> 00:06:22
Yeah, first of all, I appreciate the honesty.

61
00:06:22 --> 00:06:24
I think that is the place where a lot of folks are in.

62
00:06:25 --> 00:06:31
You know, most people don't have teams of experts for every single accessibility feature, and that is still okay.

63
00:06:31 --> 00:07:01
You can still deliver a great accessible experience even if you don't have folks from every single accessibility need on your team but one really great thing about testing voiceover first is a it's really it's approachable it's a little bit more convenient for most folks generally than obtaining a switch and going through that process so just being able to turn

64
00:07:01 --> 00:07:25
on voiceover and give that a go and it's it's okay if that is all you have the time for if all that's all that resources allow for. Because in many cases, things for switch control and voice control accessibility are also coming from a great voiceover accessible experience. So some of that comes for free. Of course, it's great to test it as well if you can. But if you can only test

65
00:07:25 --> 00:07:44
voiceover, focusing on giving a great voiceover experience will also hit a lot of the other things too. And I think the actual APIs for like voice control, for example, they do exist, but it's a fairly small API surface compared to what all of the other assistive technologies have in common.

66
00:07:44 --> 00:07:45
Is that right?

67
00:07:45 --> 00:07:47
Yeah.

68
00:07:47 --> 00:07:53
If voice control works well, most of the other technologies are going to work pretty well in addition to that.

69
00:07:53 --> 00:08:07
The additional API for a lot of our technologies is kind of really nice polish around the edges, really refining that experience, really making it just an outstanding voice control experience or a switch control experience.

70
00:08:07 --> 00:08:15
I think that matches very similar to what Sil was saying here in that you get a lot of bang for your buck with voiceover testing.

71
00:08:15 --> 00:08:20
And then there's more that you can get adding on to each product.

72
00:08:20 --> 00:08:22
But the first step is voiceover testing.

73
00:08:22 --> 00:08:26
And I think in addition to that, getting real users can really help.

74
00:08:26 --> 00:08:27
It's not required.

75
00:08:27 --> 00:08:30
You can get pretty far testing yourself.

76
00:08:30 --> 00:08:35
But getting input from real users can really help to polish an accessibility experience.

77
00:08:35 --> 00:08:41
Yeah. I feel like I kind of liken it to getting just design feedback from the visual appearance of your app, right?

78
00:08:41 --> 00:08:44
Like you want to listen to your customers and get their feedback.

79
00:08:44 --> 00:08:50
And people using a different interface of your app also have feedback and you want to include them in your design process.

80
00:08:51 --> 00:08:56
Drew, did you have anything to add to kind of the idea of testing and approaches for that?

81
00:08:56 --> 00:09:03
Just the plus one. I mean, yeah, like some of these technologies share literally the same backend API, like is accessibility element or accessibility label.

82
00:09:04 --> 00:09:07
Switch control can read or speak accessibility labels.

83
00:09:07 --> 00:09:15
So yeah, we try and design the accessibility features to work this way so that, yeah, like Greg said, I love that quote, get the most bang for your buck.

84
00:09:15 --> 00:09:22
If you have a great voiceover experience, it tends to mean that the experience for other technologies is pretty good as well.

85
00:09:22 --> 00:09:24
- That's great.

86
00:09:24 --> 00:09:26
Taking a look, there's another question.

87
00:09:26 --> 00:09:30
This kind of goes back to the image descriptions we were talking about earlier.

88
00:09:30 --> 00:09:36
The question is, is there any way to override or turn off the system-provided description of an image.

89
00:09:37 --> 00:09:44
I have an app that displays artwork, and when navigating my app, VoiceOver will read my provided description immediately followed by the system's description.

90
00:09:44 --> 00:09:48
I'm using accessibility label modifier on a SwiftUI image.

91
00:09:50 --> 00:10:01
Yeah, I think if you take off the image trait, VoiceOver will stop trying to append its own description to it, and it should just kind of represent your accessibility label that you provide.

92
00:10:01 --> 00:10:02
Gotcha.

93
00:10:02 --> 00:10:03
Great.

94
00:10:04 --> 00:10:06
But if you, yeah, we'll go ahead.

95
00:10:06 --> 00:10:09
I was going to say, it's a really good question, right?

96
00:10:09 --> 00:10:13
It's a question about artists and their art and should it be described?

97
00:10:13 --> 00:10:15
And I could go either way, right?

98
00:10:15 --> 00:10:27
For somebody who's blind, somebody that can't see it, the description that the author created is probably a really great description, but still having it described literally with AI could also be really helpful for someone who's blind.

99
00:10:27 --> 00:10:40
And I think it's a tough question to what is the right decision there for in that case, or probably in many artistic cases where we don't want to infringe or hinder the artist's creativity.

100
00:10:40 --> 00:10:47
But some of these features, what we want to do is let somebody who has a disability experience that art as well.

101
00:10:48 --> 00:10:53
And for somebody who's blind, the way they can experience it might be through these image descriptions.

102
00:10:53 --> 00:11:18
might be through some of these AI tools, might be some of those additional things for them to experience the art. So do you have any thoughts on that? Yeah, because, I mean, keep in mind that after your own alt text reads, there is a teeny tiny little sound. It's pretty subtle, but it is on by default, and it happens before the auto-generated description pops in. So the user

103
00:11:18 --> 00:11:40
will know that that's not the one that you provided and sorry drew i don't mean this in a bad way but um if you do take the image straight off of it that would deny the user to be able to access the new image description stuff because we do rely on that um and there is some functionality in there that lets the user ask further questions about the image and things like that so they they

104
00:11:40 --> 00:12:03
might want to be able to get a little more out of it very fair sorry no no that's awesome thank you Um, uh, this, uh, seeing the question here that kind of goes back a little bit to some of the stuff that you were talking about, Julia, um, the, the question reads, uh, I'm a third year medical student, uh, building an EHR for underserved areas. Part of it is a patient portal.

105
00:12:03 --> 00:12:13
I was wondering what new accessibility technologies and features announced at WWDC, uh, would be helpful for patients that I can now fold into the development from now.

106
00:12:13 --> 00:12:21
I can try to attempt to answer that one, and then you guys back me up with technical details.

107
00:12:22 --> 00:12:29
So I think Accessibility Reader is potentially a really great tool here because it's a system-wide tool.

108
00:12:29 --> 00:12:36
You can launch it from any app and customize reading experience.

109
00:12:36 --> 00:12:37
It's really meant for any long-form content.

110
00:12:38 --> 00:12:56
What I personally like about Accessibility Reader is that you can kind of have a setup, a style that you like, And then just any given text, like from Mail or from Safari or from Pages even, you can just view that how you like it.

111
00:12:56 --> 00:12:59
And it's also integrated with spoken content, which is super cool.

112
00:12:59 --> 00:13:04
The other one that comes to mind is image descriptions in Image Explorer.

113
00:13:04 --> 00:13:21
Because if there's charts and data that are in this portal, then those tools are really great at not just describing photographs and pictures, but also data-rich images and charts.

114
00:13:22 --> 00:13:22
Gotcha.

115
00:13:22 --> 00:13:36
Yeah, and just more generally, even if it's not the new technologies, I think I kind of read, and I hope I'm not misinterpreting this question, But I kind of also read this as, like, where do you start?

116
00:13:36 --> 00:13:48
Like, you know, if you're working on a very data-rich, complex app, it can sometimes feel a little daunting to figure out how we're going to make sure that that provides a great experience with voiceover.

117
00:13:48 --> 00:13:56
So do you have any thoughts on kind of, like, what are the first things that come to mind as steps when you're, like, embarking on that project?

118
00:13:56 --> 00:14:01
And you want to make sure that it works well with the, you know, wide breadth of assistive technologies that are available.

119
00:14:04 --> 00:14:05
That's a hard question.

120
00:14:06 --> 00:14:06
You're right.

121
00:14:06 --> 00:14:08
There are a lot of different technologies.

122
00:14:09 --> 00:14:11
I would probably suggest starting at dynamic text.

123
00:14:11 --> 00:14:19
I think there's just so many people that use dynamic text and so many people that can benefit from being able to increase the font size in many different applications.

124
00:14:19 --> 00:14:25
And then from there, as we were talking about earlier, voiceover support gets you some big wins.

125
00:14:25 --> 00:14:33
And so dynamic text kind of gets your feet wet, and then you continue on to adding accessibility labels and continuing to refine your application with voiceover support.

126
00:14:35 --> 00:14:40
Any other thoughts?

127
00:14:40 --> 00:14:41
No, that's great.

128
00:14:41 --> 00:14:43
That makes total sense.

129
00:14:43 --> 00:14:53
And I think I'm just kind of looking at the other questions here.

130
00:14:53 --> 00:14:57
Let's move on to the next question, because I will come back to that.

131
00:14:57 --> 00:15:02
I have another question from a developer that is also a voiceover question.

132
00:15:02 --> 00:15:08
And it reads, in many places in my app, the user taps a button which triggers a network request.

133
00:15:08 --> 00:15:14
For sighted users, I show a progress view in place of the button, then replace the content of the button.

134
00:15:14 --> 00:15:17
How do I make that accessible to users of iOS VoiceOver?

135
00:15:17 --> 00:15:23
I'd like VoiceOver to announce the action completed.

136
00:15:23 --> 00:15:25
Thoughts?

137
00:15:25 --> 00:15:31
You can use the notifications that-- we have a few different accessibility notifications.

138
00:15:31 --> 00:15:32
Two of them are similar.

139
00:15:32 --> 00:15:34
One is going to be layout changed.

140
00:15:34 --> 00:15:36
The other is screen change notification.

141
00:15:36 --> 00:15:54
And this tells VoiceOver that something in the app layout has essentially changed and maybe it's like a visual only change and you can use that to I think like layout change is usually for smaller UI changes whereas screen change is like you know the whole something like the whole screen is changing so I think like if you know if a button

142
00:15:54 --> 00:16:10
layout is being updated while the voiceover cursor is focused on it layout change is probably a best way to do it you can even pass in an accessibility element to bring voiceover focus to that element But you have to be careful that you don't do that too often.

143
00:16:10 --> 00:16:16
You can imagine that someone using VoiceOver doesn't want their focus to be pulled in a bunch of different directions.

144
00:16:16 --> 00:16:23
But you could also do the accessibility announce notification, which you just put in a string, and VoiceOver speaks the string.

145
00:16:23 --> 00:16:26
So that could be another approach as well.

146
00:16:26 --> 00:16:27
Good idea.

147
00:16:27 --> 00:16:29
Great.

148
00:16:29 --> 00:16:31
I think this will be a pretty easy question.

149
00:16:31 --> 00:16:36
The question reads, are there any updates to the text to speech APIs this year?

150
00:16:36 --> 00:16:37
I'm not aware of any.

151
00:16:37 --> 00:16:40
Are you folks aware of anything?

152
00:16:41 --> 00:16:46
I know there's a lot of improvements on the back end, but I don't think there's new API.

153
00:16:47 --> 00:16:47
Gotcha.

154
00:16:48 --> 00:16:53
So for folks listening that are looking for some improvements, those are some great feedback requests to file.

155
00:16:53 --> 00:16:54
That's great.

156
00:16:56 --> 00:17:09
Okay, I know I want to kind of circle back because we were talking about dynamic type a little earlier, and I know one of the big features this year is that dynamic type is now available on tvOS.

157
00:17:10 --> 00:17:16
So I'm really interested in your thoughts of like, what does this look like for maybe our tvOS developers listening?

158
00:17:17 --> 00:17:18
What should they be thinking about?

159
00:17:18 --> 00:17:22
And what does that work look like to get ready for dynamic type?

160
00:17:22 --> 00:17:30
I think a good thing would be to start being familiar with what the best current practices are for large text.

161
00:17:30 --> 00:17:33
Like what a good layout would be, kind of like we mentioned earlier.

162
00:17:33 --> 00:17:38
Sometimes the flow of the UI in your app should change if an accessibility font size is enabled.

163
00:17:39 --> 00:17:46
And we have a new WWDC session this year by ECs talking about large text and tvOS specifically.

164
00:17:46 --> 00:17:49
So that's a huge recommendation to check that out for sure.

165
00:17:50 --> 00:17:52
We also have other previous talks about large text too.

166
00:17:53 --> 00:18:00
So you can start with iOS, you can graduate to tvOS or get your feet wet with tvOS right away.

167
00:18:00 --> 00:18:22
And I think that means we get a nutrition label, the ability to do a large text nutrition label for tv os so that's super exciting so actually since you raised that i i do see a question about accessibility nutrition labels uh the question reads we are very excited about the accessibility nutrition labels please talk about how valuable they are and any details you would like to expand

168
00:18:22 --> 00:18:40
oh my goodness well it's a near and dear to my heart um so accessibility nutrition labels are a way for users to find out about the um about the accessibility of an app before they download it And it's also a way for developers to connect with their users in a new way.

169
00:18:41 --> 00:18:47
And we have, you know, if you go back in previous DubDub sessions last year, we had a really good one.

170
00:18:49 --> 00:18:53
And just on how to kind of prepare your apps and evaluate them for nutrition labels.

171
00:18:55 --> 00:19:03
I just, you know, I get to go out in the world and talk to, you know, community members and users.

172
00:19:03 --> 00:19:08
I was at a conference this year, and a guy was so excited to come up to tell me.

173
00:19:08 --> 00:19:13
He pulled up his phone, and he pulled the App Store up, and he's like, we did it.

174
00:19:13 --> 00:19:14
I got my nutrition label.

175
00:19:15 --> 00:19:20
And it was actually for, like, a large, I'm not going to say who it is, bank.

176
00:19:21 --> 00:19:45
And so what was cool is because of the nutrition label and this concept, he was able to use that as a way to advocate for getting, you know, kind of that support within his company to make this happen and that just like gave me warm fuzzies um so that's i think my favorite my favorite nutrition label story so far yeah oh go ahead

177
00:19:45 --> 00:20:04
i think it's a really big game changer both for developers and end users if you can imagine it's for me for someone that uses a lot of large text and zoom but also for a voiceover user downloading an app without that information would be like downloading without knowing if it's in English, right? It's just, they're just gambling at that point. And so it's a really big win for

178
00:20:04 --> 00:20:22
customers for them to be able to know if their app's going to work with the assistive technologies that they use. In addition to that, I think it's a really nice way for developers to distinguish themselves to say like, hey, I actually put in the effort here. I care about all these different features. I put in the effort and now they get those nice little badges for their application.

179
00:20:23 --> 00:20:44
So I think it's so many wins all around. I totally agree with that. And as a user myself, I cannot even tell y'all how many hours of my life I've spent downloading apps that aren't actually accessible, trying them, deleting them. Then sometimes I even need to try to get a refund if it's something that costs me money. And just being able to know right away if an app is going

180
00:20:44 --> 00:20:56
to work for me is, as Greg said, it's a huge time saver. And it just really reduces the cognitive load that exists as trying to navigate a world that might not always be designed for me.

181
00:20:56 --> 00:21:23
Totally. And I think one of the things that I really like as a developer about the fact that they exist is that they come with guidelines, they come with kind of like, this is the criteria that I should be testing against. And that's, that's a, I think that's a great benchmark. Like you said, like, you know, having that, like documentation, that guideline to like, go champion this work. Before you add those labels to the store.

182
00:21:23 --> 00:21:35
I've heard from developers tell me as well that like just having that documentation is a great motivator to kind of get the testing done and kind of reevaluate the app and then make it even better.

183
00:21:35 --> 00:21:36
So that's great.

184
00:21:37 --> 00:21:40
OK, I'm going to take a little bit of a detour.

185
00:21:40 --> 00:21:45
I see a question about Device Hub.

186
00:21:46 --> 00:21:49
So I'm going to bring this up.

187
00:21:49 --> 00:21:58
The question is, I was very excited to see that there's a voiceover option in the Xcode 27 beta device hub and was disappointed that when I tried to use it, it didn't do anything.

188
00:21:59 --> 00:22:00
There's a feedback number here.

189
00:22:01 --> 00:22:04
Is it intended to work on simulators or only on physical devices?

190
00:22:05 --> 00:22:08
Is this the year that we can test iOS voiceover on a Mac?

191
00:22:10 --> 00:22:11
Do we know?

192
00:22:11 --> 00:22:16
This could be a bug, but I'm curious if you know if this is expected.

193
00:22:18 --> 00:22:21
So I do have a little bit of information here, probably not the whole story.

194
00:22:21 --> 00:22:25
I think it's a really good question maybe to bring to the forums coming up.

195
00:22:26 --> 00:22:32
There is a new XCTest API so that from a Mac, you can test VoiceOver on your iOS device.

196
00:22:33 --> 00:22:35
And to the best of my knowledge, it should be working in the simulator.

197
00:22:36 --> 00:22:38
And so that you can check out in Seed 1.

198
00:22:41 --> 00:22:43
That's the 10,000-foot view that I know.

199
00:22:44 --> 00:22:47
The forums are probably the best spot to get some more information and some specifics.

200
00:22:48 --> 00:22:48
Gotcha.

201
00:22:48 --> 00:22:58
- Yeah, well we have an accessibility forum Q&A tomorrow, so it can be brought there, but we will also take note of the feedback report, so thank you for sending that in.

202
00:23:00 --> 00:23:07
Okay, continuing on, there's some questions about voiceover, so I'll try and bring those back up.

203
00:23:07 --> 00:23:09
This one is about macOS.

204
00:23:09 --> 00:23:13
So the question is, we're adding voiceover support to our macOS app.

205
00:23:13 --> 00:23:17
With this year's accessibility updates, is there anything we should revisit?

206
00:23:17 --> 00:23:22
And what's the recommended way to test accessibility across different assistive technologies?

207
00:23:22 --> 00:23:25
I know we talked about the latter a little bit, but maybe specific to the Mac.

208
00:23:25 --> 00:23:26
Any thoughts?

209
00:23:27 --> 00:23:32
I think the Mac, one of the biggest differences from iOS is user expectations.

210
00:23:32 --> 00:23:35
A lot of times on the Mac, users are multitasking.

211
00:23:35 --> 00:23:40
They're doing more things when they're using voiceover, and their expectations are a little bit different.

212
00:23:41 --> 00:23:44
And so the best thing you can do is get some real user feedback.

213
00:23:45 --> 00:23:47
Get some feedback on how users are using it.

214
00:23:47 --> 00:23:54
Get feedback from the field on how your app is used, what would really accelerate somebody in using your app.

215
00:23:54 --> 00:23:56
And those accelerators, I think, are really important.

216
00:23:56 --> 00:24:08
A lot of the things that we do that we hear from users for voiceover functionality on the Mac are more hotkeys, being able to jump to different sections of the app, being able to jump to the inspector quickly, being able to jump to the sidebar really quickly.

217
00:24:08 --> 00:24:25
And so there are things that aren't really voiceover or assistive technology API that you have to implement, But general app features that just really enhance the voiceover experience because of those higher expectations of accelerators, I think is probably maybe the nerdiest term to put it.

218
00:24:26 --> 00:24:31
But we expect there's a lot of hotkeys.

219
00:24:31 --> 00:24:34
There's a lot of really intricate shortcuts on the Mac.

220
00:24:35 --> 00:24:37
So I think that's probably the biggest difference to think about.

221
00:24:37 --> 00:24:37
Gotcha.

222
00:24:37 --> 00:24:39
So I saw you nodding there.

223
00:24:39 --> 00:24:39
Do you have anything to add?

224
00:24:39 --> 00:24:45
He made me really excited when he mentioned the extra keyboard shortcuts because those don't just benefit voiceover users.

225
00:24:45 --> 00:24:49
They also benefit all of the Mac power keyboard users that exist out there, and there are many.

226
00:24:50 --> 00:24:51
Gotcha.

227
00:24:51 --> 00:24:59
But in terms of the question around is there anything different with regards to the new voiceover updates, I mean, you just get them all for free, right?

228
00:24:59 --> 00:25:05
Like there's nothing you just now get image descriptions, which is very exciting.

229
00:25:05 --> 00:25:11
I'll also quickly add, you know, part of the question was, you know, what's a recommended way to test different accessibility features?

230
00:25:11 --> 00:25:19
You know, I'll say the accessibility shortcut is a really powerful way to be able to turn on and off accessibility features without having to go in or out of settings.

231
00:25:19 --> 00:25:25
So don't be afraid of, like, if it's your first time turning on voiceover and you don't know how to get out of settings back to your app.

232
00:25:25 --> 00:25:32
If you add voiceover to your accessibility shortcut, you can, you know, triple click to turn it on and off and it should be pretty easy.

233
00:25:32 --> 00:25:36
It's my favorite, like, development tool and I love to share it with everybody.

234
00:25:37 --> 00:25:40
And on macOS, you can, sorry, you can even just turn it on with command F5.

235
00:25:41 --> 00:25:41
Easy peasy.

236
00:25:41 --> 00:25:43
Excellent.

237
00:25:43 --> 00:25:47
And the accessibility shortcut on macOS, if you have Touch ID, it's triple press on Touch ID.

238
00:25:47 --> 00:25:49
And you don't need to-- it's a little bit different than iOS.

239
00:25:49 --> 00:25:51
On macOS, you don't need to configure it.

240
00:25:51 --> 00:25:53
So out of the box, you can triple press Touch ID.

241
00:25:53 --> 00:25:56
And it'll bring up the accessibility shortcut with all the accessibility options in it.

242
00:25:56 --> 00:25:57
That's cool.

243
00:25:57 --> 00:26:00
If you don't have Touch ID, it's Command-Option-F5.

244
00:26:00 --> 00:26:03
Command-Option-F5, OK.

245
00:26:03 --> 00:26:09
All right, and just on the theme of macOS, I have another-- I have an AppKit question, everyone's favorite topic.

246
00:26:09 --> 00:26:22
For custom AppKit controls with context menus, hover actions, and custom buttons, what's the right way to expose these so that VoiceOver users can actually discover and perform the same actions as mouse users?

247
00:26:22 --> 00:26:24
BRIAN DORSEY: There's quite a few ways that you could do it.

248
00:26:24 --> 00:26:28
Probably the default answer that I'd give is custom actions.

249
00:26:28 --> 00:26:33
Custom actions have a few drawbacks, though, especially on the Mac, because they're not as common.

250
00:26:33 --> 00:26:35
And so users tend to skip them.

251
00:26:35 --> 00:26:41
So they're really, really great for-- again, we were just talking about kind of accelerators or pro features.

252
00:26:41 --> 00:26:46
I think that you might want to think about some different solutions if they're really important, hover actions are really important things.

253
00:26:47 --> 00:26:51
Some of those might be making additional buttons.

254
00:26:51 --> 00:26:55
One of my favorite APIs in SwiftUI is accessibility representation.

255
00:26:55 --> 00:27:01
And with accessibility representation, you can take a SwiftUI view and say, ignore everything I did in that view.

256
00:27:01 --> 00:27:03
Here's how to expose it to accessibility.

257
00:27:03 --> 00:27:11
You can have one button in SwiftUI, and then in accessibility representation, expose that as four different buttons.

258
00:27:12 --> 00:27:17
So perhaps that's a bit off topic since the question was about AppKit specifically.

259
00:27:18 --> 00:27:19
You can do the same thing in AppKit.

260
00:27:19 --> 00:27:20
It's just a little bit trickier.

261
00:27:20 --> 00:27:24
What you would do is implement accessibility children and then create your own accessibility elements.

262
00:27:25 --> 00:27:30
A few other ones would probably be thinking back to our previous conversation, too, is what are some hot keys?

263
00:27:30 --> 00:27:31
What are some other actions?

264
00:27:31 --> 00:27:39
Because thinking about users that aren't voiceover users too, how is somebody that is a pro user that's not always using their mouse around going to get to these as well?

265
00:27:40 --> 00:27:49
And also we try to think about really carefully what's on hover actions because it can be really hard to define for somebody with a cognitive impairment.

266
00:27:49 --> 00:27:51
You have to really search around the UI.

267
00:27:51 --> 00:27:54
It's not something you can just sit and look at and know how to use.

268
00:27:54 --> 00:28:04
So we try to use them sparingly as well, and only in cases where it might provide a secondary action or secondary enhancement to the app.

269
00:28:05 --> 00:28:07
Do you have anything to add, Drew, or Syl?

270
00:28:08 --> 00:28:10
Just a plus one to the cognitive aspect.

271
00:28:11 --> 00:28:20
No, it's not maybe like the root of the question, but, yeah, considering any UI that's hidden or requires a gesture to reveal, yeah, that tends to be difficult for cognitive accessibility.

272
00:28:20 --> 00:28:22
So, yeah, just plus one.

273
00:28:23 --> 00:28:27
Just to switch gears a little bit, I think this question is really interesting.

274
00:28:27 --> 00:28:30
So I'm going to jump ahead to this one.

275
00:28:30 --> 00:28:41
The question is, how does Apple design accessibility features for people with limited or no use of their hands or arms, including people with limb differences, paralysis, tremors, or temporary injuries?

276
00:28:42 --> 00:28:46
I can start with the product marketing answer.

277
00:28:47 --> 00:28:50
So, well, first of all, we have a ton.

278
00:28:50 --> 00:28:57
So, and I think each, it might be a different solution for every user.

279
00:28:57 --> 00:29:16
That's one of the benefits of our vast array of AT solutions is that we have things like changing reachability, which I like a big phone and have small hands, so I love that feature, which is you can swipe and pull the top of the screen down for easy reachability.

280
00:29:17 --> 00:29:24
We also have a whole bunch of different ways to interact with your device that do not require your hands.

281
00:29:24 --> 00:29:32
So we have voice control, head tracking, eye tracking, sound action, switch control, to name a few.

282
00:29:33 --> 00:29:44
And then if you do touch your device but you maybe want to do it in a different way than what's default, we've got touch accommodations as well as assistive touch.

283
00:29:44 --> 00:29:54
And on the touch accommodations note, we actually have a new setup flow for iOS 27 that streamlines it.

284
00:29:54 --> 00:30:02
So currently in the touch accommodations experience, you go into these different toggles and you kind of set how you want it to react to your touch.

285
00:30:03 --> 00:30:09
And now that there's a calibration activity, so you tap kind of like actually like training eye tracking.

286
00:30:09 --> 00:30:15
So you tap a target, and then the output of that is a recommended flow.

287
00:30:15 --> 00:30:26
And so that one I'm super excited about because I have a family member with Parkinson's disease, and so I'm really excited about getting her set up with touch accommodations.

288
00:30:28 --> 00:30:29
Anything to add?

289
00:30:30 --> 00:30:36
I really love the on-device eye tracking on iOS, being able to use the built-in camera.

290
00:30:36 --> 00:30:45
We work with quite a few users and some of those users use external eye trackers, which are incredibly good.

291
00:30:45 --> 00:30:50
They're incredibly accurate, but they're not always easy to set up.

292
00:30:50 --> 00:30:57
And one of the best pieces of feedback that I heard from one of our users was that finally with the on-device eye tracking, they can entertain themselves on an airplane.

293
00:30:58 --> 00:31:04
And it's something we all kind of take for granted, right, that we don't need a complicated setup or a boom-mounted eye tracker.

294
00:31:04 --> 00:31:15
And so now with some of these built-in features for people with mobility impairments, they didn't need help changing the movie or changing the TV show when they're on an airplane because they could use built-in eye tracking.

295
00:31:15 --> 00:31:17
So I thought that was really, really cool.

296
00:31:18 --> 00:31:24
I think the other thing with all of the features, one thing I've certainly learned over my career is everyone is different.

297
00:31:24 --> 00:31:31
And it sounds obvious when you say it, but I think it's easy to say, like, oh, two people with the same disability will have the same solution.

298
00:31:31 --> 00:31:34
And it's very much the exact opposite.

299
00:31:35 --> 00:31:39
You can have two people with the exact same disability, and they will use vastly different solutions.

300
00:31:40 --> 00:31:48
And I think that's why you see such a large swath of different technologies because we really want to help people to accommodate the device to work for them.

301
00:31:48 --> 00:32:02
So they're not fighting it all the time so that whatever needs they have or how they want to interact with the device or however is most comfortable or however they prefer to do it or might need to do it in a temporary situation like on an airplane, they can set up the device that way.

302
00:32:02 --> 00:32:07
And so we're trying not to make sure we don't box them in to any individual technology.

303
00:32:09 --> 00:32:14
Just as we're talking about kind of different areas of accessibility, there's another question that I think is related.

304
00:32:15 --> 00:32:20
The question is, can you mention some features made with neurodivergent people in mind?

305
00:32:20 --> 00:32:20
Definitely.

306
00:32:20 --> 00:32:22
And I think Drew might have some thoughts on that.

307
00:32:23 --> 00:32:23
Yeah.

308
00:32:24 --> 00:32:27
You know, we have two big features that are specifically for cognitive accessibility.

309
00:32:28 --> 00:32:29
First one is going to be guided access.

310
00:32:29 --> 00:32:51
you know this is helped designed to help keep people in like a singular experience on the device so you open up an app you start guided access this is great if you want to make sure someone has access to something like a their favorite book or tv show or on a facetime call with a relative but without the without worrying about them kind of leaving that context getting to

311
00:32:51 --> 00:33:18
other places of the app or other places of the operating system we also have assistive access which is you know it's a when you uh it's like a whole um different visual language uh compared to like traditional ios where things like we have big icons we have a larger default text size larger touch targets and the idea is that we really simplify the user experience in an app

312
00:33:18 --> 00:33:41
to be kind of like the core controls you know recently we just brought a tv app to assistive access. So you can imagine that it's like a library, you tap on a TV show or movie that you have downloaded, and you get a play pause button and a back button. And it's like, you know, we're still providing all of the great value that Apple TV app will bring, but makes it accessible to

313
00:33:41 --> 00:33:55
people with neurodivergent accessibility needs. And so, you know, this is something that we tried to do for multiple first party apps. And we also have APIs for developers to create their own like fully optimized version of their app for assistive access.

314
00:33:55 --> 00:33:57
- I think we have a session on that from last year, right?

315
00:33:57 --> 00:33:58
- We do, yeah.

316
00:33:58 --> 00:34:02
I think we have a few assistive access ones that will be good to check out.

317
00:34:02 --> 00:34:24
But on top of those two features, there are also other features across iOS that we've found to be helpful and that people in these spaces use, like screen time, speak screen is one of my favorites because speak screen and speak selection, and accessibility reader, 'cause you can have text-to-speech read certain snippets of text, and it does different word

318
00:34:24 --> 00:34:26
highlighting.

319
00:34:26 --> 00:34:28
Yeah, I think we've got a lot here.

320
00:34:28 --> 00:34:32
But if you have other ideas, I think we're always looking to expand this as well.

321
00:34:32 --> 00:34:34
That's awesome.

322
00:34:34 --> 00:34:34
Great.

323
00:34:34 --> 00:34:36
I'm going to kind of go back.

324
00:34:36 --> 00:34:38
We kind of went away from the APIs.

325
00:34:38 --> 00:34:41
I'm going to bring it back to the APIs a little bit.

326
00:34:41 --> 00:34:46
There's a question about actually some of the new API surface available in SwiftUI this year in the 27 releases.

327
00:34:46 --> 00:34:51
The question is, SwiftUI has a new reorderable modifier.

328
00:34:51 --> 00:34:53
How do I make that accessible to VoiceOver?

329
00:34:53 --> 00:34:57
Dragging and dropping is not an idiom in VoiceOver.

330
00:34:57 --> 00:34:59
Any thoughts?

331
00:34:59 --> 00:35:09
I'm not actually sure if this works yet in the beta with VoiceOver, but any thoughts in general with drag and drop interactions?

332
00:35:09 --> 00:35:11
- What platform, I wonder?

333
00:35:11 --> 00:35:15
- The platform is not specified, so we can guess.

334
00:35:15 --> 00:35:18
- Sounds like it might be a great question for the forums.

335
00:35:18 --> 00:35:19
- Great, excellent.

336
00:35:19 --> 00:35:23
- The experience does vary a lot between iOS and macOS.

337
00:35:23 --> 00:35:30
I kind of suspect they might mean macOS, 'cause iOS drag and drop definitely works really well.

338
00:35:30 --> 00:35:37
On macOS, I think there might be some other things that need to land properly for it not to be squirrely sometimes.

339
00:35:37 --> 00:35:39
- Okay, very good.

340
00:35:39 --> 00:35:42
And another question about some of the new releases.

341
00:35:44 --> 00:35:49
The question is, I'm not sure if this is actually yet available, but I'm gonna pitch the question.

342
00:35:49 --> 00:35:54
The question reads, "Would love to learn more "about the new FaceTime video interpreting feature announced at WWDC.

343
00:35:54 --> 00:36:03
I'm curious how third-party apps can integrate, specifically how the interpreting session is initiated and whether there are any entitlements or API restrictions to be aware of.

344
00:36:05 --> 00:36:10
I think, correct me if I'm wrong, but I think that feature is not quite available in the iOS 27 beta.

345
00:36:10 --> 00:36:11
Is that correct?

346
00:36:11 --> 00:36:11
Correct.

347
00:36:12 --> 00:36:12
Great.

348
00:36:13 --> 00:36:13
So stay tuned.

349
00:36:13 --> 00:36:17
More information coming when those APIs are available.

350
00:36:19 --> 00:36:19
Okay.

351
00:36:22 --> 00:36:45
Let me take a look at some of the questions that I am seeing. Here's one from another SwiftUI question. The question is, for custom SwiftUI reusable views like labels, icons, composite wrappers, what is the recommended way to expose stable accessibility identifiers for UI tests and the accessibility inspector without misusing the

352
00:36:45 --> 00:37:06
accessibility label? I think two things that I tend to do in a lot of the projects I work on are for those reusable things like label or button is I make a wrapper around it that requires whoever uses it later after I make it to provide an accessibility identifier and an accessibility label.

353
00:37:07 --> 00:37:10
And then I add checks as well, like don't allow a nil accessibility label.

354
00:37:10 --> 00:37:18
And then that way it kind of enforces anyone that comes after me that's reusing the shared view to add the accessibility information.

355
00:37:18 --> 00:37:38
And in that case, what that means here is every time you use it, someone would have to provide a concrete accessibility label for it, which can really help for testing. And now you have something that's really stable throughout your code, throughout testing. And I'd also encourage you to, especially when you're using symbols and buttons, require somebody making one of those buttons to provide an accessibility label

356
00:37:38 --> 00:38:06
as well, in addition to the identifier. Great. I'm going to, I think I'm not seeing any other big Swift UI one, so I'm going to bring us back a little bit. There's, I think, a really interesting question, and so I think you might have some thoughts on this. At the beginning of an app design project, what process do you recommend for making sure that accessibility is built in from

357
00:38:06 --> 00:38:29
the start instead of bolted on later? I'm curious how that moves from UX research and wireframing into Swift development, testing, and support for different assistive technologies. That is a really great and nuanced question yes I'm sure other folks here are going to have some things to to chime in about as well but I can just say the fact that you're even asking that question is putting

358
00:38:29 --> 00:38:56
you just miles ahead already because a lot of folks don't even know or think about accessibility until the app is already pretty much done and dusted and by that point it is tough because then you're just trying to kind of tape on some accessibility over the top and that experience is never going to be it's never going to be the same as if you had designed from the ground up

359
00:38:56 --> 00:39:20
with accessibility in mind so just thinking about it from day one and it doesn't need to be you know the main focus but just everything you build checking it out a little bit with voiceover and kind of learning about how a user of maybe a couple of the different assistive technologies might use a similar app if you can think of anything that's already been done that is a

360
00:39:20 --> 00:39:42
success that can be a great starting point too and if you have the resources for usability research as you mentioned do it because people are always they're going to have things to say and they're going to have great suggestions for you that you might not even thought of how do you how do you do that in a big team though because i can imagine the larger your team grows

361
00:39:42 --> 00:40:05
the harder it is to kind of like propagate the importance of being focused on accessibility throughout the design process um how do you how do you and maybe i know this is intentionally a tough question so there might not be an answer here but like um how do you kind of like re-inspire everyone to kind of be focused on that throughout the entire process you know something that has

362
00:40:05 --> 00:40:33
really worked well for for me um has been just showing developers what's going on for me as a user so you know if if i'm working on a really big project and um i need to i need to convey hey i think we we might really need to give this app some accessibility love here pretty soon before we go much farther sometimes you know just just saying that um it's possible that that that might

363
00:40:33 --> 00:40:56
not be enough but showing them going over and showing hey i'm a voiceover user i'm touching this thing but my phone is just saying literally nothing i can't touch this at all um just bringing the human connection it really shows folks oh my gosh i don't want this person to be blocked out of being able to use my app i want them to use it i mean we're not folks are developing things

364
00:40:56 --> 00:41:07
because they want people to use them and enjoy them and that applies to everybody totally and And I think one of the things I'm hearing you explain there is, like, the importance of iteration, right?

365
00:41:07 --> 00:41:10
Like, all software goes through iterations.

366
00:41:10 --> 00:41:19
And, like, that should be part of the iteration process is making sure the, like, you don't want two weeks of accessibility we'll figure out at the end of the project, right?

367
00:41:19 --> 00:41:21
But in building that into every iteration.

368
00:41:22 --> 00:41:30
I think a few other things that you can think about in a planning process in any company is often you have different categories that you're thinking about.

369
00:41:30 --> 00:41:31
You're thinking about your main app.

370
00:41:31 --> 00:41:33
You're thinking about, well, what about the security implications?

371
00:41:33 --> 00:41:35
What about the privacy implications?

372
00:41:35 --> 00:41:39
And if you can add accessibility into that list, it can really help as well.

373
00:41:39 --> 00:41:48
And that can elevate it up to everyone involved in the project that, yeah, accessibility is one of the things that we care about in this project amongst lots of other important things.

374
00:41:48 --> 00:41:52
And it's a key thing that's going to be part of the project.

375
00:41:52 --> 00:41:54
It's part of the conversations we go through the design process.

376
00:41:55 --> 00:42:02
Then when you get to engineering, I think going back to, you know, how we talked a little bit earlier about making wrappers.

377
00:42:02 --> 00:42:07
Like how do you force the design process then once it goes into prototyping?

378
00:42:07 --> 00:42:10
How can we force some of those prototypes to have some accessibility in them as well?

379
00:42:11 --> 00:42:17
And then lastly, I think still highlighting absolutely you have to think about it through the whole process.

380
00:42:17 --> 00:42:27
But also you always need to make room at the end as well to really refine it because we all know prototyping moves really quick and design process moves really quick.

381
00:42:28 --> 00:42:35
And you can end up wasting a lot of time if in the beginning you say, oh, I want to now make the voiceover experience perfect and really streamlined.

382
00:42:35 --> 00:42:38
And then the UI changes and then you go back to voiceover.

383
00:42:38 --> 00:42:42
And so you absolutely need to think about it and have some basic things working.

384
00:42:42 --> 00:42:51
But then I think making sure that you have time at the end to say, all right, now we're going to sit down and before, well before our ship date, we're going to really refine that voiceover experience.

385
00:42:51 --> 00:43:03
We're really going to fine tune the dynamic text experience now that all of our views landed, now that everything is solidified a little bit more, we can really make a really great experience for customers.

386
00:43:04 --> 00:43:12
And I think one thing that comes to mind is our colleague Ryan did a tech talk recently about preparing your app for accessibility nutrition labels.

387
00:43:12 --> 00:43:21
And one of the examples he gives is the design of control center and how when you have larger text enabled, the size of the controls actually grows to accommodate the larger text.

388
00:43:22 --> 00:43:27
And I think that's such a great example because that is like, it's kind of a two-way street, right?

389
00:43:27 --> 00:43:34
Like in implementing dynamic type, making sure that works, it also kind of changes the behavior of the UI itself.

390
00:43:34 --> 00:43:36
And so like you have to kind of do that early.

391
00:43:36 --> 00:43:39
That's not something you want to like discover at the end of the project.

392
00:43:39 --> 00:43:40
Just one other thing.

393
00:43:40 --> 00:44:00
I think from, like, a higher-level planning conversation, it's important to, like, as feature development is ongoing and you're weighing different priorities against each other, a lot of times I think people tend to use, like, metrics in a certain way, like utilization, like what number of people would benefit from this.

394
00:44:01 --> 00:44:06
But I think it's also important to think about criticality to users.

395
00:44:07 --> 00:44:20
And so just making sure that the way that you're evaluating and trading different features in this kind of larger conversation isn't just about, like, the bell curve, right?

396
00:44:20 --> 00:44:37
And so understanding, like, what is the – not the number – not necessarily number of people impacted, but, like, how big of a deal is this for users that – just kind of making that part of the open conversation.

397
00:44:38 --> 00:44:38
Totally.

398
00:44:38 --> 00:44:54
Yeah, and I'm going to pivot a little bit back to the technical side a bit, but I have a developer here asking a question about kind of building a product that spans all of our platforms.

399
00:44:54 --> 00:45:02
And so the question is, there are a lot of resources on accessibility for iOS with less resources tailored to some of the other targets.

400
00:45:02 --> 00:45:30
Are there any platform-specific pitfalls that you can recommend to look out for when developing a universal app? So for macOS, iPadOS, watchOS, tvOS, et cetera, that might not be obvious if you're just coming from iOS. I think one of the things, accessibility aside first, is acknowledging to really deliver a great customer experience, you're going to need to test it on

401
00:45:30 --> 00:45:41
each platform, right? If you just make an amazing SwiftUI app, it works on iOS, there's probably going to need to be some things that you're going to tweak to work on iPad. There's probably going to need to be some things that you tweak to really make it a great Mac experience.

402
00:45:42 --> 00:46:01
And so along those lines, accessibility would be very, very similar, that when you're testing voiceover on iOS, you're probably also going to need to quickly fire up voiceover on macOS and test out your application, because there's little nuances, and as we talked about a little bit earlier, there's also different user expectations, where the Mac users might expect

403
00:46:01 --> 00:46:06
more keyboard shortcuts, and the iOS users don't have that much of an expectation.

404
00:46:06 --> 00:46:15
On iOS, hit testing is far, far more important for a voiceover user than it is on macOS, where accessibility children order is much more important.

405
00:46:15 --> 00:46:23
So they're just like SwiftUI in general and cross-platform deployment in general, where it's a huge time saver.

406
00:46:24 --> 00:46:30
It's great to be able to do that, but there are some nuances where you will need to test each platform individually.

407
00:46:31 --> 00:46:38
Yeah, and there's, like, the small nuances of, like, how people navigate the different interfaces.

408
00:46:40 --> 00:46:41
There's also screen size, right?

409
00:46:41 --> 00:46:43
I mean, like, for dynamic type, that makes a big difference.

410
00:46:44 --> 00:46:50
But I'm also thinking about, like, are there specific interactions maybe for them?

411
00:46:50 --> 00:46:56
Like, one of the things I think about is on the watch, you have, like, the digital crown, and that's a whole interaction that you need to consider.

412
00:46:56 --> 00:47:01
Are there other kind of, like, top level, like, this interaction is very different?

413
00:47:01 --> 00:47:05
so pays special attention to it on other platforms that come to mind?

414
00:47:08 --> 00:47:08
A lot.

415
00:47:10 --> 00:47:12
Both an easy question and a hard question somehow.

416
00:47:13 --> 00:47:15
I think the answer is yes, right?

417
00:47:15 --> 00:47:20
On the Mac, you always have a keyboard, and that's one of the things that I think about a lot.

418
00:47:20 --> 00:47:24
You always have the keyboard, and users expect the keyboard to be super, super functional in your application.

419
00:47:24 --> 00:47:29
If you are on tvOS, you have no keyboard, and you have no touchscreen, and you just have the remote and linear navigation.

420
00:47:31 --> 00:47:36
If you're on the phone, then you don't have a keyboard, and you have touch-based navigation for many of our apps.

421
00:47:36 --> 00:47:42
And so a lot of the same principles will hold true, but again, it's coming back to how do you create that great experience for customers.

422
00:47:43 --> 00:47:46
If it works on iOS, it'll probably work on Mac.

423
00:47:47 --> 00:47:55
If it works on Mac, it'll probably work on iOS, but it's not going to be really fine-tuned and provide that really great experience.

424
00:47:55 --> 00:48:01
Again, coming back to just like Swift UI development, you're going to need to do a little bit of tweaking on each platform.

425
00:48:01 --> 00:48:09
So keyboard, touch input, Vision Pro input.

426
00:48:10 --> 00:48:13
I can't think of the name of, I guess that's still touch input maybe.

427
00:48:15 --> 00:48:20
And the mouse and trackpad input as well for different modalities and different users.

428
00:48:20 --> 00:48:23
And then as we talked about earlier as well, different mobility impairments.

429
00:48:23 --> 00:48:33
So if you have somebody who doesn't have arms or only has one arm or can't use their arms, the keyboard is not quite as important to them and they're going to use something like eye tracking.

430
00:48:33 --> 00:48:35
And so their experience is also going to be different.

431
00:48:35 --> 00:48:40
And so then depending on the disability, you also now have more limitations or different hardware that may or may not be available.

432
00:48:42 --> 00:48:48
Yeah, and so I'm kind of interested in your perspective on this because I know you do a lot of testing in your day-to-day.

433
00:48:48 --> 00:49:01
Like are there certain – we've said the word expectations a lot, but are there certain expectations that are specific to the platforms that you specifically look out for that might be worth calling out?

434
00:49:01 --> 00:49:03
I think Greg landed on most of them.

435
00:49:03 --> 00:49:14
I guess the only other thing that comes to mind for me is all of the grouping that is possible on macOS that can really, really make keyboard navigation much more efficient.

436
00:49:15 --> 00:49:18
Just determining on kind of which elements you're grouping together.

437
00:49:20 --> 00:49:46
Even kind of going back to our conversation earlier about when we were talking about what one should do with a bunch of buttons, for example, like actions or something like that on Mac, You might even consider grouping them together so that when a voiceover user moves through, they land on each main item, like each card, so to speak, maybe, and then they can then interact in and find what's in there.

438
00:49:46 --> 00:49:53
So it makes it a lot faster for them to navigate, and that is not something that's used on iOS as much, but that's the big thing that comes to mind.

439
00:49:53 --> 00:50:02
Yeah, that's a really great point that I omitted, that navigation with VoiceOver on the Mac is very hierarchical, whereas on iOS it's very linear.

440
00:50:03 --> 00:50:05
And so grouping is really important on the Mac.

441
00:50:06 --> 00:50:08
For simple apps, not quite as important.

442
00:50:08 --> 00:50:18
But if you can imagine a complex app like Final Cut Pro that might have hundreds of controls on screen at once, to have to navigate linearly through every single control one at a time can take a really long time.

443
00:50:18 --> 00:50:23
And so grouping them can be a make or break thing for a voiceover user.

444
00:50:23 --> 00:50:28
And again, that kind of like, is it usable if there's no groups and everything's just linear?

445
00:50:28 --> 00:50:31
Yeah, it's usable, but it's a really poor experience.

446
00:50:32 --> 00:50:35
And the groups then also will benefit iOS.

447
00:50:35 --> 00:50:40
iOS has commands to jump to the next group as well through the rotor.

448
00:50:41 --> 00:50:46
And so all of these things, again, will help all platforms, especially when you're developing cross-platform with SwiftUI.

449
00:50:47 --> 00:50:54
Yeah, and I think we had a great session, not this year, but either last year or the previous year, on improving Mac accessibility.

450
00:50:54 --> 00:50:57
And there's an example of kind of an inspector pane.

451
00:50:57 --> 00:51:06
And you can kind of see, like, you know, by default without any grouping, just how long it takes to navigate through that inspector through all the different selectable items there.

452
00:51:06 --> 00:51:10
So it's a great session if you're interested in improving your Mac apps accessibility.

453
00:51:11 --> 00:51:13
Yeah, it's a challenging problem, too, because you can have too many groups.

454
00:51:14 --> 00:51:38
you're you know thinking about a hierarchy you don't want to be too deep or too wide you need to find that right goldilocks balance in between for users where it's not overly confusing in either either direction yeah totally um a little bit of a different question um maybe this one's for drew um the question is uh with these new improved ai voices will any of them be available for voiceover

455
00:51:38 --> 00:52:03
uh yeah i mean so i can have still help me out here too but um you know the voices that are being used on voiceover we specifically tailor them and tune them to be used for screen readers and so uh you know still can probably attest you know when people are using voiceover uh they tend to have the speech turned up pretty fast what would you say generally yeah um great

456
00:52:03 --> 00:52:30
um and let me just jump around here a little bit um so here's an interesting one um i don't think I've gotten this question before so I'm very interested in your thoughts on this. The question is besides manual testing how do you prevent accessibility regressions? What about approaches like accessibility snapshot testing or automated UI testing with accessibility assertions?

457
00:52:33 --> 00:52:45
I think the easiest answer to that is automated testing because XC test and specifically XC UI test relies on the accessibility hierarchy across Apple's ecosystem on all platforms.

458
00:52:46 --> 00:52:55
And so if your app is not accessible, your XC tests are going to fail. I think in addition to that, for your XC test, you can also add things like checking the accessibility label.

459
00:52:55 --> 00:52:59
You could even add a test to say, make sure there are accessibility labels of UI.

460
00:53:00 --> 00:53:08
So I think the core is just that automated testing gets you 90% of the way there. Outside of that...

461
00:53:08 --> 00:53:11
And is there a new testing API for VoiceOver this year?

462
00:53:12 --> 00:53:13
I think there's something.

463
00:53:13 --> 00:53:14
There is.

464
00:53:14 --> 00:53:18
We talked briefly about this earlier where there is a new API.

465
00:53:18 --> 00:53:20
I don't know it off the top of my head.

466
00:53:20 --> 00:53:24
In order to actually start up VoiceOver and navigate VoiceOver element to element to element.

467
00:53:24 --> 00:53:29
And what you can then do is validate that VoiceOver is speaking the right thing and getting to the controls.

468
00:53:29 --> 00:53:37
And so depending on your app, you could probably also use that as a smoke test to say, can VoiceOver get to nine elements on this screen?

469
00:53:37 --> 00:53:41
Does VoiceOver get to any element where it doesn't speak anything, things like that?

470
00:53:43 --> 00:53:46
I see a question here that's getting upvoted.

471
00:53:46 --> 00:53:49
The question is, I'm a blind iOS developer.

472
00:53:50 --> 00:53:55
Are there any accessibility improvements in Xcode and developer tools in general this year?

473
00:53:56 --> 00:53:57
Oh, I can say something.

474
00:53:57 --> 00:53:58
Yeah.

475
00:53:58 --> 00:54:00
Okay, so we actually did work on that.

476
00:54:00 --> 00:54:01
I'm super happy about this.

477
00:54:02 --> 00:54:07
We improved terminal accessibility in some really cool ways this year.

478
00:54:07 --> 00:54:29
So, VoiceOver can now, it's much better, you can move to visible beginning, you can access marks better now, you can, it now better reads tab autocomplete suggestions, and there's a handful more that I'm sure I'm neglecting, but terminal accessibility, that is something that we are working on.

479
00:54:29 --> 00:54:33
If you have more feedback to share about that, please do so.

480
00:54:33 --> 00:54:37
And then we are working on possibly some things for Xcode.

481
00:54:38 --> 00:54:39
More to come with that.

482
00:54:39 --> 00:54:39
Please keep an eye out.

483
00:54:41 --> 00:54:42
Do you have anything to add, Greg?

484
00:54:42 --> 00:54:48
Yeah, there's lots of great bug fixes in Xcode right now for voiceover that you'll be able to see in seed one.

485
00:54:49 --> 00:55:02
In addition, it might be staying the obvious, I think SwiftUI is a real game changer for voiceover users that are also developers because gone are the days where you had to use the WYSIWYG interface tool to build your UI.

486
00:55:03 --> 00:55:05
Now it's really a level playing field.

487
00:55:05 --> 00:55:08
Everyone is defining and editing UI in code.

488
00:55:08 --> 00:55:19
And so I think that's a huge kind of even overlooked benefit of doing SwiftUI now, that somebody who's blind is editing and doing the same thing as all of their peers.

489
00:55:20 --> 00:55:23
And in addition to that, there are a bunch of rotors.

490
00:55:23 --> 00:55:31
I don't think any are new, but there's a lot of rotors for Mac voiceover in Xcode that allows you to quickly jump between different methods.

491
00:55:31 --> 00:55:34
You can even jump between errors and warnings in the same file.

492
00:55:35 --> 00:55:38
And it jumped between variable names, all kinds of things.

493
00:55:39 --> 00:55:42
So it's a pretty robust solution for voiceover.

494
00:55:42 --> 00:55:48
But I think there's still a bit of work that we'd love to hear from customers on what we could improve and how we could make that better.

495
00:55:48 --> 00:55:50
AI also, I think, is going to be another game changer.

496
00:55:50 --> 00:55:53
I think we're all still learning a little bit about how it's going to change our industry.

497
00:55:55 --> 00:56:04
But you can imagine now a lot of what might make a sighted programmer more efficient is their ability to quickly scan code.

498
00:56:05 --> 00:56:16
And now with AI tools, its ability to scan and summarize code is really a leveling the playing field for somebody who might struggle with that either because of vision or because of a cognitive impairment.

499
00:56:17 --> 00:56:24
And so I think we're also going to see more opportunities arise because of AI and what you can do with that.

500
00:56:24 --> 00:56:29
And so we're really looking at ensuring that the AI experience is really rock solid for voiceover and Xcode.

501
00:56:30 --> 00:56:41
In addition to not specifically this question, but to plug some of the work that we did, there's some really awesome new AI skills in Xcode to help make your application accessible for both voiceover and dynamic text.

502
00:56:41 --> 00:56:44
And how does that work with the coding assistant?

503
00:56:44 --> 00:56:47
Do you just ask, hey, help me improve my voiceover experience?

504
00:56:48 --> 00:56:48
Yep.

505
00:56:48 --> 00:56:53
In SeedOne, there's two skills, one for voiceover, one for dynamic text.

506
00:56:53 --> 00:56:57
And so you can say things like, make this view accessible for voiceover.

507
00:56:57 --> 00:56:58
Make my app accessible for voiceover.

508
00:56:59 --> 00:57:02
what's wrong with my file with regard to voiceover.

509
00:57:02 --> 00:57:06
And it'll help edit your code, it'll provide you suggestions, it'll tell you what's wrong.

510
00:57:06 --> 00:57:17
So it's really a culmination of a lot of the internal knowledge from Apple engineers on how we make apps accessible boiled down into a quick skill that can really, really help developers.

511
00:57:17 --> 00:57:18
That's great.

512
00:57:18 --> 00:57:21
I'm going to go try that out.

513
00:57:21 --> 00:57:46
And looking at the clock, I know we don't have a ton more time, but I thought I'll pick one more question because it's about a platform that we haven't talked about so far, which is VisionOS. And so the question is, for spatial computing tools that use eye tracking or gaze, such as VisionOS and eye-based interaction, how do designers avoid assuming that all users can rely on visual gaze as their

514
00:57:46 --> 00:58:08
main input method? Yeah, we have different accessibility features on VisionOS, kind of specifically to, to not answer the question, but like to, to, you know, be, I don't know, be appropriate here. Uh, one of them is called pointer control. And so instead of using like gaze-based interaction, you know, or hands, uh, you can use different parts of your body to be

515
00:58:08 --> 00:58:12
almost like your pointer instead of your eyes. So we have one that's based on your head.

516
00:58:12 --> 00:58:34
We have one that's based on your finger. You can use like kind of like your, your wrist as an anchor point as well um and uh you know not to plug my own wwdc session uh but me and my teammate dan worked on one uh when we shipped vision os and we talk we we we it's it is very very comprehensive if you haven't seen it you totally should check it out um and we we cover some of

517
00:58:34 --> 00:58:55
this specifically it'd be good to talk about yeah and i think um uh there was a question that i don't think we got to but there was a question about um uh how do you make like reality content reality kit content accessible and that's actually covered in that session we have an api for that yeah definitely awesome um uh there's one more that i just want to shout out here uh not so much a

518
00:58:55 --> 00:59:18
question but i want to share it with the team it reads um just would like to thank the whole team so much and point out that assistive technology is for everyone i myself without any disability so to speak use voiceover and dictation all the time and love it so thank you so much um all right Well, I think that's about all the time we have for today's group lab.

519
00:59:18 --> 00:59:24
I'm so thankful that you were able to join us today, that all of the folks listening today were able to join us.

520
00:59:24 --> 00:59:26
I hope you found today's conversation helpful.

521
00:59:27 --> 00:59:31
And thank you to all the folks working behind the scenes to make today happen.

522
00:59:31 --> 00:59:42
If we didn't get to your questions, please visit the developer forums at developer.apple.com slash forums, including the accessibility and inclusion Q&A taking place tomorrow.

523
00:59:42 --> 00:59:45
That's Wednesday at 10 a.m. Pacific.

524
00:59:46 --> 00:59:52
If you have a bug or an API request for us, please send those our way at feedbackassistant.apple.com.

525
00:59:53 --> 01:00:06
And to make a request for an enhancement or share your story using the accessibility features of Apple products or offer other accessibility feedback, you can email us at accessibility at apple.com.

526
01:00:07 --> 01:00:17
And try out the new AI search experience at developer.apple.com to get answers about frameworks, design, accounts, and everything related to development on Apple platforms.

527
01:00:18 --> 01:00:24
And speaking of feedback, you'll receive an email with a survey link to let us know about your experience at WWDC.

528
01:00:24 --> 01:00:28
And we would love to incorporate your feedback into future events.

529
01:00:28 --> 01:00:31
Thanks again for joining us and have a great WWDC.
