---
title: Safari and Web Technologies Group Lab
source: https://developer.apple.com/videos/play/wwdc2026/8015/
session: 8015
collection: wwdc2026
duration: 01:04:17
fetched: 2026-06-12
via: whisper.cpp ggml-large-v3 (session SD video audio)
transcription: machine-generated, no speaker diarization
---

> Machine transcription of the **Safari and Web Technologies Group Lab** SD video via whisper.cpp (`ggml-large-v3`).
> Group labs ship no Apple transcript; this is generated from the audio. Cues are timestamped segments, **not** speaker-attributed turns.

## Transcript

1
00:00:02 --> 00:00:06
Welcome to the Safari and Web Technologies Group Lab.

2
00:00:06 --> 00:00:17
My name is Saranya Parikh, and I'm part of the WebKit team here at Apple, and I'm joined by Tim Nguyen, Alexey Marchenko, Kiara Rose, Brandel Zatranak, and Jen Simmons.

3
00:00:18 --> 00:00:25
In addition to those on screen, there is a whole team behind the scenes helping with the triage of all of your inbound questions.

4
00:00:26 --> 00:00:31
We're excited to hear your questions today about Safari, about WebKit, and about web technologies.

5
00:00:31 --> 00:00:45
If you have code-specific questions or we don't get to your question today, we'd really encourage you to bring your questions to the developer forums at developer.apple.com slash forums, where we will continue the conversation.

6
00:00:46 --> 00:00:55
If you have a bug or a feature request, we'd encourage you to go to bugs.webkit.org as we want to focus our questions on what will help the broader audience.

7
00:00:55 --> 00:00:56
And with that, let's get started.

8
00:00:56 --> 00:01:05
We're going to begin with a short preview of what's new in WebKit for Safari 27 by Jen Simmons, one of our web evangelists.

9
00:01:05 --> 00:01:06
Jen, take it away.

10
00:01:07 --> 00:01:07
Thanks.

11
00:01:09 --> 00:01:20
So we shipped a lot of new web technology in WebKit already this year, and there's even more coming in Safari 27.

12
00:01:21 --> 00:01:25
Grid lanes shipped in Safari 26.4.

13
00:01:25 --> 00:01:31
You can use it to create masonry-style layouts with really simple CSS.

14
00:01:32 --> 00:01:43
Check out this website we launched, the Field Guide to Gridlanes, at gridlanes.webkit.org, to experience what's possible with gridlanes.

15
00:01:43 --> 00:01:50
And watch Learn CSS Gridlanes, where Brandon will walk you through exactly how to use it.

16
00:01:51 --> 00:01:55
Customizable Select is coming this fall in Safari 27.

17
00:01:55 --> 00:02:26
it lets you fully style the html select element to match your design and it lets you add custom content with all the accessibility and robustness of html forms tim teaches you how in his session rediscover the html select element in safari 27 the model element comes to ios ipad os and mac os Use it to add a 3D model to your website.

18
00:02:27 --> 00:02:34
It works just like the other HTML media elements, and you can manipulate it using JavaScript.

19
00:02:35 --> 00:02:40
Alexei will show you how and get started with the HTML model element.

20
00:02:41 --> 00:02:45
Immersive website environments come to VisionOS 27.

21
00:02:47 --> 00:02:51
Use the web to provide an immersive environment to users.

22
00:02:51 --> 00:03:00
Maybe you make something beautiful or something practical, like a place for people to preview tickets when they're buying a seat.

23
00:03:01 --> 00:03:06
John will show you how in Explore Immersive Website Environments in VisionOS.

24
00:03:07 --> 00:03:09
And then web extensions.

25
00:03:10 --> 00:03:26
In Create Web Extensions for Safari, Kiara will walk you through the entire process of building a web extension and show you how to distribute your extension to Safari users without Xcode or even without a Mac.

26
00:03:26 --> 00:03:37
But really, the biggest news for WebKit this year, it's not any particularly exciting specific one feature.

27
00:03:37 --> 00:03:41
It's our deliberate effort to improve the quality of the browser engine.

28
00:03:42 --> 00:03:47
We tackled over 1,100 feature improvements and fixes since last fall.

29
00:03:48 --> 00:03:53
Learn all about the quality efforts watching What's New in WebKit for Safari 27.

30
00:03:54 --> 00:03:59
We'd love to have you test your projects in the latest versions.

31
00:04:00 --> 00:04:06
And if you are having problems, file an issue at bugs.webkit.org or in Feedback Assistant.

32
00:04:06 --> 00:04:16
Check out our website at webkit.org where we have all sorts of useful content that we hope will help you make the projects you're making.

33
00:04:16 --> 00:04:21
There, you can learn everything about what's in WebKit for Safari 27.

34
00:04:22 --> 00:04:26
And we're thrilled to be here today to talk about it all.

35
00:04:28 --> 00:04:30
Wonderful. Thank you so much, Jen.

36
00:04:30 --> 00:04:33
Now let's dig into your questions.

37
00:04:33 --> 00:04:36
Okay, let's start with a good one.

38
00:04:39 --> 00:04:39
Okay.

39
00:04:43 --> 00:04:51
What web technologies or APIs do you think every new web developer should learn today to prepare for the future of the web?

40
00:04:51 --> 00:04:53
Very timely. I like this question.

41
00:04:54 --> 00:05:00
Yeah, I think in many ways, even though so much is changing right now, the answer to this question is kind of the same.

42
00:05:01 --> 00:05:07
It really helps to understand why there's HTML, CSS, and JavaScript, why there are three programming languages for the web.

43
00:05:08 --> 00:05:14
I think it really helps to learn what you can do with semantic HTML, which is expanding.

44
00:05:14 --> 00:05:21
There's so much happening, things that you used to have to use JavaScript for that now you can do declaratively in HTML alone.

45
00:05:22 --> 00:05:24
So I think investing that time into learning HTML.

46
00:05:25 --> 00:05:28
CSS to really understand how do you do CSS layouts?

47
00:05:28 --> 00:05:30
How do you make your website work on every screen?

48
00:05:30 --> 00:05:31
Progressive enhancement.

49
00:05:32 --> 00:05:37
Like how do you make sure your website works in every browser that your user might be using?

50
00:05:38 --> 00:05:45
You know, both the different browsers also on the different operating systems also back in time ways because people, it takes time to update.

51
00:05:45 --> 00:06:05
So those core skills, like even as folks, more and more people might be turning to large language models or using AI tools, it feels like those fundamentals are the things that will like help you know whether or not the code that's getting generated by a tool is good code.

52
00:06:06 --> 00:06:12
Like you kind of really like and those investments into learning that stuff will last you your entire career.

53
00:06:12 --> 00:06:14
I agree.

54
00:06:14 --> 00:06:33
I also think that there are a lot of things that if you've been in web development for a long time, we've had to create some workarounds, things like CSS, preprocessors, and way back to things like jQuery, where the web standards themselves have kind of come along and actually simplified and cleaned those things up.

55
00:06:33 --> 00:06:35
It's worth understanding the latest and greatest.

56
00:06:36 --> 00:06:43
If you are beholden to working on a large existing code base, by all means, you need to know how it is solving those problems.

57
00:06:43 --> 00:06:51
But the reality is that all of those platforms, the HTML, the CSS, and the JavaScript, are all getting better at meeting those needs directly.

58
00:06:52 --> 00:06:59
And so looking at the state of the art today is an important part of being able to make sure that you're producing and shipping the simplest code possible.

59
00:07:00 --> 00:07:08
Yeah, and to add on top of that, I think like these days, understanding 3D and spatial web is also important.

60
00:07:08 --> 00:07:13
For the last couple of decades, web used to be pretty much like 2D.

61
00:07:14 --> 00:07:27
But now we have things like model element and other things and technologies like WebXR that allow you to interact with 3D spaces and bring 3D content into the web.

62
00:07:28 --> 00:07:34
So start learning about this, educating yourself about these things, also important and useful.

63
00:07:34 --> 00:07:50
I want to mention accessibility, and I think learning the basics, HTML, CSS, and JavaScript, can really help also understanding accessibility.

64
00:07:51 --> 00:08:00
Because when you start using semantic HTML instead of custom JavaScript, that by itself helps accessibility already.

65
00:08:02 --> 00:08:10
And so I want to go back to what the two first panelists said about how learning the basics is really important.

66
00:08:11 --> 00:08:17
Yeah, and John, who's in the back, he was saying earlier, like, just make it easy.

67
00:08:17 --> 00:08:19
Like, always choose the easier path.

68
00:08:19 --> 00:08:20
Make it easy for yourself.

69
00:08:20 --> 00:08:25
And I think doing things like using a semantic HTML element, you get accessibility for free.

70
00:08:25 --> 00:08:28
You get, you know, robustness and progressive enhancement for free.

71
00:08:28 --> 00:08:43
And sometimes there were reasons that people couldn't in the past, but there's been so much work done recently, especially in the last five years, to try to be like, you don't have to use a pile of JavaScript with spans and divs now.

72
00:08:43 --> 00:08:47
You can use this other new – we paved that cow path.

73
00:08:47 --> 00:08:48
So it's easier.

74
00:08:48 --> 00:08:50
It's easier for you as a web developer.

75
00:08:51 --> 00:08:52
Be lazy.

76
00:08:53 --> 00:08:54
Be lazy.

77
00:08:54 --> 00:08:55
Lesson of the group lab.

78
00:08:55 --> 00:08:56
Be lazy.

79
00:08:56 --> 00:08:56
I love that.

80
00:08:57 --> 00:09:04
Kara, I'm curious, where do web extensions fall into the things I should learn about and be good at for the web for the future?

81
00:09:04 --> 00:09:05
Where does that fall into place?

82
00:09:05 --> 00:09:10
Well, yeah, so as mentioned, web extensions use JavaScript, CSS, HTML.

83
00:09:10 --> 00:09:17
So if you're familiar with those technologies and you haven't considered using a web extension or creating one, you can.

84
00:09:17 --> 00:09:27
And it could be a really good tool to supercharge your browsing experience or provide useful tools for users who use Safari or other browsers.

85
00:09:27 --> 00:09:52
so yeah you can use those tools for web extensions as well and with it being made up of those main three languages you can port over extensions that you might have in other browsers to Safari we've created numerous developer tools to help developers who've created extensions for other browsers to have support for them in Safari so yeah and we've done a lot of work in the web

86
00:09:52 --> 00:10:14
extensions community group to help standardize web extension apis to make the development process smoother for developers across browsers so yeah we're excited about the work that we're doing there awesome that is exciting and speaking of standards my next question is about web standards what upcoming web standards is the team most excited about i'm really excited about new

87
00:10:14 --> 00:10:22
install element declarative partial updates and html and canvas and this is from dfa BULIC, I believe is how you say that.

88
00:10:22 --> 00:10:26
Well, I'm really excited about the Web Extension's working group that was just founded.

89
00:10:27 --> 00:10:51
We've been involved in the community group for the past five years, meeting bi-weekly to discuss issues within the Web Extension space, helping specify APIs and clarify things, and now we can do so in a more concrete manner and actually have a spec for the Web Extension's API and push towards interoperability across the browsers.

90
00:10:52 --> 00:10:53
So I'm really excited about that.

91
00:10:53 --> 00:10:54
Nice.

92
00:10:55 --> 00:10:57
I want to encourage everyone to watch our session.

93
00:10:59 --> 00:11:07
But on a personal level, I'm very excited about the random item function that's in the new CSS value spec.

94
00:11:08 --> 00:11:26
So it's similar to the random function that shipped recently in Safari, but instead of being restricted to just numbers and lengths, you can actually pick between different values for keywords for different CSS properties.

95
00:11:26 --> 00:11:29
And I think that opens up a lot of possibilities.

96
00:11:29 --> 00:11:40
And the other one that I'm excited to see coming is spatial CSS that some of my colleagues have been working on.

97
00:11:40 --> 00:11:42
I actually wanted to mention that.

98
00:11:42 --> 00:11:51
Spatial CSS is one of the most exciting things I've been working on as a developer and see my team and my colleagues contributing as a standard and as a SPAC.

99
00:11:52 --> 00:11:57
And it's an amazing technology that will allow us to bring more compelling content to the web.

100
00:11:58 --> 00:12:00
Can you say more? What kind of cool stuff will you be able to do with it?

101
00:12:00 --> 00:12:10
I mean, we already can see from the SPAC draft that we can do, like, transforms on multiple objects, like 3D objects.

102
00:12:11 --> 00:12:13
In 3D space, like real 3D space.

103
00:12:13 --> 00:12:14
In 3D space.

104
00:12:14 --> 00:12:21
Yes, in 3D space on platforms that are going to be able to support it, like Apple Vision Pro and other platforms that are like it.

105
00:12:21 --> 00:12:28
But it also has relevance for the objects that you display on a phone or on a Mac or a laptop.

106
00:12:29 --> 00:12:35
Almost everybody in an e-commerce context today shows their product in some kind of 3D configurator.

107
00:12:35 --> 00:12:52
And having more people be able to reach for a system that allows them not just to display that object, which model element achieves for folks in VisionOS, MacOS, and iOS 27 today, but also through JavaScript polyfills for other platforms.

108
00:12:53 --> 00:13:06
But looking forward to spatial CSS, you'll be able to combine those, annotate them, work with the sort of now excellent accessibility trees and solutions that have been sort of following along in the long run of the web.

109
00:13:07 --> 00:13:12
We have added many new platforms to the web over the last three and a half decades.

110
00:13:13 --> 00:13:18
And it takes a process of kind of figuring out what those platforms need.

111
00:13:18 --> 00:13:25
Spatial CSS, I think, is the very beginning of thinking about what we need to do to move into this new dimension.

112
00:13:25 --> 00:13:28
And I think it's going to be awesome.

113
00:13:29 --> 00:13:56
What makes me really excited about Spatial CSS is if you know the basics, like absolute positioning, anchor positioning uh it just extends naturally from that using like if you know top left right bottom those properties um there's now like depth uh like it's um it's it very naturally uh like expands from what you already know um that's from 2d to 3d and anchoring is actually one of the

114
00:13:56 --> 00:14:15
really exciting things like when you can combine like multiple models into a single object that can be customized i think there's going to be a huge hit at e-commerce at least it has everything for e-commerce to be i it's interesting when you ask the question about web standards and what's most exciting i was sort of guessing it's like oh you know a third of this panel is from

115
00:14:15 --> 00:14:39
the spatial web and and like i think that answer is probably you know there there are a whole bunch of people here right now and a lot more in in our team buildings and it's like gosh that the answer to that question would be so personal to all the different teams so personal because there's so many different groups of experts that I honestly like really honestly feel very lucky to

116
00:14:39 --> 00:14:56
be working with and I can I just they were their faces were flashing through my head and I was like well these people would say this and this guy would say this and this person would say that and like it's kind of I don't know there's a lot of different web standards happening and they're all interesting and exciting and hard I mean I don't want to romanticize the web standards process

117
00:14:56 --> 00:15:24
it's a hard it's a hard process it can end up feeling like it's taking years longer than you want it to be taking but at the same time like it goes slow on purpose in a way because if it's hard to change it's hard to change back and these things these decisions like oh maybe it took like three years longer to ship grid lanes than we wanted it to but also will anyone care 40 years

118
00:15:24 --> 00:15:33
from now right no they will be very happy that it was the best designed api it could be and so i I feel like we feel a lot of responsibility at Apple.

119
00:15:33 --> 00:15:38
Like, we're willing to go slow because we want the end result to be really, really good.

120
00:15:38 --> 00:15:44
We don't like going slow, but we're willing to go slow if that's what it takes to end up with a result that's excellent.

121
00:15:44 --> 00:15:50
Because on the web, we're not just going to throw away HTML5 and, like, replace it with HTML17.

122
00:15:51 --> 00:15:54
Like, HTML is HTML is HTML.

123
00:15:54 --> 00:15:59
So, yeah, anyway, we could talk about standards forever because we do talk about it so often.

124
00:15:59 --> 00:16:03
But it's a core part of what I think we care about in the work that we're doing here.

125
00:16:04 --> 00:16:05
I see that throughout the whole team.

126
00:16:06 --> 00:16:08
Next question is from Josh D.

127
00:16:09 --> 00:16:12
I'm a native app developer on macOS iOS.

128
00:16:12 --> 00:16:22
I've been using agentic coding to play with some JavaScript-driven apps and have noticed that Safari can sometimes be more performant than native in terms of UI UX.

129
00:16:22 --> 00:16:24
Can you explain how this is possible?

130
00:16:24 --> 00:16:37
A long time ago, at the very beginning of iOS, there was a plan that we didn't follow through with not to ship apps on iOS at all.

131
00:16:37 --> 00:16:43
It was an idea that people could build everything they needed as web apps.

132
00:16:44 --> 00:16:54
That turned out, and in fact, it's in a book that was published by one of the early developers inside Apple, that the Stocks app and a number of other apps were built that way.

133
00:16:55 --> 00:17:02
But ultimately, they realized that while you can build performant web apps, it is hard.

134
00:17:03 --> 00:17:14
But that means that when web apps are performant, they are every bit as good and potentially even better than native apps, depending on what people are doing and how they're working with it.

135
00:17:14 --> 00:17:24
WebKit is doing a lot of work under the hood to optimize a lot of the different things that end up happening on the web in terms of things like a lot of content and refreshes and things like that.

136
00:17:24 --> 00:17:34
So, depending on what you're doing, there may be extra help that is being given to you from WebKit and other browser engines.

137
00:17:35 --> 00:17:47
You know, one thing I think that I did not understand as a web developer, and I imagine a lot of people don't, is just how deeply integrated WebKit is into the operating systems of all of our platforms.

138
00:17:47 --> 00:17:56
That JavaScript core, which is what runs JavaScript on a website, is a big chunk of the operating system.

139
00:17:56 --> 00:17:57
Like it's built into the app.

140
00:17:57 --> 00:18:03
And if any other app anywhere doing anything is using JavaScript, it's also using JavaScript core.

141
00:18:04 --> 00:18:14
And WebKit is now a view in SwiftUI where people could just open up SwiftUI, be coding an app using all the best modern practices for app development.

142
00:18:14 --> 00:18:17
And realize, oh, you know what?

143
00:18:17 --> 00:18:19
I think actually here I'd like to use web technology for this.

144
00:18:19 --> 00:18:21
And then they just bring it up, and they're writing web technology.

145
00:18:21 --> 00:18:22
So it's interesting.

146
00:18:22 --> 00:18:29
Sometimes I feel like there's this sort of, I don't know, like sports game, this or this.

147
00:18:29 --> 00:18:34
There's two teams, and they're very clear which is which, and only one can win, and they're up against each other.

148
00:18:34 --> 00:18:36
And it's like that's not actually what's happening.

149
00:18:37 --> 00:18:41
People are using whatever tool they want to make whatever work they want.

150
00:18:41 --> 00:18:44
And users, our customers, they don't know.

151
00:18:45 --> 00:18:45
They don't care.

152
00:18:46 --> 00:18:51
Like, people just want to do what they want to do, and people who make those experiences are reaching for the tools that they find appropriate.

153
00:18:52 --> 00:19:01
And the truth is that WebKit and JavaScript Core are deeply integrated into every one of our operating systems, even like watchOS.

154
00:19:01 --> 00:19:04
There's no web browser on watchOS, but WebKit is running on watchOS.

155
00:19:04 --> 00:19:05
JavaScript Core is running on watchOS.

156
00:19:05 --> 00:19:09
So performance matters a lot, a little-to-mean device.

157
00:19:10 --> 00:19:15
So I think we all have to take our hats off and say a big thank you to JavaScript Core.

158
00:19:17 --> 00:19:50
exactly all right next question this is from d fabulic uh web devs have very strong opinions about what web features web kit should and shouldn't support how does apple recommend that web devs advocate for these features and what shouldn't we do are web kits standard standards positions factoring in our radars i'll take that um wow i'm reading it again web

159
00:19:50 --> 00:19:54
Web developers have very strong opinions.

160
00:19:54 --> 00:19:54
Yes, they do.

161
00:19:57 --> 00:20:00
I mean, we always love to hear from web developers.

162
00:20:00 --> 00:20:02
I honestly mean that.

163
00:20:02 --> 00:20:05
And bugs.webkit.org is one place.

164
00:20:05 --> 00:20:07
You can find our evangelists on social media.

165
00:20:07 --> 00:20:08
That's another place.

166
00:20:09 --> 00:20:22
And I do think that often what really helps us, because we have so many fantastic ideas, and there's just not enough human beings on Earth to get them all done.

167
00:20:22 --> 00:20:25
So we have to prioritize like any software team.

168
00:20:25 --> 00:20:26
We're prioritizing.

169
00:20:26 --> 00:20:41
So we're taking in a lot of information as we set those priorities and knowing sort of what is the use case and what is the benefit to your users because that's what Apple really cares about is that end user.

170
00:20:42 --> 00:20:55
What is it that you want to create for them and how is it that this API would really help you do that job or how is it that not having that API is making it hard for you or perhaps impossible for you to do what it is you're trying to do.

171
00:20:55 --> 00:20:59
So, I mean, I think that's part of it.

172
00:20:59 --> 00:21:02
I feel like there's another piece of that question, though, that I don't remember at the moment.

173
00:21:04 --> 00:21:06
Yeah, how does Apple recommend that?

174
00:21:06 --> 00:21:07
Oh, standards positions.

175
00:21:08 --> 00:21:19
Like, a lot of people might not know, you know, web standards is a conversation between lots of different people about what might become an official web standard in the future.

176
00:21:20 --> 00:21:27
And so there's a lot of things that get introduced and perhaps even implemented in one browser that are not actually web standards yet.

177
00:21:27 --> 00:21:28
There's no consensus.

178
00:21:28 --> 00:21:49
There's just an idea and perhaps other browser engines have been very vocal that they think the idea could be better, that maybe the use case is a good use case to solve, but that first idea is going too fast and we need it to be more private, more secure, more performative, something that won't hurt people's battery life as much,

179
00:21:49 --> 00:21:51
something that will be easy for web developers to use.

180
00:21:51 --> 00:21:56
There's quite a few web standards positions we have out there where we're like, we don't know about that.

181
00:21:56 --> 00:22:00
And the reason is because we think it's going to be too hard to use.

182
00:22:00 --> 00:22:01
It's going to be too hard to use that API.

183
00:22:01 --> 00:22:06
It needs to be designed better so that when web developers do use it, it works out really well for them.

184
00:22:07 --> 00:22:14
And so if people are curious about standards positions, you can find them on GitHub, the official standards positions that we've published.

185
00:22:15 --> 00:22:20
It gets really in the weeds because it's web standards conversations.

186
00:22:20 --> 00:22:48
I do think that what I'd love for people to understand is that there is true passion and there is true desire to get it right for users first, for developers second, and for ourselves third. Much like the priority of constituencies written into the HTML5 design principles, which by the way, was written by one of our standard engineers and the person who is in charge of all

187
00:22:48 --> 00:23:16
of webkit back in the day so like anna and machi wrote that that principle like that's not we we believe it very deeply so um and sometimes you know there's something that would be great for a project or a developer but we can see some way that it would be dangerous for users if misused and so we're often like gosh i wish we could ship that that would be awesome but

188
00:23:16 --> 00:23:38
these other people would do something really bad with it and so we can't we can't we have to we have to protect users we have to make sure that if you ever click a link anyone ever clicks a link that what happens after that link is clicked is safe that's one of our top priorities so and privacy that like people's data is not being sucked into you know something that's not good

189
00:23:38 --> 00:24:03
for those people so that's that's what a lot often our web centers positions are about i would also say that there's a difference between user needs and standards positions and even if apple has or webkit has a standards position that should never prevent uh developers and users from enunciating and describing their actual needs because the web is such a vast platform at this point there are

190
00:24:03 --> 00:24:20
often ways to disentangle these things or approach them in a slightly different way so a specific feature or a specific APA, maybe something that Apple doesn't feel comfortable getting behind, but we are always responsive to trying to work out what is the best, webbiest way to be able to meet these user needs.

191
00:24:21 --> 00:24:32
And so no matter what we've said or if we haven't said anything, filing those requests is absolutely vital in working out what it is that we actually can do with and for the web in order to meet those needs.

192
00:24:32 --> 00:24:33
Well, and it is an ongoing conversation.

193
00:24:34 --> 00:24:34
Yes.

194
00:24:34 --> 00:24:35
It's never a done deal.

195
00:24:35 --> 00:24:35
No.

196
00:24:35 --> 00:24:37
And things change.

197
00:24:37 --> 00:24:42
I mean, I don't think we had a standards position against a parent selector.

198
00:24:42 --> 00:24:45
But it certainly came up over and over and over and over for years and years and years.

199
00:24:45 --> 00:24:49
And I think it's right to say that Apple was like, yeah, that's never going to happen.

200
00:24:49 --> 00:24:51
A lot of browser engineers were like, that's never going to happen.

201
00:24:52 --> 00:24:58
And then I guess it was Agalia who was like, wait, we really think has can happen.

202
00:24:58 --> 00:25:04
And for some reason, that triggered our engineers to be like, all right, well, look at has again.

203
00:25:05 --> 00:25:08
We've said a billion times it's not going to work.

204
00:25:08 --> 00:25:10
A parent selector is going to be too slow.

205
00:25:10 --> 00:25:11
It's going to slow the browser down.

206
00:25:11 --> 00:25:12
The chips aren't fast enough.

207
00:25:12 --> 00:25:13
We can't do it.

208
00:25:13 --> 00:25:14
The hardware's not fast enough.

209
00:25:14 --> 00:25:21
And then our engineers looked at it again, and I don't remember who it was exactly, but it had a genius stroke of like, but what if we did it this way?

210
00:25:22 --> 00:25:26
And also, hey, the hardware's a lot faster now than it was 20 years ago.

211
00:25:27 --> 00:25:28
And we figured it out.

212
00:25:28 --> 00:25:38
Like, after saying no for so many years, it was the WebKit team at Apple who figured out how to do has as a parent selector in CSS and make it super fast.

213
00:25:38 --> 00:25:40
And that unlocked everything, and now it's shipped in every browser.

214
00:25:41 --> 00:25:43
But, yeah, things change.

215
00:25:43 --> 00:25:44
Very nice.

216
00:25:44 --> 00:25:48
Just to double down on what Brando said, like, be vocal about features you need and you want.

217
00:25:49 --> 00:25:57
Build JavaScript libraries, demos, whatever, share it across the web, on social media, and bring it to people's attention.

218
00:25:57 --> 00:26:22
so others can hear about that and see it and eventually if it's like useful and people want it it will get into a standard and into webkit or safari yeah i think it really helps to know is it just a couple people or is it like a lot of people who want or need something and is it just for one or two use cases or is it for a lot of use cases i also think that being really kind helps and

219
00:26:22 --> 00:26:47
realizing that these are humans and we're having a conversation and we're really just trying to figure out what's best rather than you know other things that's a good point and make demos that represent your actual like use cases that's very important not just like flashy demos that makes the most of the api you're trying to use yeah because we want to know is it realistically

220
00:26:47 --> 00:27:14
something that people are going to be using and it's going to really help our users nice this one is from harsha is there any improvement to cookie synchronization in wk webview similar to the behavior that ui webview used to provide we're still seeing challenges keeping cookies in sync between the app and wk webview so i'm curious if there are any recent enhancements or recommended

221
00:27:14 --> 00:27:30
approaches um so from the backroom um the backroom suggested to use wk htp cookie store to observe and manipulate cookies for WKWebView?

222
00:27:32 --> 00:27:35
I don't think we have experts on cookies here.

223
00:27:36 --> 00:27:37
All right.

224
00:27:37 --> 00:27:38
So this is a good question for the forums then.

225
00:27:39 --> 00:27:39
Yeah, for sure.

226
00:27:40 --> 00:27:41
For the developer forums.

227
00:27:41 --> 00:27:47
Okay, next we have, excited to see we can create extensions from a prompt.

228
00:27:47 --> 00:27:50
This is from Jay Kingens.

229
00:27:50 --> 00:27:55
This motivates more powerful extension-based API support.

230
00:27:55 --> 00:28:04
Are there plans to further close the gap on API support with what Chrome and Firefox offer, for example, tab group APIs?

231
00:28:05 --> 00:28:06
Yes, so a lot mentioned there.

232
00:28:06 --> 00:28:10
So I'm also excited to see that users can create an extension from a prompt.

233
00:28:11 --> 00:28:25
We really wanted to give users the opportunity to see what extensions are in the App Store if they're already there or to get creative with their browsing experience and specify an extension that meets their unique use case.

234
00:28:26 --> 00:28:38
As referring to the different APIs, we encouraged for you guys to reach out to us on bugs.webkit.org, fileabug.

235
00:28:38 --> 00:28:44
As mentioned earlier, it's always helpful to get the use case for that specific API because there's so many APIs.

236
00:28:44 --> 00:28:49
And as Jen was saying, there's not enough time or people or resources to tackle all of them.

237
00:28:50 --> 00:29:03
And so what really comes into consideration is the use case and what benefit it adds to your extension or other extensions that you might see that might benefit from that API and why it'd be useful to have additional support for that.

238
00:29:04 --> 00:29:16
But in addition to that, we are working really hard in the community and working group to standardize those APIs and to get them the same behavior across platforms.

239
00:29:17 --> 00:29:25
So if you see an API that might be available in another browser but not in Safari, be loud about it and let us know why.

240
00:29:25 --> 00:29:30
And we'll work really hard to figure out if that's something that we can get done.

241
00:29:31 --> 00:29:34
This one is from Paul Sheldon.

242
00:29:35 --> 00:29:40
With Spatial CSS, do I see things on my Apple Vision Pro in 3D coming from the web?

243
00:29:41 --> 00:29:45
So Spatial CSS at this point is a proposal.

244
00:29:45 --> 00:29:54
It's not something that we have any specific news about shipping, but given that it is coming from us, it's something that we feel positively about.

245
00:29:55 --> 00:30:17
What we do have at this point with the sort of spatial representation on the web is the inline HTML model element, allowing you to have USD and other 3D file format-based 3D models placed into the web page, As well as being able to drag them out, or now with the immersive environments, being able to see those at full scale.

246
00:30:18 --> 00:30:25
That also allows you to create things procedurally, so you would be able to create things that are like CSS.

247
00:30:25 --> 00:30:27
We're excited for people to play with that.

248
00:30:28 --> 00:30:34
Ultimately, we think that spatial CSS and having the full accessibility tree, all of the other features, is the right way to do that.

249
00:30:34 --> 00:30:40
But nothing today is going to prevent you from playing and experimenting with those things in a way that's going to look a lot like that.

250
00:30:40 --> 00:30:42
And I would like to see it personally.

251
00:30:42 --> 00:30:43
Very nice.

252
00:30:44 --> 00:30:48
This is from Waggy, I think is the name.

253
00:30:48 --> 00:30:57
Do you think we reached the end game for optimizations and performance in the web, or do you think there is still more performance to squeeze out of the browser?

254
00:30:59 --> 00:31:10
Yeah, I don't write browser engine code, so I'm not one of the people working on performance, but there's a lot of people working on performance all the time.

255
00:31:11 --> 00:31:12
And you know what?

256
00:31:12 --> 00:31:19
I think one thing, when I've heard web developers talk a lot about performance, They're often thinking about how do they make their JavaScript run faster?

257
00:31:19 --> 00:31:21
How do they make their images load faster?

258
00:31:21 --> 00:31:26
How do they make sure their website is in front of users as fast as possible to the point where a user can use it?

259
00:31:27 --> 00:31:43
And yes, also, I know my colleagues here, my friends here at Apple are like thinking about how do we make sure that a particular website or a browsing experience doesn't tax people's batteries too much?

260
00:31:43 --> 00:31:45
Because that's what a customer is thinking about.

261
00:31:45 --> 00:31:48
They're thinking about, like, ooh, I haven't gotten home yet.

262
00:31:48 --> 00:31:51
I'm trying to take the train, and my battery is down to 15%.

263
00:31:51 --> 00:31:52
Oh, no.

264
00:31:52 --> 00:31:53
Like, that's the thing.

265
00:31:53 --> 00:31:56
There's lots of ways to define performance, and there's lots and lots.

266
00:31:56 --> 00:32:02
There's always going to be – because we keep making the web more powerful, and then we need to make it more performant.

267
00:32:02 --> 00:32:04
We make it more powerful, we make it more performant.

268
00:32:04 --> 00:32:07
So, yes, I don't think that that's going to end anytime soon.

269
00:32:07 --> 00:32:13
In the context of things like 3D graphics, not just on Vision OS, but everywhere else as well.

270
00:32:13 --> 00:32:17
We started with WebGL back sort of starting in 2010.

271
00:32:18 --> 00:32:32
And after a few years of that, we realized that what was really taking the most time, what was really hard on these chipsets across the entire industry was a little bit different to what looked like was going to be the bottleneck at the outset.

272
00:32:32 --> 00:32:36
And that is why we moved from what WebGL still exists.

273
00:32:36 --> 00:32:49
And WebGPU is the successor to that in terms of making sure that it is most appropriate as a match to the silicon that Apple, but all of the other major vendors are producing as well.

274
00:32:49 --> 00:33:04
So as we discover what people want to do the most of, we also end up with different optimizations, and it can take a while for that circle to close in terms of how we do it and then how we do it as efficiently and as performantly as possible.

275
00:33:04 --> 00:33:14
So unless we end up stopping development on the web at all, I think that we're probably in line for about as much performance improvement there as well.

276
00:33:17 --> 00:33:20
WebKit engineers, they have to deal with benchmarks every day.

277
00:33:20 --> 00:33:22
We can't regress benchmarks.

278
00:33:23 --> 00:33:25
And we're also constantly looking for improvements.

279
00:33:25 --> 00:33:32
And what I found is it's very surprising what can or what can't affect performance.

280
00:33:33 --> 00:33:35
It's often a complete surprise.

281
00:33:36 --> 00:33:40
And so it's hard to say if there's a ceiling that we've reached.

282
00:33:41 --> 00:33:52
And even if there's a ceiling that we've reached, often we create a new version of the benchmark that takes into account the new needs of the web.

283
00:33:52 --> 00:34:02
Websites constantly keep evolving, so there's always new optimizations we need to think about for the evolving needs of the web.

284
00:34:03 --> 00:34:05
Yeah, it's something users care about a lot.

285
00:34:06 --> 00:34:12
As a person who worked on performance for the model element, I can say there is no endgame for performance, never.

286
00:34:12 --> 00:34:15
As Brendel said, only if we stop development completely.

287
00:34:16 --> 00:34:24
At the same time, every change in the code can have positive or negative implications on performance.

288
00:34:24 --> 00:34:47
And big shout-out to WebKit PNP team, Power and Performance, who are actually working on an infrastructure that is running performance tests on loading, on rendering, power for pretty much every commit or at least we try to run it for every commit in order to have data like what is going on this performance on Safari and faster

289
00:34:47 --> 00:35:11
we get the data better we understand like what what is going on if something is going on or if like a performance improvement actually hitting the target mm-hmm I mean we're obsessed which is why we have the fastest browser this is from Dirkio. I would like to create a Safari extension for SEOs that takes what's in Safari

290
00:35:11 --> 00:35:17
and displays reports, guidance in a Swift UI window so users can use it across any platform.

291
00:35:18 --> 00:35:23
Fast app switching on iOS, separate palette next to Safari on macOS and VisionOS.

292
00:35:23 --> 00:35:25
What is a path you'd recommend for this?

293
00:35:25 --> 00:35:27
Good question.

294
00:35:28 --> 00:35:30
There are a couple of things that you can do.

295
00:35:30 --> 00:35:57
You can display some of this information in some extension UI, so you can use an extension pop-up for this in terms of like monitoring different changes within the page there are different extension apis that you can use to leverage that so web navigation apis if you want to get some communication back and forth between your app and the web extension you can use native

296
00:35:57 --> 00:36:06
messaging to get that information and display it in the app and such so different variations that you can leverage there.

297
00:36:06 --> 00:36:08
MELANIE WARRICK: Very nice.

298
00:36:08 --> 00:36:11
All right, let's see.

299
00:36:11 --> 00:36:16
OK, Tim, this is for you.

300
00:36:16 --> 00:36:28
If a developer wants to progressively enhance an existing select, adding appearance-based select without breaking the experience in browsers that don't support it yet, what is the safest approach?

301
00:36:28 --> 00:36:36
TIMOTHY JORDAN: So it's very important to always have textual content in your select.

302
00:36:36 --> 00:36:46
Because with customizable select, And so it can be pretty tempting to remove text and put icon-only interfaces or color-swatches-only interfaces.

303
00:36:47 --> 00:36:59
And if you have text content, then when you use a browser that doesn't support customizable select, that text content is just automatically used.

304
00:36:59 --> 00:37:01
And that's the beauty of progressive enhancement.

305
00:37:02 --> 00:37:07
But for that to happen, it's very important to have text content.

306
00:37:07 --> 00:37:17
Otherwise, in a browser that doesn't support the technology, you'll just see a blank pop-up, which isn't great for users.

307
00:37:17 --> 00:37:32
And not only it's great for progressive enhancement, it's also great for accessibility, because that's what screen readers will read out loud when you navigate to select.

308
00:37:32 --> 00:37:35
And often, it's also good for UX.

309
00:37:35 --> 00:37:38
Sometimes, icons are hard to recognize.

310
00:37:38 --> 00:37:50
And having a text label can help clarify the meaning of the icon that you put in your UI.

311
00:37:50 --> 00:37:53
So always put text.

312
00:37:53 --> 00:37:55
Yeah, so it's pretty simple, right?

313
00:37:55 --> 00:38:03
If you use select in the traditional way and you put all the text options in and then you layer on top of it, oh, also we're going to put these images in here.

314
00:38:03 --> 00:38:04
We're going to put this extra content in here.

315
00:38:04 --> 00:38:06
We're going to style it with all this CSS.

316
00:38:06 --> 00:38:19
And then when a user goes to a browser that and have support, they just get the OG list of text and the browsers we support get all the fanciness and the web developer doesn't have to do anything.

317
00:38:19 --> 00:38:21
They can just be totally lazy.

318
00:38:22 --> 00:38:39
Yeah, I think that that's one of the things that people, maybe especially if people learned other programming languages first, maybe they have a computer science degree or maybe they went to boot camps and they learned all about React or whatever, but they sort of learned a framework or another way of programming and now they're trying to figure out the web.

319
00:38:39 --> 00:38:48
I think sometimes it can be tough for people to understand progressive enhancement because I don't know that other programming environments work this way.

320
00:38:48 --> 00:38:53
But because the web technology never changes, like, I mean, you know, there's never a version two.

321
00:38:53 --> 00:38:55
It's always building on the original web.

322
00:38:56 --> 00:39:01
Progressive enhancement is so, like, and Select was designed that way on purpose.

323
00:39:01 --> 00:39:06
There was, wasn't there a conversation about making a new element that had a different name?

324
00:39:06 --> 00:39:06
Yeah.

325
00:39:06 --> 00:39:10
And then they didn't go that way because of the progressive enhancement story?

326
00:39:10 --> 00:39:11
Yes, yes.

327
00:39:12 --> 00:39:15
It used to be a select menu element.

328
00:39:15 --> 00:39:16
That was the original proposal.

329
00:39:18 --> 00:39:30
And it was suggested by one of our standards engineers, I think, Anna, to reuse a select element so older browsers could get a nice fallback.

330
00:39:31 --> 00:39:35
Yeah, the semantic underpinning of the web is really a secret superpower.

331
00:39:35 --> 00:39:54
It enables so many things from accessibility trees to AI-readable content and incredible localization capabilities that simply don't come in other windowing and layout management systems unless they are either secretly actually the web underneath or people are going to an incredible amount of effort for it.

332
00:39:54 --> 00:40:01
So I absolutely think that using Select the way it's supposed to be is probably the best bet.

333
00:40:02 --> 00:40:07
And not to mention you get like keyboard navigation for free, which I haven't mentioned, but it's really awesome.

334
00:40:07 --> 00:40:19
Yeah, and not just like your keyboard navigation, but like all the keyboards and all the input devices and all the output devices, including Braille keyboards or like, you know, devices you have never even seen, like just use HTML.

335
00:40:19 --> 00:40:45
I mean, that's the exciting thing about not only customizable select, which is pretty darn exciting, but then also there's a spec in progress that you wrote the first draft of, Tim, of appearance-based to be able to style all the HTML form controls because that's one of the number one reasons why people reach for other tooling or use divs or spans instead is because they need to make them look a certain way for their brand.

336
00:40:45 --> 00:40:48
So that spec is still in progress.

337
00:40:48 --> 00:40:49
there's still discussion that needs to happen.

338
00:40:50 --> 00:40:53
I think even you and I don't agree completely yet on how it should work.

339
00:40:55 --> 00:40:59
There's discussion that's going to happen about how it works before anybody sees it running in a browser.

340
00:40:59 --> 00:41:25
But to find a path to solve a 30-year-old problem of can we actually give developers full control over styling real HTML form controls so that everyone can use real HTML form controls that can be totally interactive and you can make them look however you need to for the project that you're working on all right um alexa this is for you how does the model element

341
00:41:25 --> 00:41:51
handle loading states and errors and what control do developers have over the experience while a model is still loading that's a good question so we have a ready promise in order to signal developers that the model is loaded or not and i think with combination of this promise and like let's say spinners that you can implement we can provide a really good experience for users to see

342
00:41:51 --> 00:42:13
like whether a model is loading or not and when it's done like you can hide the spinner and present the model and the second part of the question was um the second part was uh do we have control over what control do developers have over the experience while a model is still loading It's still loading.

343
00:42:13 --> 00:42:15
I mean, that's pretty much it.

344
00:42:15 --> 00:42:19
Maybe Brendel has something more in mind.

345
00:42:19 --> 00:42:24
But before it loaded, you can't do anything with it.

346
00:42:24 --> 00:42:26
Fair.

347
00:42:26 --> 00:42:28
So we will signal you with promise.

348
00:42:28 --> 00:42:35
And then you can interact and use like entity transform and other things and other JavaScript APIs.

349
00:42:35 --> 00:42:40
Do we have a CSS pseudo class for when the model is loading?

350
00:42:40 --> 00:42:41
That's great feedback.

351
00:42:44 --> 00:42:57
So one thing that is, it's like images, but more so, is that there is a difference between downloading the bytes for an asset and having the resource actually loaded and usable.

352
00:42:58 --> 00:43:07
And so, you know, that's the difference between sort of fetching the core bytes of a JPEG or a PNG, and then when the image element that results from it is actually loaded.

353
00:43:07 --> 00:43:09
And the same is the case for a model element.

354
00:43:09 --> 00:43:16
You can get the bytes and you can then process them and put them into a model element.

355
00:43:16 --> 00:43:22
And there will still be some period of time before that is actually ready to use in that same way.

356
00:43:22 --> 00:43:31
So you can look at those two distinct states, more or less in the same way that an image works as well, and sort of reason about or present those things.

357
00:43:31 --> 00:43:50
And likewise, if you are generating a model procedurally using some kind of JavaScript library then you're still going to have to wait from the from the bytes even though you have them already in the browser locally for those to be processed into the actual resulting model so there are a couple of different phases but there are ways of reaching in and reasoning about and

358
00:43:50 --> 00:44:07
presenting relevant information for each one how big do you expect most models are on the web or will be well models can go crazy like yeah but we would encourage people to keep them smaller like and optimize them before putting on the web.

359
00:44:07 --> 00:44:10
The same thing that you do for images, right?

360
00:44:10 --> 00:44:14
Because loading time will increase and processing time.

361
00:44:14 --> 00:44:23
And, of course, as engineers, we love to take memory as an infinite entity.

362
00:44:24 --> 00:44:33
But, of course, it's limited, and it's our job to make sure that we can have enough memory in order to show our content to visitors of the website.

363
00:44:34 --> 00:44:41
I would encourage, in terms of the size, it depends on what you actually need to see out of a model.

364
00:44:41 --> 00:44:46
So for the most part, if a model is content on a page, it's not going to be bigger than that page.

365
00:44:46 --> 00:44:52
That also means that the number of textures and the size of the textures doesn't need to exceed what is relevant for displaying that.

366
00:44:52 --> 00:44:56
Likewise for the geometry, the shape of the object.

367
00:44:56 --> 00:45:12
So we don't have any hard guidelines, but in general, we've found that people can hit the maximum sort of visual quality in somewhere around 10 megabytes, which is larger than an image of the same thing.

368
00:45:12 --> 00:45:20
But we think that given the flexibility and the range of motion and animation and things that can be achieved with it, that most people should be able to do that.

369
00:45:20 --> 00:45:40
If you have an environment like in John's session, those things can end up larger and need to be made out of more polygons and more textures, but that's in order to sort of stand in as a full screen experience, much more in the same way that a long running or large video might end up being a lot larger than the page it's actually hosted on.

370
00:45:40 --> 00:45:57
Yeah, and what a lot of maybe web developers, web designers who are newer to the industry might not remember, but those of us who have been around for a long time do, is it used to be that like watching a video on a web page was an experience that included a bunch of waiting.

371
00:45:57 --> 00:46:03
And before that, it used to be that looking at an image on a web page was an experience that required a lot of waiting.

372
00:46:03 --> 00:46:17
So it feels like some of the – often the lessons learned in the past reemerge and maybe this is a moment to think through where it sounds like designers and developers need to think through like what should the experience be and how can they help their users through it and how can they optimize it to make it better.

373
00:46:18 --> 00:46:26
But also that things will continue to evolve and maybe there will be better ideas and new ideas about how to support loading and new technology to get invented over the next couple of years or so.

374
00:46:27 --> 00:46:28
I'm just making this up.

375
00:46:28 --> 00:46:30
No, it's absolutely true.

376
00:46:30 --> 00:46:38
So USD Crush, which we've outlined in the number of talks, is a command line tool provided by the Alliance for OpenUSD.

377
00:46:38 --> 00:46:42
And in the past, that would reduce everything down to JPEGs.

378
00:46:42 --> 00:46:48
As we've sort of agreed upon it, now that is compressing to AVIF files, which are much smaller.

379
00:46:49 --> 00:46:57
And we are in discussions with the Alliance for Open Media about what might be an appropriate mesh compression format for being able to pursue these things.

380
00:46:57 --> 00:47:01
And when that happens, then that's the kind of thing that people will be able to kind of do.

381
00:47:02 --> 00:47:07
It's back to that point of, like, what are people doing a lot of and how can we make that easier?

382
00:47:07 --> 00:47:12
And that sort of circle takes a while to close, but the progress happens.

383
00:47:14 --> 00:47:19
When you said, like, there used to be times when we waited images to load, I hear a modem connecting to it.

384
00:47:23 --> 00:47:24
Oh, it's there.

385
00:47:26 --> 00:47:32
Next question, how should developers think about permissions in Safari extensions compared to other browsers?

386
00:47:33 --> 00:47:38
Are there things Safari is more restrictive about, and how does that affect what an extension can actually do?

387
00:47:39 --> 00:47:40
That's a great question.

388
00:47:40 --> 00:47:54
When it comes to permissions for extensions in Safari, we design the permission models with privacy first and privacy in mind, and with the user's privacy in mind, because extensions can be really powerful.

389
00:47:54 --> 00:48:04
They can access a lot of data, a lot of sensitive browsing data, and we wanted to make sure that the users are in control of the data that can become available to an extension.

390
00:48:05 --> 00:48:12
So permissions that are requested in extensions manifest, for example, aren't automatically granted to that extension.

391
00:48:13 --> 00:48:28
We give the user that choice to decide which site they want the extension active on and so forth, and we try to be as clear as possible to the users of Safari of what that extension can do if it's granted access.

392
00:48:29 --> 00:48:43
And a lot of the time, developers aren't even trying to really access all this information, but they may be unsure of what sites the user might be on for their extension, so they might request too much access.

393
00:48:43 --> 00:49:05
And one way that they can work around that is with the active tab permission so that the extension is only active on the tab that the user is on, and then they lose host permissions like once the user navigates um and so that's like one work around that way uh for that issue but we really want to make sure that users privacy and data is kept as protected as possible

394
00:49:05 --> 00:49:35
that they're aware of the sites that the extension can can access cool how does the immersive api interact with existing web content if a user is in an immersive environment and something on the page needs their attention. How does that handoff work? In an immersive environment, the web page is still up. And so whatever you need to do on that page, you will have full ability to do. If

395
00:49:35 --> 00:49:57
you need to get into another application, I believe that this is a single app experience. And so if you had messages open on the side, that may dim. But it's available just with a crown press, restoring you back to your sort of general shared view. But if you need the web page, or if you want the web page to alter things about the immersive environment, say to move the position of where

396
00:49:57 --> 00:50:23
you are in it or play or pause an animation, then you can put whatever UI onto that web page for the user to be able to do that. Very nice. I know that for a lot of web developers, the idea of doing anything spatial is still very new and it might not always feel relevant to their work and their kind of everyday experience of developing is the model element the thing that tips that over or

397
00:50:23 --> 00:50:52
how is spatial web relevant to kind of more everyday developers these days so i would say model element is just the first step and probably the easiest step for web developers to experience three three day stuff of the web like or even like spatial web and it's definitely the easiest way to go right now even if you don't have a model you can either find online or with modern

398
00:50:52 --> 00:51:20
generation tools like you can actually generate 3d models to your liking whether it's a creative generation or use like a bunch of images in order to create an object that looks like a real object and i would highly encourage encourage people who are interested in spatial web to start with this it's like less friction in order to get into that and maybe after that even learn a little bit more

399
00:51:20 --> 00:51:48
about 3d about tools that allow you to build custom stuff uh like blender or something like that and yeah i mean there is natural progression in that but the first steps i outlined like right now it's the easiest and probably most compelling like when you can see results pretty quickly absolutely with appearance-based select how much of the styling just works out of the box

400
00:51:48 --> 00:52:12
versus what do developers need to explicitly reset or re-declare to get a consistent result across browsers this question is for me it's not it's not but this is the topic that tim and i don't agree this is a spicy yeah this um yeah but go ahead tim um so with appearance-based select what you get out of the box at least is the layout

401
00:52:12 --> 00:52:49
like the layout works like correctly and also it's better than the like Appearance None where like it uses it's a custom font and with Appearance Base like the font just like gets in your control like without any changes but what I find amazing about Appearance Base Select are like the pseudo elements that you get from it like now you can like uh select the picker icon part uh and style that

402
00:52:49 --> 00:53:19
separately without changing anything else in the select or you can change the just the drop down menu or just a check mark in the select element um so that's personally what i'm the most excited about the new like access to the specialized parts of the select yeah and the appearance based select for the select element and also appearance base which will come later it's not

403
00:53:19 --> 00:53:45
shipping now but it will be for all of the form control elements um and including select so eventually everybody will just use appearance base um it does yes deliver a functional form control unlike a parent's none um and it is a hundred percent interoperable between browsers so you're not going to get a different looking or different feeling form control in different

404
00:53:45 --> 00:54:04
browser engines and which is sort of the basic like foundation that's needed in order to be able to style it on like to put your own styling on top of it because if different browser engines had different versions of the form control and you would apply styling over here to make it look how you want it but then that styling being applied over here would end up with a different

405
00:54:04 --> 00:54:28
result if they don't start in the same place so i think that's one of the most profound things about this work is switching to an inner 100 interoperable base on which to start and then yeah pseudo element so you can target things that you weren't able to target before and more also the dom structure being consistent between browsers that the html under the hood you can

406
00:54:28 --> 00:54:50
address it because it's the same between all of the browsers. That's why it's very hard. It's a hard project. And the thing that I'm kind of laughing about that we're not quite, that a little bit of discussion is still happening about is what will it look like when you apply parent space or parent space select? What's the default? Is it sort of, does it remind you of the 1990s

407
00:54:50 --> 00:55:01
where now you need to make it look good or does it somehow look like a modern form control and You don't have to write as much CSS to make it look good.

408
00:55:02 --> 00:55:03
But what does modern even mean?

409
00:55:04 --> 00:55:05
And is that a fashion trend that's going to change?

410
00:55:06 --> 00:55:07
Whose opinion is that?

411
00:55:08 --> 00:55:18
But no matter where it starts, the idea is for it to inherit as much as possible from the existing website, which forms don't do by default, or they haven't done in the past.

412
00:55:18 --> 00:55:24
So, yeah, if you have a background color that your background color is being seen, if you have a font, your font is being used.

413
00:55:24 --> 00:55:28
If you've set your font color to something, that font color is being applied.

414
00:55:28 --> 00:55:37
Just for the UA style sheet to have as little opinion as possible and just to inherit as much as possible, because that's how everything else works in CSS.

415
00:55:38 --> 00:55:40
Yeah, that's how dialogue works, that's how details and element works.

416
00:55:41 --> 00:55:42
Everything works like that.

417
00:55:43 --> 00:55:44
To make it easier.

418
00:55:44 --> 00:55:52
And I want to add on top of that, that AppearanceBase doesn't use any layout magic that form controls have.

419
00:55:54 --> 00:56:28
So it's entirely, like, under the hood, it functions the way it's laid out is exactly like a normal um like html element like um the normal css and html layout applies there's no special hacks to size it a certain way or special hacks to clip it a certain way it's all um um like hopefully predictable web technology that that gets into play um yeah it's wild to me that inside the select uh pop over what's

420
00:56:28 --> 00:56:35
called the Picker, you can use Grid or Flexbox or Gridlanes to lay out your options.

421
00:56:37 --> 00:56:38
Yeah, it's incredible.

422
00:56:39 --> 00:56:51
For a developer who has an existing Chrome or Firefox extension, what's the realistic amount of work to get it into Safari and where do the manifest or API differences tend to bite people?

423
00:56:52 --> 00:57:02
So, in terms of a realistic amount of time, it depends on the extension in short. You might have an extension that you can convert and it just works out of the box.

424
00:57:02 --> 00:57:16
And there aren't any browser-specific APIs that that extension that was being run in Firefox or Chrome was relying on that isn't supported in Safari or is just in that browser alone.

425
00:57:17 --> 00:57:26
But for the most part, we've been working really hard in the web extensions community group to find inconsistencies within APIs across browsers.

426
00:57:26 --> 00:57:38
And so if you experience that you've converted it and you've tested it out in Safari, and for those who might not know, you can go into the developer menu and you can load a temporary extension.

427
00:57:38 --> 00:57:50
You can just use the extension resources for that extension that you've shipped to Firefox or Chrome or another browser, load it in Safari, test it out, see what works, see what doesn't.

428
00:57:50 --> 00:58:03
And if there is an API that behaves differently in Safari than it does in Firefox or than it does in Chrome, or if you've noticed, oh, Safari aligns with Firefox, but we don't align with Chrome, we've had those discussions and we're continuing to have those discussions.

429
00:58:04 --> 00:58:21
So bring that to the community group or the working group or file a bug on bugs.webkit.org or use Feedback Assistant to let us know that this is an inconsistency that you've experienced and we will take a look and see what we can do.

430
00:58:21 --> 00:58:34
But in terms of like manifest differences, there aren't any grave differences between like our manifest support across browsers and we tend to not fail on unsupported keys.

431
00:58:34 --> 00:58:45
And so that was a really big thing that we wanted to do to help developers who were migrating their extensions to Safari to not fail on things that we don't support and things like that.

432
00:58:45 --> 00:58:45
So, yeah.

433
00:58:46 --> 00:58:51
Now, for our final question, I'd love to get an answer from each person, a quick one, if possible.

434
00:58:51 --> 00:59:03
What is one thing that you would love people watching, web developers who work with Safari, who work with WebKit, what do you want them to know about either the upcoming release or WebKit in general?

435
00:59:06 --> 00:59:08
You get them if you want to.

436
00:59:10 --> 00:59:12
Gosh, I wish I had time with this question.

437
00:59:12 --> 00:59:47
i mean the answer that um i think the thing that might get easily missed but that i do hope people realize is just how long the release notes are this year they're so long just scrolling for days i think the safari 27 beta release notes are like twice the normal length yeah and not just 27 but also 26.2 26.4 all of the 26. you know two three four five six release notes like if you add them

438
00:59:47 --> 01:00:12
all up together which i have done multiple times uh it's an incredible amount of work and it's because so much time and attention went in this year to hunting down those little paper cuts and doing whatever it took to fix them and we're not done nobody thinks we're done um we're gonna continued that work but I know I've heard from a lot of web developers you know in my mentions or

439
01:00:12 --> 01:00:42
wherever I see people frustrated or feeling dejected or feeling like I don't know they they come to some kind of conclusion that was you know a different ideas coming from a different browser engine about like whether or not we even care it's like we absolutely care and sure you're hitting a bug like let us know we're going to squash that bug because what's the advantage of

440
01:00:42 --> 01:01:09
making users experience of the web worse in safari why in the world would that be our goal that's not our goal like so yeah um yeah i mean we mean it people worked incredibly hard this year this whole cycle to um to really align with web standards and really like improve the quality of the browser engine so we've gone as far as rewriting our javascript module loader to to fix um the the

441
01:01:09 --> 01:01:34
top level await bug that people have been mentioning so i i think just that effort is like amazing for a couple of bugs um yeah and something to look forward to or to look for on the web extension side is that not recently but a few years ago we moved all of our support for extensions into WebKit, and that might not be something that folks know.

442
01:01:34 --> 01:01:42
And so if you have filed a bug or you want to see a difference in an API, you can contribute, and you can bring your contributions to WebKit.

443
01:01:43 --> 01:01:48
So feel free to check out the code base, file a bug, and see how you can help improve it.

444
01:01:49 --> 01:01:49
Nice.

445
01:01:50 --> 01:02:03
Yeah, as a father of three, I can't pick a favorite child, but to highlight one, I would definitely say play with model element and see how it works on different platforms and just enjoy 3D on the web.

446
01:02:03 --> 01:02:04
All right.

447
01:02:04 --> 01:02:23
And I would say the web is such an incredible platform these days that if you've just started or if you've been on it for a while, try to look back and figure out how little work you have to do based on what the web standards are doing for you.

448
01:02:23 --> 01:02:33
I have been a web developer professionally for over 25 years now, And I mostly managed to get away with not using any frameworks, not using any environments.

449
01:02:33 --> 01:02:35
And I just couldn't be happier about that.

450
01:02:35 --> 01:02:40
So for web developers, think about how little you might be able to get away with building and using.

451
01:02:40 --> 01:02:45
Because I think you would be pleasantly surprised by the state of it these days.

452
01:02:45 --> 01:02:54
I would love to see people use more like Safari-specific technologies in demos.

453
01:02:54 --> 01:02:59
I want to see more like random function demos and filter function demos.

454
01:02:59 --> 01:03:03
- You mean things that we've shipped first, that are web standards and will be coming to other browsers sooner.

455
01:03:03 --> 01:03:04
Yeah.

456
01:03:04 --> 01:03:05
- Yeah, hanging punctuation.

457
01:03:05 --> 01:03:07
Like, I wanna see more love.

458
01:03:07 --> 01:03:08
- There's a lot of that.

459
01:03:08 --> 01:03:12
- 'Cause there are quite a few things I think people don't realize.

460
01:03:12 --> 01:03:17
We often are inventing new technology and shipping things first, and yeah.

461
01:03:17 --> 01:03:19
- Very nice.

462
01:03:19 --> 01:03:22
Well, that is about all the time we have today for this group lab.

463
01:03:22 --> 01:03:28
We are so thankful that you all joined us today, and we hope that you found it really helpful.

464
01:03:28 --> 01:03:34
I also want to say thanks to our wonderful panelists, as well as all the folks working hard behind the scenes to make this happen today.

465
01:03:35 --> 01:03:45
As we mentioned earlier, if we didn't get to your question, please visit the developer forums at developer.apple.com slash forums, where we will continue the discussion.

466
01:03:46 --> 01:03:50
And visit bugs.webkit.org to file any bugs or feature requests.

467
01:03:50 --> 01:03:53
We really do appreciate your bug reports and feedback.

468
01:03:53 --> 01:03:57
For more articles and release notes on WebKit and Safari, check out webkit.org.

469
01:03:58 --> 01:04:04
And if you want to learn more about grid lanes, we made an amazing field guide for you at gridlanes.webkit.org.

470
01:04:04 --> 01:04:05
It is so good.

471
01:04:05 --> 01:04:11
Speaking of feedback, you should receive an email with a survey link to let us know about your experience at WWDC.

472
01:04:11 --> 01:04:14
We would love to incorporate your feedback in future events.

473
01:04:14 --> 01:04:17
Thanks again for joining us and hope you have a great WWDC.
