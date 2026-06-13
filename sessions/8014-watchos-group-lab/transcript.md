---
title: watchOS Group Lab
source: https://developer.apple.com/videos/play/wwdc2026/8014/
session: 8014
collection: wwdc2026
duration: 01:00:06
fetched: 2026-06-12
via: whisper.cpp ggml-large-v3 (session SD video audio)
transcription: machine-generated, no speaker diarization
---

> Machine transcription of the **watchOS Group Lab** SD video via whisper.cpp (`ggml-large-v3`).
> Group labs ship no Apple transcript; this is generated from the audio. Cues are timestamped segments, **not** speaker-attributed turns.

## Transcript

1
00:00:03 --> 00:00:09
Hi, I'm Leah Wommelsdorf, and I'm a part of the Worldwide Developer Relations team at Apple.

2
00:00:09 --> 00:00:17
Today, I am joined by members of the WatchOS engineering team, and we are so excited to answer some of your questions about the platform.

3
00:00:18 --> 00:00:20
So before we get started, I'd love for you all to introduce yourselves.

4
00:00:21 --> 00:00:27
Dan, I'm giving your name away, but Dan, do you want to introduce yourself, talk about what you work on for WatchOS?

5
00:00:27 --> 00:00:31
I was going to call myself David, but now you stole it.

6
00:00:31 --> 00:00:32
You can't go undercover anymore.

7
00:00:32 --> 00:00:32
I can't go undercover.

8
00:00:32 --> 00:00:33
It's all over.

9
00:00:33 --> 00:00:35
Hi, I'm Dan Keene.

10
00:00:35 --> 00:00:38
I manage the workout team on watchOS, and I'm super thrilled to be here.

11
00:00:38 --> 00:00:46
I'm Anne Hitchcock, and I am a watchOS frameworks engineer, and I work on all kinds of stuff.

12
00:00:46 --> 00:00:47
What are we talking?

13
00:00:47 --> 00:00:49
Throw some frameworks out there.

14
00:00:49 --> 00:00:50
Throw some frameworks out there.

15
00:00:51 --> 00:00:55
So system spaces stuff, so system UI.

16
00:00:56 --> 00:00:58
I've worked on some connectivity stuff this year.

17
00:00:59 --> 00:01:04
I work with a lot of other teams, so I've worked with the foundation models team this year.

18
00:01:05 --> 00:01:11
I worked with the App Intents team this year in their work with WatchOS, so a lot of all over the place.

19
00:01:12 --> 00:01:12
That's amazing.

20
00:01:13 --> 00:01:14
Cool, I guess I'm next.

21
00:01:14 --> 00:01:15
Yeah, I'm Devin.

22
00:01:15 --> 00:01:20
I work on widgets and live activities and then how they integrate with system spaces like SmartStack and WatchFaces.

23
00:01:21 --> 00:01:22
And hey, I'm Matthew Koontz.

24
00:01:22 --> 00:01:28
I manage the WatchOS system spaces team, so that includes a lot of what you kind of think of when you think of watch.

25
00:01:28 --> 00:01:36
So how we launch apps and how we get notifications and all the underlying look and feel of the platform.

26
00:01:37 --> 00:01:44
So what I'm really excited about is we have a lot of frameworks and a lot of system experiences covered here today from HealthKit to UI frameworks to connectivity.

27
00:01:45 --> 00:01:50
And what that means is we're really well posed to answer a lot of your questions about all things watchOS.

28
00:01:51 --> 00:01:53
And this year has a lot of big updates for watchOS.

29
00:01:54 --> 00:02:00
There's new frameworks coming to the watch with foundation models, vision framework, core AI framework.

30
00:02:00 --> 00:02:02
They're all on watchOS 27.

31
00:02:02 --> 00:02:09
And there are also some big updates for Liquid Glass and new HealthKit APIs like the menopause API and the workout zones.

32
00:02:09 --> 00:02:13
So there's a lot of new stuff that we're well-suited to talk about.

33
00:02:13 --> 00:02:17
But also what's cool about watchOS is there's such a long history of the platform.

34
00:02:17 --> 00:02:28
And this is a great avenue to discuss all of your questions, whether it's about the old stuff you've been thinking about for a long time, qualities that make great watch apps, or the new stuff that you're starting to think about.

35
00:02:28 --> 00:02:58
So we're so excited for your questions today. And I'm really excited to dive into it. So I'm going to check to see some of your questions. Okay, so for our first question, and this is from forgive me if I'm pronouncing the username improperly. This is from Emcro. Okay, the question is, a session video mentions foundation models are now available on watchOS. Does that

36
00:02:58 --> 00:03:25
run directly on the connected iPhone or directly via PCC, private cloud compute, in the event of an independent watch app. So Anne, I'm going to kick this off for you. Let's talk about foundation models on watch. Okay. The foundation models on watch do not require you to be connected to your iPhone, but they will require that network connection. You can use PCC or you can use

37
00:03:25 --> 00:03:50
anything that conforms to the language model. So the language model protocol will have official support for Claude and Gemini coming soon from those vendors, but you can conform to language model yourself, but it will require a network call. So you're not going to have models running on the device, but I've had a lot of fun this year working with that team and playing with that

38
00:03:50 --> 00:04:16
framework and there's so many fun things that you can do with that that are great use cases on watch like summarizing text to fit on the watch display and you know making more accessible text out of really dense text that you might have so it's a lot of fun yeah this is by far my like thing i'm most excited for because i think it's going to be where we'll see the developer

39
00:04:16 --> 00:04:39
our audience take it so much further than even we thought would be possible like this is such an amazing emerging technology and we're finally opening it up uh on a platform that you know has a lot of great health data and insights and now you can go see how it applies and you can use it to build some really amazing experiences so yeah i by far foundation models is my most excited thing

40
00:04:39 --> 00:04:50
uh but i think it's things like that the tech summarization or uh take some all this data and and go feed it to an LLM and see what kind of insights you can provide to your users.

41
00:04:50 --> 00:04:52
It's going to be such an exciting opportunity.

42
00:04:53 --> 00:04:55
So I think there's a lot of really cool stuff people can do.

43
00:04:56 --> 00:05:00
In terms of learning resources, I know there's some dub-dub videos, some sample code.

44
00:05:00 --> 00:05:07
I'd love to hear that part purely, like hear more about what someone needs to do to onboard to thinking about Foundation Models Framework.

45
00:05:07 --> 00:05:10
And then also the other piece of that story, network connectivity.

46
00:05:13 --> 00:05:32
Developers are going to have to think about what type of experience makes sense in my app, some graceful fallbacks if there's you know maybe it's not a cellular supported watch what types of things should people developers be thinking about as they prepare to use foundation models framework in their watch apps yeah they do need to keep that in mind check there are some apis in foundation

47
00:05:32 --> 00:06:02
model to to check is its foundation models you know is this available right now so make sure that you're checking that before you're making these calls and definitely if you're using pcc be aware that needs an entitlement that's a that is uh mentioned in that reference session um and so plan for that fallback that if you're using pcc or any language model provider

48
00:06:02 --> 00:06:17
have you reached the quota um what's what's your token usage on that so there's a lot of fallbacks that people need to think about when they're designing their apps using foundation models and large language models.

49
00:06:19 --> 00:06:37
Yeah, and it sounds like there's a lot of watch, like whether you're using a Foundation Models-enabled app or you're just making a regular watch app, I feel like there's a lot of design foundations that also come into play when making a great experience on watch, like some qualities that come to mind are glanceability, predictability,

50
00:06:37 --> 00:06:43
like what other things come to mind that we should really be keeping in mind as we think about the next wave of watch apps?

51
00:06:44 --> 00:06:49
I think it's to be a lot about trying to find the right experience for the wrist.

52
00:06:50 --> 00:06:51
This is a different form factor.

53
00:06:51 --> 00:06:54
It's something that we talk about year over year with Apple Watch and WatchOS.

54
00:06:55 --> 00:07:07
And I think, again, the excitement I have about this new technology for us is that we will see what you do with it to go build out these great new experiences, but it really comes back to the fundamentals.

55
00:07:08 --> 00:07:12
And the fundamental experiences on watch are that they are literally smaller.

56
00:07:12 --> 00:07:13
They're on your wrist.

57
00:07:13 --> 00:07:16
They need to be glanceable.

58
00:07:16 --> 00:07:17
They need to be quick.

59
00:07:17 --> 00:07:24
They need to be concise and easy to digest for a user because you are going to look at your wrist and then put it down and go do something else.

60
00:07:24 --> 00:07:31
And so it's about bringing that information to your users in a timely and, like, immediate interaction.

61
00:07:32 --> 00:07:35
And I think they're not just smaller and glanceable, but they're more intimate.

62
00:07:36 --> 00:07:36
They're more personal.

63
00:07:36 --> 00:07:50
And I think a large thing that the foundation models can give you to the insights and stuff that we've been talking about is how to make that more personal, how to make it feel that the content that you're going to display really quickly for the user here is really personal for what they want it to be.

64
00:07:50 --> 00:08:00
Yeah, it's something that we think about a lot with how we've been using new large language model type features for interactions on our platform and where you guys take it as well.

65
00:08:00 --> 00:08:08
So, yeah, I mean, go back to those fundamentals and then see how you layer these experiences on because, you know, at the end of the day, it's still a watch.

66
00:08:08 --> 00:08:16
It still has all the power and performance constraints of this platform, but it's now opened up in a whole new way than we've had in the past.

67
00:08:17 --> 00:08:24
Yeah, I'm really excited not just to, like, to think of new ideas myself and to see what you, the developer community, comes up with.

68
00:08:24 --> 00:08:40
And as you're trying new things and you have questions or you have discussions you want to have, I really encourage you to one-file feedback through our feedback channels and also check out the Apple Developer Forums to ask questions as you hit roadblocks or maybe you want to deepen your understanding.

69
00:08:40 --> 00:08:42
But I think this is a really exciting year for watchOS.

70
00:08:43 --> 00:08:46
Okay, I'm going to dive into our next question.

71
00:08:48 --> 00:08:53
And, okay, we have – oh, you guys are really interested in the foundation models.

72
00:08:53 --> 00:08:58
Okay, I'm going to do one more on the foundation models before switching gears a little bit.

73
00:08:58 --> 00:09:16
So this is from Durkio, and this question is, when using the foundation models framework on Apple Watch, does it only use PCC through either cellular or Bluetooth to iPhone, or can it also utilize the on-device model in the iPhone if it's on the same network?

74
00:09:19 --> 00:09:24
So it does not use the on-device model on the iPhone.

75
00:09:24 --> 00:09:27
It uses PCC over the network.

76
00:09:27 --> 00:09:33
So you're going to make a network call over your cellular or Wi-Fi connection.

77
00:09:34 --> 00:09:42
And so you can use PCC or you can use another language model conforming protocol, but you're going to go over the network.

78
00:09:42 --> 00:09:44
And so that's something to keep in mind.

79
00:09:44 --> 00:09:46
You're always going to have that network latency for those calls.

80
00:09:46 --> 00:10:09
So really think about what you're making those calls for and the timing and when you're going to do them and how many you're going to do them because you're always going to make that network hop. That is good insight. And again, on the topic of foundation models, I'm really excited that there's such great documentation about all the opportunities for every Apple platform. So I

81
00:10:09 --> 00:10:36
really encourage you all to check that out as you continue exploring. Okay, we have a more of a platform question from username LazyVar. Fun name. Very cool. Okay, the question is, as an iOS dev moving to watch os yay um i'm adapting to the watch's unique background constraints what core architectural paradigms or hidden pitfalls should we keep in mind when handling heavy

82
00:10:36 --> 00:10:59
off main thread work like cloud kit syncing without relying on ios habits there's a lot to get in here thank you lazy var okay who who is that matthew do you want to kick things off yeah i mean i think you know i was mentioning the constraints of the platform and you know for a lot of what we want you as ios developers coming to watch is thankfully a lot of the apis that you

83
00:10:59 --> 00:11:24
are already using work on the platform you have ns url session you have things like cloud kit and off main thread like swift async like there's a lot that you already have in skills that you can bring to the platform as well as just like a whole wealth of swifty y and like code that you have being able to be reused the the key thing to know is that we as like the platform do take like

84
00:11:24 --> 00:11:49
strident rules so i'm trying to make sure that you and everyone because there's so much that's happening on the watch at any one time that we are uh giving the runtime that and preserving the battery life for our users because we know how much uh all day battery life matters to our users into your users. So those are the key things to know about. We do have, you know, limits put in

85
00:11:49 --> 00:12:11
place by the system. You'll see sometimes things like watchdog timeouts that, you know, are there put in place to make sure that both you and everyone else that's running on the platform are good citizens. But for the most part, you should be able to just reason through similar kinds of background runtime considerations you have on phone, but just know that the

86
00:12:11 --> 00:12:14
timeouts are very strict, and they're strict for good reasons.

87
00:12:15 --> 00:12:21
You brought up Swift async, and I do want to say one of the things that I really enjoy doing on the watch is how constrained the platform is.

88
00:12:21 --> 00:12:25
So if you're coming from iOS to watchOS, it's not just the smaller screen.

89
00:12:25 --> 00:12:28
You also have a lot lower core count and stuff like that.

90
00:12:28 --> 00:12:31
So being cognizant of the work that you're kicking off is very helpful.

91
00:12:33 --> 00:12:36
And sometimes constraints can sound scary.

92
00:12:36 --> 00:12:58
oh no constraint but it's an intellectual opportunity exactly and really thinking about i think like something we talk a lot about the watch platform is that some things are great on an ios app or mac os app and on a watch app it's a totally different design problem and there as you alluded to earlier there's this great guidance from uh the watch os 10 wwdc video um it might be

93
00:12:58 --> 00:13:17
called design i actually am not remembering the exact name but it's a really great video about designing great watch experiences instead of just taking everything from an ios app and putting it on the watch think about those core moments and experiences that really shine on the platform with the intimate qualities of the watch and how people leverage them um design and build apps with

94
00:13:17 --> 00:13:39
watch os there we go and you'll recognize one of the one i was gonna say we've got a lot of a lot of famous people today talking about their videos a little self-promotion yeah um but no it is really cool. And I loved what you said about different frameworks, like many frameworks being available on watch with similar, like instead of just being available, it's not just, oh, it's available. It's

95
00:13:39 --> 00:13:57
tailored to the platform. Like Devin, I think you could really speak about widgets and live activities. Like there's so many system experiences that are tailored to the watch and what comes to mind for you as the opportunity for developers? Yeah, definitely. I mean, I think, um, coming from iOS, actually there's a lot of similarities with widgets and live activities, the runtime you

96
00:13:57 --> 00:14:16
get for widgets on watch os i mean other than a slower uh you know smaller core count it's actually the same as what you get on ios um so that you know typically transfers transfers over pretty cleanly um and even if you already have a lock screen widget um that should just work out of the box on watch os maybe with some small layout tweaks um i think we do have some unique api

97
00:14:16 --> 00:14:33
services though especially around smart stack you have opportunity to get your widget suggested at the right moment for the user um using the the widget relevance api which is heavily exercised on watch os um so i think there's a special opportunity there where you can really think about you know just the right way to be proactive with your app and push information to the user

98
00:14:33 --> 00:14:54
instead of needing to dig in and look for it we have an interface where you can just bring it when the time is right you know and dan what comes to mind for you thinking about health experiences on watch like i know there's a different level of data availability a different time span does anything come to mind as a unique opportunity on watch that's different from on a phone many

99
00:14:54 --> 00:15:21
things oh good good i set you up well yeah you did um if you think about it so the watch has a lot more information about you that is recording at that time so uh your your calorie information your heart rate stuff like that is uh it's coming from the watch uh it's sort of that as the primary case for it um i was talking to some developers on monday uh just about interesting ways to use

100
00:15:21 --> 00:15:42
heart rate in their apps in terms of surfacing, wow, your heart rate was really high during this time. That's kind of interesting. Totally not in a fitness-related context. So I think that there's an opportunity to, again, going back to that personal intimate thing, we're measuring things about the user. We're recording them into HealthKit. It's all there. There's times when that

101
00:15:42 --> 00:15:45
could actually really be applied to things that you don't necessarily even expect it to be.

102
00:15:45 --> 00:15:52
Yeah, I totally agree. And again, an opportunity and design constraint problem that's really cool.

103
00:15:52 --> 00:15:56
And I have one more treat for LazyVar, a follow-up question.

104
00:15:57 --> 00:16:10
For those, I think what's so cool about Apple Watch and WatchOS is that you can actually have a presence on Apple Watch without building a watch app right away through system experiences like live activities, through notifications.

105
00:16:11 --> 00:16:20
So for people who already have apps on other platforms and they're WatchOS curious, whether they're thinking about building their first app or they're like, how do I have a presence on watch?

106
00:16:20 --> 00:16:37
what types of things do you recommend the developers think about what yeah how should they approach that transition or what's step one i mean i think that there's a lot of really good opportunities you mentioned one of the biggest one which is live activities we're like we really love how we have this experience with the phone live activities show up on your lock screen they're

107
00:16:37 --> 00:16:56
like these great glanceable interactions ryan do you have another platform so we've decided to bring them to the watch as well and they come automatically but there's an opportunity for developers that if you want to customize that experience and make a really great looking live activity that shows up on the smart stack, it's really easy to do. You don't need a watch

108
00:16:56 --> 00:17:20
app at all. So we keep on looking for more and more opportunities like that. One that we also brought in watchOS 26 is controls. So you can go into the watchOS control center and add your controls from the phone app to the watch. So I have one to unlock my car. A developer did not have to do anything actually for that one at all there's no customization needed we look at what

109
00:17:20 --> 00:17:44
kind of uh controls that you provide find ones that make sense to bring to the watch and do it automatically so it's a really great thing to like think about you know there's like spaces on the phone already like the lock screen or control center and we we decided to bring those over to the watch so that uh like your users are likely using them already and i think that's just a

110
00:17:44 --> 00:18:09
really good entry point into the platform of course we want you to also bring an amazing app but not the apps don't necessarily make sense for every use case and so for those uh experiences the live activities for example are just like a really great way to still bring your experience front and center to people's wrists have that personal interaction uh and it's uh thankfully not a

111
00:18:09 --> 00:18:30
whole lot of work yeah yeah like it's validation like you need to test your code try it out and like be aware of that but i mean it's a really lightweight way to get on the platform exactly right yeah very cool okay i've i've exhausted that i'm gonna keep going and check out some more no lazy that was a great question and i really enjoyed it um oh we could plug the live activities

112
00:18:30 --> 00:18:48
fundamentals session oh that was a great session so if you want to dive into live activities that is a great session there you go on the topic of fundamentals another set so live activities fundamentals and widget kit foundations I know two different naming conventions but there's two great videos that for people curious about watch OS as a

113
00:18:48 --> 00:19:13
platform I really recommend that you check out and you can build a strong foundation with these system experiences yeah so we did have one more thing for lazy far one more thing that's good thing it was oh man there's even more okay I have actually another really good question from lazy var and then I Okay, this one is about a different topic though any tips to speed up physical watch OS debugging loops

114
00:19:13 --> 00:19:32
what are the best practices for minimizing Xcode installation delays and Handling the initial system symbol sync symbol system symbol sync. That's like I'm impressed if I delivered that well More efficiently can we force a local Wi-Fi tunnel or pre cache these system symbols on the Mac?

115
00:19:32 --> 00:19:41
Let's talk about yeah, let's talk about tooling This is something that Anne and I spend a lot of time thinking and talking about.

116
00:19:41 --> 00:19:44
So, like, this is a conversation that we have frequently.

117
00:19:44 --> 00:19:46
Yes, yes.

118
00:19:47 --> 00:19:48
Goodness.

119
00:19:48 --> 00:19:53
Yeah, I would say, yeah, ensure your connectivity to the Mac is good.

120
00:19:55 --> 00:19:57
Be nearby.

121
00:19:57 --> 00:20:00
You know, I don't think there's a lot of magic to this.

122
00:20:02 --> 00:20:03
You keep your OS up to date.

123
00:20:03 --> 00:20:15
I know that the Xcode team has done a lot of work over the past couple releases to make this quite a bit better.

124
00:20:16 --> 00:20:19
So please give it a try.

125
00:20:20 --> 00:20:21
Update.

126
00:20:21 --> 00:20:24
Get the beta installed.

127
00:20:24 --> 00:20:25
Xcode 27.

128
00:20:26 --> 00:20:26
Yes.

129
00:20:26 --> 00:20:29
No, this year I've heard a lot of – there's been a lot of attention.

130
00:20:29 --> 00:20:32
Yeah, they have put a lot of work into this.

131
00:20:32 --> 00:20:40
And not only have they put a lot of work into this, but they put in a lot of tools to help diagnose any problems that you might have.

132
00:20:40 --> 00:20:51
So if you're running into a problem, even after all the work they've done, please file feedback because they've put in a lot of stuff to help them diagnose any problems that you're seeing now.

133
00:20:51 --> 00:20:57
So please file that feedback because that will help find any of these lingering issues that you're seeing now.

134
00:20:58 --> 00:21:00
So, yeah, install Xcode 27.

135
00:21:01 --> 00:21:04
Give it a go now because they have heard you.

136
00:21:04 --> 00:21:11
They have heard your frustration, and they have tried to make it better, and they want to hear, is it working?

137
00:21:11 --> 00:21:17
If it's not working, file that feedback and send them the logs because they're ready.

138
00:21:17 --> 00:21:18
Yes.

139
00:21:18 --> 00:21:34
I'd say just like, you know, the top tips and tricks that we think about, we now, starting with Xcode 26, but also Xcode 27 with Device Hub, we have a direct connection from Xcode on your Mac directly to the watch.

140
00:21:34 --> 00:21:56
in the past we would proxy through the phone and we have seen substantial improvements in both reliability and throughput by having the connection go directly from the watch to the phone sorry watch to the to your mac yeah uh so that you mentioned like infrared wi-fi that's actually something that we are doing uh so we uh do have that connection do make sure that you uh

141
00:21:56 --> 00:22:20
are on a network that actually has the ability for devices to like talk to each other we've seen some issues with people especially like on corporate networks where peer-to-peer is not enabled uh so do you keep an eye on that kind of like network setup uh more recent apple watch hardware also has a five gigahertz wi-fi chip uh that helps also again for throughput so there's just a couple of

142
00:22:20 --> 00:22:47
these like things that we can do to help uh the the actual like network latency uh that that's why you're saying update and using more recent hardware helps a ton there we of course though support and need you to be testing on the whole range of apple watches that are out there right now because your users are likely not on the latest apple watch uh and so if you need that

143
00:22:47 --> 00:23:01
connection like testing on physical hardware is great using device hub to test all the various different uh hardware configurations though it's also a great way to make sure you get that validation across all of your potential users.

144
00:23:01 --> 00:23:03
- Absolutely, well said.

145
00:23:03 --> 00:23:04
Were you gonna add something, Devin?

146
00:23:04 --> 00:23:06
Amazing, I will.

147
00:23:06 --> 00:23:13
There's a lot to, we're really eager to hear your feedback on Xcode 27, so as you are debugging, let us know what you think.

148
00:23:13 --> 00:23:16
Okay, I'm gonna move on to a new question.

149
00:23:18 --> 00:23:22
Let's see.

150
00:23:22 --> 00:23:26
Okay, this is one I'm excited about 'cause I have a really good answer.

151
00:23:26 --> 00:23:52
But PearlJam66 says, "What are your favorite new features of watch os 27 you can go first you said you i know it was like who's gonna who's gonna speak first who's gonna speak first well i mean i gotta i gotta plug my team right uh yeah let's hear it we we've got really great new insights and workout buddy that i have personally

152
00:23:52 --> 00:24:16
found wonderful in my workouts and i'm really enjoying them and that's not to say less of any of the rest of the amazing features in watch os 27 but i really enjoy it and i think they've done a great job with it very cool could you give us an example like what kinds of insights uh sure um measuring um speed over time and how it compares to previous weeks months year uh how that's going

153
00:24:16 --> 00:24:36
and as my cycling personalized exactly and as my cycling has been getting better it's actually been it's been uplifting to hear that's awesome that's exciting oh can i have more than one favorite feature i mean yes you can double down triple down i didn't know that was an option oh So I've built a lot of apps and I play with a lot of APIs.

154
00:24:36 --> 00:24:38
I mean, they're not really playing.

155
00:24:38 --> 00:24:43
But anyway, so I have had so much fun with Foundation Models framework on watchOS.

156
00:24:44 --> 00:24:46
So that is a favorite for me.

157
00:24:47 --> 00:24:54
But also being able to update widgets with watch connectivity.

158
00:24:55 --> 00:24:58
So, yes, that is great.

159
00:24:58 --> 00:25:00
So if you've been waiting for that, it works now.

160
00:25:01 --> 00:25:02
I feel like say it again into the mic.

161
00:25:03 --> 00:25:04
Update widgets with watch connectivity.

162
00:25:05 --> 00:25:05
Yay.

163
00:25:05 --> 00:25:06
Capital letters.

164
00:25:06 --> 00:25:07
Yeah.

165
00:25:07 --> 00:25:09
It's great to see that one get across the line, definitely.

166
00:25:09 --> 00:25:09
Yes.

167
00:25:09 --> 00:25:16
I think it opens up a lot more opportunity for apps to stay in sync between the iOS app and their watch companion and just push it right to your wrist.

168
00:25:16 --> 00:25:16
Exactly.

169
00:25:18 --> 00:25:19
Well, that was actually one of my favorite features, too.

170
00:25:20 --> 00:25:20
So you saw that one.

171
00:25:21 --> 00:25:33
But I guess also plugging my own team, I think, you know, there's a lot of enhancements to, I mean, general performance and reliability around widgets this year and making sure they load in a timely manner and making sure you have more of that runtime to update information.

172
00:25:34 --> 00:25:36
Also we have new suggestions in the Smart Stack.

173
00:25:36 --> 00:25:38
I found a really handy one the other day.

174
00:25:38 --> 00:25:40
Gave me a reminder of a friend's birthday.

175
00:25:40 --> 00:25:42
But yeah, always making new suggestions.

176
00:25:42 --> 00:25:44
- Yeah, that's one of my favorites too, actually.

177
00:25:44 --> 00:25:45
- Very windy.

178
00:25:45 --> 00:25:55
- It's also just really fun, and I think people that update to watchOS 27 will find some delightful new surprises in the Smart Stack on holidays and other significant events.

179
00:25:55 --> 00:25:56
So look out for those.

180
00:25:56 --> 00:25:58
- Now I have something to look forward to for holiday.

181
00:25:58 --> 00:25:59
Check out the Smart Stack.

182
00:25:59 --> 00:26:04
- I think my favorite feature is what we're doing with some really great work with Siri AI.

183
00:26:04 --> 00:26:08
It's something that's not out in beta one, but something that will be coming.

184
00:26:08 --> 00:26:13
And we're really, really excited to see what we do in that space.

185
00:26:13 --> 00:26:14
I think it's gonna be really, really awesome.

186
00:26:14 --> 00:26:20
And just in general, I think the new intelligence features on the platform are fantastic.

187
00:26:20 --> 00:26:23
- I am so excited, very cool.

188
00:26:23 --> 00:26:39
I was gonna say, I'm really excited about all the health updates, the health kit updates coming to watchOS, and not just the first, the experiences within Apple's apps, but to see how developers use them with heart rate zones, cycling power zones, and the perimenopause and menopause API.

189
00:26:39 --> 00:26:41
I'm so excited for the full breadth of features.

190
00:26:41 --> 00:26:44
I was going to say, like, if I got two answers.

191
00:26:45 --> 00:26:46
Okay, just this once.

192
00:26:46 --> 00:26:49
Number two was the zones, but now you've stolen it.

193
00:26:49 --> 00:26:54
I know now, but Devin stole Anne's answer, so really we're all just stealing.

194
00:26:55 --> 00:26:56
It was a co-answer.

195
00:26:56 --> 00:26:57
It was a co-answer.

196
00:26:58 --> 00:27:00
Mutual appreciation for multiple frameworks.

197
00:27:00 --> 00:27:01
No, that was really good.

198
00:27:02 --> 00:27:03
Thank you, PearlJam66.

199
00:27:04 --> 00:27:14
Okay, I have a question about SceneKit from Zev Eisenberg, NYT.

200
00:27:15 --> 00:27:16
Exciting.

201
00:27:16 --> 00:27:17
Maybe that stands for New York Times.

202
00:27:18 --> 00:27:22
Okay, the question is, I have a watchOS app that uses SceneKit.

203
00:27:23 --> 00:27:29
SceneKit was deprecated in watchOS 26, and its replacement, RealityKit, is not available on watchOS.

204
00:27:29 --> 00:27:35
Is there a recommendation, is there a recommended modern way to render 3D graphics on watchOS?

205
00:27:36 --> 00:27:40
So, Matthew, I'm curious about what comes to mind for you for this.

206
00:27:40 --> 00:27:45
Yeah, I mean, I think SceneKit deprecated but not gone.

207
00:27:45 --> 00:27:48
So you're still welcome to use it in your app.

208
00:27:48 --> 00:27:54
But we really do think that the path forward for watchOS in particular is what we provide in SwiftUI.

209
00:27:55 --> 00:27:58
And so SwiftUI Canvas has been around for a couple of years now.

210
00:27:58 --> 00:28:00
It's a really great drawing system.

211
00:28:00 --> 00:28:09
It's not SceneKit, but it is still a really well-featured and we think appropriate for the watch platform graphics library.

212
00:28:10 --> 00:28:20
So the note there is likely time to start walking away from seeing Kit for Watch and going towards the SwiftUI and SwiftUI Canvas.

213
00:28:20 --> 00:28:22
I would say Canvas is very fast, too.

214
00:28:22 --> 00:28:23
It runs on the GPU.

215
00:28:24 --> 00:28:25
Take advantage of that on the watch.

216
00:28:25 --> 00:28:25
Yeah.

217
00:28:26 --> 00:28:36
And if there are things that you can't do, can't figure out how to do, if you can't figure it out, talk to DTS, file feedback.

218
00:28:36 --> 00:28:38
Post in the forums.

219
00:28:38 --> 00:28:39
Some other people might know.

220
00:28:40 --> 00:28:48
Yeah, because, you know, please figure out if you haven't figured it out or if it can't be done, we would like to know.

221
00:28:48 --> 00:28:48
Definitely.

222
00:28:49 --> 00:28:49
Absolutely.

223
00:28:49 --> 00:28:50
Name of the game.

224
00:28:50 --> 00:28:58
We're all trying to make the watchOS platform as optimal as possible for you, the developers, and for all the people who love using their Apple Watches.

225
00:28:58 --> 00:29:02
So, yeah, if you can't figure it out, we want to help you get there.

226
00:29:02 --> 00:29:03
Very cool.

227
00:29:04 --> 00:29:30
Okay, I have another question, and this is also kind of in the theme of thinking about having a great, strong presence on Apple Watch. And this is from Prechaya Tries. I think it's Tries, like, yay. Okay, cool. The question is, what do you think are the things that most app developers might not know that they can do on watchOS that will make them want to extend their

228
00:29:30 --> 00:29:41
app to the device? For example, a long time ago, they were interested in making a VisionOS tennis game using the sensor on Apple Watch for the racket, but it didn't seem possible at the time.

229
00:29:42 --> 00:30:04
So yeah, what do you think people like, what do you, what do you think are some of the really awesome opportunities on Apple Watch? It's interesting because I wonder what was missing for them then. I think that there's actually a possibility for doing that as a peripheral for Vision OS. Going back to the earlier thing about the watch being on your wrist and having all that

230
00:30:04 --> 00:30:28
health data and everything there's also um cm sensor data and stuff like that so you can actually see i'm being like core motion yes sorry core motion sensor data for the other folks in the room yeah little little street lingo yeah yeah so you could um uh track the motion of the device while you're doing things uh which could be cool for anything from a tennis thing to i don't know

231
00:30:28 --> 00:30:51
maybe you're into pottery and you wanted to see how steady your hand is as you're forming the vase i could see that being interesting so i think there's a lot of possibilities of novel experiences yeah i think some of the the great greatest watchos experiences are the ones that are kind of like proactively brought to you and so i'll keep on talking about live activities because i

232
00:30:51 --> 00:31:04
think that's a great one but you know there's a lot of these really awesome kind of outside of your app experiences that we try to bring proactively uh to users when the time is right And so that's really the promise of the smart stack.

233
00:31:04 --> 00:31:08
And relevant widgets are a really great way to do that as well.

234
00:31:08 --> 00:31:13
So for people, I realize we're also throwing out some vernacular that might be unfamiliar to people.

235
00:31:13 --> 00:31:19
Like what makes a great live activity or what makes something meaningful in the smart stack and most relevant?

236
00:31:19 --> 00:31:24
Like what are some of the qualities people should think about when exploring these experiences?

237
00:31:25 --> 00:31:27
I'll start with widget suggestions, yeah.

238
00:31:28 --> 00:31:33
So I think with widget suggestions, we really just care about, you know, what is the user or what is the customer doing currently?

239
00:31:34 --> 00:31:36
You know, like what is their real world context around them?

240
00:31:37 --> 00:31:45
And when you register your widget to become relevant, you associate it with like a location or a date or sometimes like semantic locations, like when the person's at work or at home.

241
00:31:46 --> 00:31:53
So just making sure that, you know, what you're showing is really relevant for their real world situation around them.

242
00:31:53 --> 00:31:54
I mean, your watch is something on your wrist.

243
00:31:54 --> 00:32:05
It's in the moment with you, and the things we're showing you should help give you information about that or help you unlock your front door when you get home, like actions that you can take around you and being accelerant to that.

244
00:32:07 --> 00:32:13
Yeah, I think the other advice there is try to avoid being in that space too long, actually.

245
00:32:14 --> 00:32:18
You might find that users begin dismissing your app if you're taking up that top spot too often.

246
00:32:19 --> 00:32:25
So, yeah, just being judicious about when is the right time to surface this thing, what might the user want to do with pretty good confidence.

247
00:32:25 --> 00:32:37
Like I think of, when I think of live activities on watch, I think of the mango cart app and that you made in a video from, was that WWDC 25?

248
00:32:39 --> 00:32:39
Yes.

249
00:32:40 --> 00:32:40
I see.

250
00:32:40 --> 00:32:41
It was 25.

251
00:32:41 --> 00:32:41
All here.

252
00:32:42 --> 00:32:44
Bring your live activity to Apple watch.

253
00:32:45 --> 00:32:45
Yes.

254
00:32:45 --> 00:32:49
And so like talk like that live activity was a mango delivery.

255
00:32:49 --> 00:32:50
It was produce shopping.

256
00:32:50 --> 00:32:51
Produce shopping.

257
00:32:51 --> 00:32:51
Yes.

258
00:32:52 --> 00:32:53
Produce delivery.

259
00:32:53 --> 00:33:04
And so that was, yes, you could go shop for your produce box and get it delivered to your house and it would show the status of your delivery.

260
00:33:04 --> 00:33:10
So you've ordered and how long is it going to take to get to you, the progress of your delivery.

261
00:33:10 --> 00:33:14
And meaningfully, it's not just there all day, like five hours after it's delivered.

262
00:33:14 --> 00:33:16
It's like, okay, it's been delivered.

263
00:33:17 --> 00:33:19
Now it's gone, right?

264
00:33:19 --> 00:33:19
Yes.

265
00:33:19 --> 00:33:22
It's like kind of the kind of thing we should be thinking about a lot.

266
00:33:22 --> 00:33:22
Right.

267
00:33:22 --> 00:33:26
I think with live activities, too, you have the opportunity to have, like, notifying updates and non-notifying updates.

268
00:33:26 --> 00:33:32
So I think there can be a difference between things you really want to alert about, like, your delivery's arrived right now.

269
00:33:32 --> 00:33:32
I need to go outside and get it.

270
00:33:33 --> 00:33:33
Definitely alert for that.

271
00:33:34 --> 00:33:37
Small status updates, you know, can be something you passively track.

272
00:33:37 --> 00:33:40
So just be mindful of, like, what's the important piece of information here?

273
00:33:40 --> 00:33:41
What's the signal worth alerting for?

274
00:33:42 --> 00:33:47
That's something that we spent actually quite a lot of time thinking about when we worked with the sports team to develop.

275
00:33:47 --> 00:33:50
They are working on bringing live activities to the sports app.

276
00:33:51 --> 00:33:54
We want to have alerting live activities.

277
00:33:54 --> 00:34:00
They don't alert for every score, though, because you look at every sport, and they don't necessarily mean the same thing.

278
00:34:00 --> 00:34:03
And so basketball, they alert at the quarters.

279
00:34:03 --> 00:34:05
Soccer, they alert every score.

280
00:34:06 --> 00:34:14
And so these kinds of like thinking about when and how is the right time to alert for a live activity is super important to the watchers experience in particular.

281
00:34:15 --> 00:34:25
because we actually bring the smart stack back to the forefront and give you an opportunity to draw something really cool or show a great update, and then the user can dismiss it.

282
00:34:26 --> 00:34:28
You can get more updates in the background as the game's progressing.

283
00:34:29 --> 00:34:33
And then a big score, you do another learning activity, learning update.

284
00:34:34 --> 00:34:40
And kind of going in this direction a little bit more, I think we've also thought a lot about notifications versus suggestions in the smart stack.

285
00:34:40 --> 00:34:44
And sometimes it makes sense to present information to a user but not necessarily alert.

286
00:34:44 --> 00:34:47
and we've done a noise suggestion this year.

287
00:34:48 --> 00:34:52
So I think a little bit before you normally get the notification about high noise, you'll get a suggestion in the smart stack.

288
00:34:53 --> 00:34:54
So it's more like a soft reminder.

289
00:34:55 --> 00:35:01
We're not quite ready to alert you about it yet, but we know something's going on here and it's worth elevating.

290
00:35:02 --> 00:35:24
So the best place to learn how to make these decisions, like if I'm making a sports app and saying, oh, this is how often we should notify people with the live activity, like what is the best resource to learn about how to make these decisions i'd say probably the developer website yeah anything i think you know the the tying these together end to end is something

291
00:35:24 --> 00:35:46
that we kind of leave to the developer audience to figure out how this should all fit together but we want to every one of these tools serves a unique purpose in and of itself and and we found great ways to pull them together to build some of our experiences what we provide from the apps already on your watch uh or when we work with uh apple developers that build like the sports app

292
00:35:46 --> 00:36:08
uh and when and how do they provide notifications versus live activities versus a smart stack widget it's something to look at and get inspired by and hopefully that you too can take that and run with it and find really awesome ways to use that data yourself that was like great api so So we don't want to be too prescriptive, I think, is the answer.

293
00:36:08 --> 00:36:13
Like, they have all these tools, and they're for you to figure out how to pull them together.

294
00:36:13 --> 00:36:14
Yeah, I think it's very demand-specific.

295
00:36:15 --> 00:36:16
Yeah, I think, yeah, it is.

296
00:36:16 --> 00:36:23
Ultimately, you as a developer know your app and your domain, and you will know.

297
00:36:23 --> 00:36:25
You figure out what's right for you.

298
00:36:25 --> 00:36:25
Yeah.

299
00:36:26 --> 00:36:27
Absolutely.

300
00:36:27 --> 00:36:38
And I will say, like, I think times of year like WWDC when we get the chance to speak to the developer community and hear about the problems that they're facing or the things they're trying to solve or the questions they have.

301
00:36:38 --> 00:36:43
I think that also informs the direction of our frameworks and which wrinkles we iron out.

302
00:36:43 --> 00:36:45
It's a great feedback loop that we have.

303
00:36:45 --> 00:36:59
And I think it's a challenge that we have as Apple platform developers is to find the right entry points and the tweaks that we want you as developers to make for your specific use cases.

304
00:36:59 --> 00:37:01
That's what makes a really great API.

305
00:37:01 --> 00:37:09
It's flexible enough that you can go do some really amazing things, and we try to build it with that in mind.

306
00:37:10 --> 00:37:14
I mean, it's a very noble goal, and I think I'm happy to be a part of it.

307
00:37:14 --> 00:37:15
It makes me smile.

308
00:37:18 --> 00:37:22
Okay, I have another question.

309
00:37:24 --> 00:37:26
There's so many good ones.

310
00:37:26 --> 00:37:31
Okay, you all have excellent questions.

311
00:37:31 --> 00:37:33
I love that one about new liquid glass.

312
00:37:34 --> 00:37:36
That's something that I was really excited about.

313
00:37:36 --> 00:37:38
You know, yeah, let's talk liquid glass.

314
00:37:38 --> 00:37:41
We haven't given it any love because, yeah, it's time.

315
00:37:42 --> 00:37:46
Okay, this is a question from Simon from Helix.

316
00:37:47 --> 00:37:50
What's new for liquid glass specifically on watchOS?

317
00:37:50 --> 00:37:52
Let's talk glass.

318
00:37:52 --> 00:37:53
Yeah, let's talk glass.

319
00:37:54 --> 00:37:55
I'll dive into it.

320
00:37:56 --> 00:38:02
So we were really excited with watchOS 26 to bring liquid glass and the new design language to Apple Watch.

321
00:38:03 --> 00:38:09
And there has been a lot of refinement we've done even since that initial release.

322
00:38:09 --> 00:38:19
So something that we've brought in watchOS 27 is the updated look with more of the dark edges, dark speculars, what we call them.

323
00:38:19 --> 00:38:28
Some nicer like separation of content as stuff scrolls so that you are able to more easily preserve legibility in your app.

324
00:38:28 --> 00:38:31
As well as just like a lot of really great performance improvements.

325
00:38:32 --> 00:38:34
Just something that we've noticed in our own use.

326
00:38:35 --> 00:38:41
And so we worked really, really hard on making the liquid glass experience really shine on the platform.

327
00:38:42 --> 00:38:50
I think my favorite thing is just, like, using the really great, like, reactive glass experiences where you, like, drag the buttons and stuff.

328
00:38:50 --> 00:38:51
With the interactive glass.

329
00:38:51 --> 00:38:52
Yeah, with the interactive glass.

330
00:38:52 --> 00:38:53
That is really fun to use.

331
00:38:53 --> 00:38:54
It's, like, just fun to play with.

332
00:38:54 --> 00:38:58
And we're, like, excited to have that in watch and throughout the whole system.

333
00:38:59 --> 00:39:06
So it is something that comes to a lot of apps automatically on watchOS especially.

334
00:39:06 --> 00:39:15
We found that our design system that we brought in watchOS 10 really naturally leaned into where we were going with the new design language.

335
00:39:15 --> 00:39:22
And so for a lot of your apps, the Liquid Glass updates even last year and this year are already there for you.

336
00:39:23 --> 00:39:29
And so, you know, we're excited to continue to work with you and the developers and provide feedback again.

337
00:39:29 --> 00:39:47
Like I think if anything that this year and the kind of how we were talking about all the operating systems with these really great performance and improvements that we've made is all about that feedback loop from developers and users coming back to us.

338
00:39:47 --> 00:39:53
And I think Liquid Gloss and the updates there are really great refinement on that system.

339
00:39:53 --> 00:40:12
Yeah, I think like the feedback channel for Liquid Glass in all the platform 26s, the developer relations team met with developers around, really around the world, getting the feedback, seeing which areas were either confusing or challenging or weren't meeting the needs of all of the users.

340
00:40:12 --> 00:40:16
And that really informed the direction for iOS and watchOS 27.

341
00:40:16 --> 00:40:28
Yeah, I think the What's New in Swift UI session this year, too, gives you some really great new tools to allow them to come to watch as well for you to customize and make sure Liquid Glass is working for you and your app.

342
00:40:28 --> 00:40:43
And I have found it delightful when I see the apps I know and love update with Liquid Glass, and it feels really cohesive to the system, and it makes me, like, especially as I go between different platforms, I'm like, I can see that consistency.

343
00:40:44 --> 00:40:45
It's a more delightful experience.

344
00:40:46 --> 00:40:48
I have two follow-up questions.

345
00:40:48 --> 00:40:56
One, is there anything different that developers need to do for watchOS 27 to integrate or to use Liquid Glass effectively in their apps?

346
00:40:56 --> 00:40:58
For the most part, no.

347
00:40:58 --> 00:40:58
Okay.

348
00:40:58 --> 00:41:00
Which is actually, I think, a great answer.

349
00:41:00 --> 00:41:01
Yeah.

350
00:41:01 --> 00:41:03
I mean, that sounds good to me.

351
00:41:03 --> 00:41:03
Yeah.

352
00:41:03 --> 00:41:19
Yeah. For a lot of our developers out there, especially ones that have been developing with the watchOS 10 design language that we brought a couple of years ago, the new glass designs and the updates we've made come to you automatically.

353
00:41:20 --> 00:41:20
That is really cool.

354
00:41:21 --> 00:41:21
Yeah.

355
00:41:21 --> 00:41:33
And then my second question, the settings, the personal settings that someone makes on iOS for how clear or tinted the glass is, does that have an equal presence on watch?

356
00:41:33 --> 00:41:36
Is there also a watch level preference or is it synced across devices?

357
00:41:36 --> 00:41:38
It's actually we don't provide one for watch.

358
00:41:39 --> 00:41:42
And if you really feel like we need one, please give us that feedback.

359
00:41:42 --> 00:42:06
I think it was a decision that we made for the platform that we felt that because our user interface is primarily dark, that the layout and the smaller screen size meant that we felt like we could really hit that balance of preserving legibility and still give the the feel of liquid glass without needing a slider to let users pick where they felt like

360
00:42:06 --> 00:42:32
there's the right balance for them but you know we're here if you have more suggestions of course we also have all the accessibility features uh reduce transparency increase contrast there for you on watch as well if you need those but uh we think that the the platform uh is like uh as as Designed is in a good place. No, that's great to know like like frameworks liquid glass is tailored to watch OS

361
00:42:32 --> 00:42:54
So that I think that's a really nice sentiment. Thank you I'm seeing a lot of love for watch OS 27. I'll just say a quick shout out a Shafri said we love watch OS. Thank you all for your hard work. I'll say that to you. Thank you and Asked what is something new in watch OS 27 that you think is a hidden gem?

362
00:42:54 --> 00:42:59
And I think we've covered that with the new features, but I just wanted to thank you for the love of the platform.

363
00:42:59 --> 00:42:59
I like that one.

364
00:43:00 --> 00:43:01
Oh, you can drive number three.

365
00:43:01 --> 00:43:02
I can just expand on it a little bit.

366
00:43:02 --> 00:43:16
Well, I mean, we talked about this already with the Zones API, but I think unlike foundation models, which is like this huge, beautiful feature that everyone is really excited about, Zones is something that has actually been requested for years.

367
00:43:16 --> 00:43:35
And it took us a while to get to the point where we shipped an API that we think is actually really flexible and really easy to use and provides a lot of value that I think we learned by using it internally for a little bit first. So I think in terms of a hidden gem, I think that one fits. I have a hidden gem.

368
00:43:36 --> 00:43:38
Share the hidden gem. Good question.

369
00:43:38 --> 00:43:40
I'm sorry I was going to move past that.

370
00:43:40 --> 00:43:42
Don't move past this. We have hidden gems.

371
00:43:44 --> 00:43:47
My favorite hidden gem is the reorderable API. Yes.

372
00:43:48 --> 00:43:50
What? Yeah. Say more.

373
00:43:50 --> 00:43:54
New in SwiftUI this year is a new API for reorderable.

374
00:43:55 --> 00:43:56
And it was mentioned in the State of the Union.

375
00:43:56 --> 00:43:57
Yes.

376
00:43:57 --> 00:43:59
But it is also available on Watch.

377
00:44:00 --> 00:44:05
And so we allow developers to really easily have collections that they drag around on the platform.

378
00:44:05 --> 00:44:09
We actually had used it ourself to implement Control Center.

379
00:44:10 --> 00:44:16
So we are really excited that this is something that we can see in your apps as well now.

380
00:44:16 --> 00:44:17
So, yeah, reorderable.

381
00:44:17 --> 00:44:18
That's my hidden gem.

382
00:44:18 --> 00:44:23
This is the first time developers can reorder containers on WatchOS ever.

383
00:44:23 --> 00:44:24
We are so excited.

384
00:44:25 --> 00:44:25
Wow.

385
00:44:25 --> 00:44:25
Okay.

386
00:44:25 --> 00:44:28
I'm really glad we dove deeper here into this.

387
00:44:29 --> 00:44:30
Devin, do you have another hidden gem?

388
00:44:31 --> 00:44:31
I don't think so, no.

389
00:44:33 --> 00:44:34
Only public gems.

390
00:44:34 --> 00:44:35
Only public gems.

391
00:44:35 --> 00:44:39
I want to shout out more to Heart Rate Zones because, Dan, you've opened the can of worms.

392
00:44:40 --> 00:44:41
And I'm really excited about Heart Rate Zones.

393
00:44:42 --> 00:44:43
And Power Zones.

394
00:44:43 --> 00:44:43
Power Zones.

395
00:44:43 --> 00:44:44
Don't forget the cyclists.

396
00:44:44 --> 00:44:44
And cycling.

397
00:44:45 --> 00:44:45
I know.

398
00:44:45 --> 00:44:47
Cyclists in the room are saying, come on.

399
00:44:47 --> 00:44:52
There were like intricacies around time in zone, which is actually something that's really interesting.

400
00:44:52 --> 00:44:55
So a lot of people were just asking, just give me the boundaries.

401
00:44:56 --> 00:45:06
And we've been able to provide something that's actually much richer for them and still incredibly flexible where you can choose different zones for your specific workout app as well, which is, I think, really cool.

402
00:45:07 --> 00:45:07
It is super cool.

403
00:45:07 --> 00:45:16
And what I also like is you don't have to be just a workout app, but just a workout app to leverage the new workout zones API.

404
00:45:16 --> 00:45:21
Because there's the aspect of what zone is someone in during a workout, how hard are they working.

405
00:45:21 --> 00:45:27
But there's also the after-workout experience of talking about, you know, how long was someone in a high heart rate zone during the week's workouts.

406
00:45:27 --> 00:45:32
There's so many opportunities to think, and especially leveraging more intelligent experiences.

407
00:45:32 --> 00:45:34
There's a lot of data that you could do cool stuff with.

408
00:45:34 --> 00:45:37
So I'm really excited to see how people use it.

409
00:45:38 --> 00:45:39
Great question.

410
00:45:39 --> 00:45:39
Okay.

411
00:45:40 --> 00:45:46
We have a question about widgets that I'm excited for from Medium Fidelity.

412
00:45:47 --> 00:45:47
It's very cool.

413
00:45:47 --> 00:45:52
I'm thinking of the different family configurations for widgets.

414
00:45:52 --> 00:45:54
Okay, the supported families.

415
00:45:55 --> 00:46:01
Okay, does watchOS 27 contain any changes that alter guidance for keeping a widget up to date?

416
00:46:02 --> 00:46:06
Are we looking at about 50 widget, parentheses, complications, updates per day?

417
00:46:06 --> 00:46:11
That's regardless of if they're the background app refresh or APNS-based, right?

418
00:46:11 --> 00:46:15
And I think there's a few things to talk about here, the notion of widget budgets.

419
00:46:15 --> 00:46:21
But I think maybe the secret question buried underneath is how do I keep my widget up to date?

420
00:46:21 --> 00:46:23
What things should I keep in mind?

421
00:46:23 --> 00:46:24
Is a widget the right thing for me?

422
00:46:25 --> 00:46:26
Should I use something else?

423
00:46:26 --> 00:46:30
So, Devin, I'll kick things off with you to get into medium fidelity's question.

424
00:46:30 --> 00:46:30
Yeah.

425
00:46:31 --> 00:46:37
So, I mean, off the bat, I don't think we have much new guidance for watchOS 27, but I can reinforce just good practices.

426
00:46:38 --> 00:46:43
So, yeah, typically, I mean, I think you can expect your widget to update every 15 to 20 minutes if it's being actively used.

427
00:46:44 --> 00:46:48
And I think there's a big difference there between whether your widget resides on the watch face or the smart stack.

428
00:46:49 --> 00:46:59
Things on the watch face are getting viewed all day, so they're at the top tier of the budget, and they will be viewed favorably by the scheduler and be more guaranteed to get updates.

429
00:47:00 --> 00:47:02
I think updates can come in a few different ways, too.

430
00:47:02 --> 00:47:09
You can invalidate your widget from your app, and you have some novel information, and you need to tell the system that it's time to get reloaded.

431
00:47:10 --> 00:47:15
But there's also your timeline, and it has a reload policy attached to it that gives it an expiration.

432
00:47:16 --> 00:47:19
And that's also a very powerful and slightly more deterministic way to make sure you get refreshed.

433
00:47:20 --> 00:47:27
We might hold a stale timeline for a little bit, but there's stronger guarantees that we'll give you a refresh after that.

434
00:47:28 --> 00:47:43
For the smart stack, I think that if the user's added the widget to the smart stack, again, kind of in that similar bucket of getting background refresh through the day, but that background refresh is not going to be quite in that 15- and 20-minute bucket because it depends on how often the user actually goes to look at your widget.

435
00:47:43 --> 00:47:46
This is similar to on the iOS.

436
00:47:46 --> 00:47:51
If you have a widget on a home screen page, a few pages away, you'll get about the same behavior.

437
00:47:51 --> 00:47:55
If the user looks at it once a day, you might get a refresh once a day.

438
00:47:56 --> 00:48:00
I think you dropped a lot of important information.

439
00:48:00 --> 00:48:01
There's a lot in there.

440
00:48:01 --> 00:48:02
There's a lot there.

441
00:48:02 --> 00:48:02
There's a lot of nuance.

442
00:48:02 --> 00:48:03
There's a lot of nuance.

443
00:48:04 --> 00:48:19
The things that stand out to me are that the system, not just on watchOS, on iOS, on all platforms that have widgets supported, the widget budget of updating is optimized for both the platform and how often people are engaging with your widget.

444
00:48:20 --> 00:48:25
There's a lot of really great documentation about this, that medium fidelity, the content of your question actually includes.

445
00:48:25 --> 00:48:30
It's an article on the developer website called Keeping Your Widget Up to Date.

446
00:48:30 --> 00:48:34
So I think that is a really good resource to continue referencing.

447
00:48:34 --> 00:48:55
There's also, we talked about it briefly, there's a new Widget Kit Foundations video from WWDC 26, and that's going to cover all the different techniques to keep a widget up to date And the foundations, if you're like, what are they talking about, this is a really good resource to just learn the basics to not only build your first widget but think about how to keep it up to date and the different tools at your disposal.

448
00:48:56 --> 00:49:03
And similar to everything we've talked about, we love to hear what's working and what is an area of friction when using things like that.

449
00:49:03 --> 00:49:14
I think something that we've seen, especially when we've worked with developers on adopting widgets, is sometimes you naturally reach for like the first thing is not necessarily the right tool for the job.

450
00:49:14 --> 00:49:28
And so we provide a lot of different types of APIs for widgets and live activities and controls available through the WidgetKit framework that we hope kind of hits at the various different needs of developers out there.

451
00:49:28 --> 00:49:30
Of course, if there's something missing, provide the feedback.

452
00:49:30 --> 00:49:33
We want to hear it, especially with your use case.

453
00:49:33 --> 00:49:40
I think that's something that helps us a lot to understand where and how and why you want to have this thing that does not exist.

454
00:49:41 --> 00:49:45
But I think there's a couple different types of widgets out there.

455
00:49:45 --> 00:49:51
Our typical timeline-based widgets are really great for data that updates throughout the day.

456
00:49:51 --> 00:49:53
I think a canonical example is weather.

457
00:49:54 --> 00:49:59
It changes, obviously, frequently, but you have a forecast.

458
00:49:59 --> 00:50:01
And so that's a really natural fit for a timeline.

459
00:50:02 --> 00:50:03
Calendar is another great example.

460
00:50:03 --> 00:50:05
It's a natural fit for a timeline.

461
00:50:06 --> 00:50:28
We've found mismatches, though, where some people want something that's a little more immediate it and maybe a little more timely and that's where the relevant configuration comes from you say this widget is relevant at this place time location uh and when those things are met then we give you the runtime to go update the widget and provide the information in that moment if you

462
00:50:28 --> 00:50:53
have something that's more like a session a sports game a ride share that has a more obvious beginning an end, live activities are a really great fit for that. You get substantially more runtime in order to provide substantially more updates for that fixed amount of time, knowing that when it's over, that runtime goes away. But in the moment, we want you to have a really live experience.

463
00:50:54 --> 00:51:22
And if you want to provide, like, I want to perform an action or, you know, let the user just do something quickly that's what controls are for so uh if you try to frame the uses of like what you're looking for within each of these types of apis i think you'll find more success trying to tailor the experience to the right tool yeah i think on your point about um there's just a

464
00:51:22 --> 00:51:35
bunch of different data models for different apps and i'm constantly i don't surprise and challenged by the different ways we find that apps have different requirements and different ways to refresh um and we think we've covered a lot of the bases with the different options we have we have a lot of different ways to get down to the watch face and the control center and

465
00:51:35 --> 00:51:38
the smart stack and all these things look the same.

466
00:51:38 --> 00:51:41
So yeah, I think you've got to try the different models.

467
00:51:41 --> 00:51:47
I think even internally we pivot a lot between different ones and you've just got to find what works for you.

468
00:51:47 --> 00:51:52
I think that's something that we also find iteratively with our own widgets internally.

469
00:51:52 --> 00:52:11
We sometimes start off with one technology and as we are working on the design and how it feels, we decided actually this is a better fit for relevant configuration and we started off with a timeline widget and that's okay that's like the fun part of the discovery you mentioned the like cool thing about the constraints of the platform right like it's a journey it's a journey

470
00:52:11 --> 00:52:30
and so uh you have to kind of be open to that yeah but all that being said i mean if you feel like there's some case that you you have that isn't served well i mean we're always open to feedback and trying to learn more about what those cases are you know you know new suggestions or new conditions we should make suggestions and and new reasons for uh for refreshes yeah well said i love

471
00:52:30 --> 00:52:48
learning the use case of developers because that really helps us get to the heart of the problem one more thing on this oh this isn't necessarily new to this year but last year in watch os 26 we shipped uh apns based uh widget refreshes yeah i think that's part of the original question um so that's a whole new path to being able to uh you know from a server uh push new information to

472
00:52:48 --> 00:53:07
your widget and get a refresh and now in this year we have watch connectivity based that's right yeah so we we are continuing to try to find more and more opportunities to provide the right signals into the system to provide that like update we want your widgets to also feel fresh too uh and so it's just a matter of we mentioned the constraints of the system it's a matter of

473
00:53:07 --> 00:53:26
finding that right balance so whether it's being pushed from the phone it's being pushed from a server it's right driven by background app refresh or you have a timeline like these are all the various different tools we provide and again going back to what we were saying earlier like we want to provide all these different tools and opportunities

474
00:53:26 --> 00:53:29
for you to go find the right way to combine them together.

475
00:53:29 --> 00:53:32
And if you just can't quite find that right fit, that's the right time to come to us.

476
00:53:33 --> 00:53:35
That was beautifully said. I love it.

477
00:53:35 --> 00:53:42
Okay, we're nearing the end of our time together, so I'm going to look at one more question to round things out.

478
00:53:43 --> 00:53:46
And there are so many great questions that I really...

479
00:53:46 --> 00:53:47
There are so many great questions.

480
00:53:47 --> 00:53:55
I know, I'm like, wow, thank you for being engaged, and I would love to engage more on the Apple Developer Forums after this.

481
00:53:55 --> 00:54:00
Wow, thank you all for this.

482
00:54:00 --> 00:54:02
I'm really excited.

483
00:54:02 --> 00:54:05
There's a lot of we love watchOS, this is really nice.

484
00:54:05 --> 00:54:06
Okay. - We have to love watchOS.

485
00:54:06 --> 00:54:08
- We all love watchOS.

486
00:54:08 --> 00:54:08
- Who else loves it?

487
00:54:08 --> 00:54:09
I love it.

488
00:54:11 --> 00:54:12
There is a, okay.

489
00:54:12 --> 00:54:17
LazyVar, I think. - We have to end with LazyVar. - We have to end with LazyVar. - Yeah, LazyVar.

490
00:54:17 --> 00:54:22
- I wish I had like a special badge on the screen to give excellent questions today, all of you.

491
00:54:22 --> 00:54:25
And thank you for upvoting your favorite questions.

492
00:54:25 --> 00:54:28
This is the last one for our time together this time around.

493
00:54:28 --> 00:54:29
Okay.

494
00:54:29 --> 00:54:41
LazyVar asks, what are the best practices for optimizing heavy asset fetching on a standalone watchOS app's first launch without relying on watch connectivity?

495
00:54:42 --> 00:54:49
I'd want to keep the initial sync fully independent of iPhone, but struggle with the watch's radio scheduler limitations.

496
00:54:52 --> 00:54:52
Oh, goodness.

497
00:54:53 --> 00:55:01
You know, I think you really think about what you need to get.

498
00:55:02 --> 00:55:03
What can you bundle?

499
00:55:03 --> 00:55:19
Because you're going to have to, you know, you can use background URL to get things, but you're going to be, you know, in the background that first launch because you don't have any time before that first launch to do anything.

500
00:55:19 --> 00:55:33
So it's like really think about, you know, what you can do to provide the things that you instantly need on first launch beforehand to provide a good experience while you're getting those other assets that you need.

501
00:55:33 --> 00:55:46
So, you know, provide something for people so that they can start doing something, see something and not see a spinner until they get the other things that they need.

502
00:55:47 --> 00:56:00
So, you know, really think about what are those assets that you need to download versus what can you get and what other experience can you give them that's useful to them in that moment while you're getting those other things?

503
00:56:01 --> 00:56:07
I think we're all smiling because this is like a canonical Apple Watch challenge.

504
00:56:07 --> 00:56:09
But that's what we need to talk about.

505
00:56:09 --> 00:56:10
No, I think it really is.

506
00:56:10 --> 00:56:11
This gets down to it.

507
00:56:11 --> 00:56:14
It's what we think about a lot for the experiences that we build, right?

508
00:56:15 --> 00:56:19
And it's kind of the magic trick of working with those constraints.

509
00:56:20 --> 00:56:23
Like, yeah, it is a constrained environment.

510
00:56:23 --> 00:56:26
The connectivity is a challenge.

511
00:56:26 --> 00:56:30
Getting the runtime you need is an obstacle.

512
00:56:30 --> 00:56:44
But like Anne was saying, it's about trying to find the right way to craft a user experience that masks the uncomfortable things that we all have to do as developers to get the assets downloaded on disk and still provide a delightful experience for your users.

513
00:56:44 --> 00:56:46
ANNE RADWAY: Yeah, and you may not have connectivity.

514
00:56:46 --> 00:57:09
The first launch of that app may be someplace out in the back country, because we all love our our ultras and our our series watches and we go off the grid with them and they may launch your app and have no connectivity and what are they gonna see and so we as watch app developers think about what happens what happens when i don't have anything and i'm completely

515
00:57:09 --> 00:57:31
offline what should i do now i i love it's a it's a constraint and it's also an opportunity for something that's truly delightful and forward thinking and making the best watch app experience and i think those are the moments that really stand out on the platform when it's so thoughtful um and you know i said that was the last question but i have a personal question for me i don't have

516
00:57:31 --> 00:57:52
a cool username yet but i'll work on that uh i would like to see what watch bands you guys are all showing off because i i'm going with the black one it's very cool um but i don't know i think like let's see what and you're wearing too i feel like you got it you got to show off for a second okay So, I've got the Matchy Matchy Lume and Unity Bloom because it also matches.

517
00:57:52 --> 00:57:55
I love the colors, and this is one of my favorite watch bands.

518
00:57:55 --> 00:57:55
Nice.

519
00:57:55 --> 00:57:57
I like the double, like a superhero.

520
00:57:58 --> 00:57:58
It's cool.

521
00:57:59 --> 00:58:02
It's like, it's how you really know testing the code.

522
00:58:03 --> 00:58:03
That's awesome.

523
00:58:04 --> 00:58:04
Cool.

524
00:58:05 --> 00:58:09
Well, seriously, thank you all for your questions.

525
00:58:09 --> 00:58:11
This has been so much, I've had a lot of fun.

526
00:58:12 --> 00:58:13
I think I can speak for all of you.

527
00:58:13 --> 00:58:14
I know you've also had fun.

528
00:58:15 --> 00:58:15
Have you had fun?

529
00:58:15 --> 00:58:16
I've also had a lot of fun.

530
00:58:16 --> 00:58:18
This has been very rewarding for me.

531
00:58:18 --> 00:58:25
It's really fun to talk about WatchOS, talk about the qualities that make your apps so excellent and keep people coming back from the platform.

532
00:58:26 --> 00:58:37
I love trying your apps and not just hearing about your triumphs, but also hearing about the things you're still trying to figure out and learn and pushing each framework to be better on Apple Watch.

533
00:58:37 --> 00:58:45
So I love, we love hearing your feedback and learning about the cool stuff that you're building through all of the channels that we get to connect with.

534
00:58:45 --> 00:58:48
So thank you so much for being a part of the Watch developer community.

535
00:58:49 --> 00:58:57
It is such a special platform that makes people's everyday lives better through convenient interactions, staying healthy and connected.

536
00:58:58 --> 00:59:00
I want to say thank you to our lovely panelists.

537
00:59:00 --> 00:59:01
Thank you.

538
00:59:01 --> 00:59:02
This was really awesome.

539
00:59:02 --> 00:59:03
Thank you.

540
00:59:03 --> 00:59:04
Oh, thank you.

541
00:59:04 --> 00:59:05
Thank you for being such a great host.

542
00:59:05 --> 00:59:06
Yeah, thank you.

543
00:59:06 --> 00:59:07
This was really fun.

544
00:59:07 --> 00:59:11
Thank you to our support staff helping us go through all the questions.

545
00:59:11 --> 00:59:33
If we didn't get the chance to answer your question today, that's okay. We're still here in different formats. Please visit the Apple Developer Forums at developer.apple.com slash forums, where we'll continue the discussion. We've had great watchOS and health and fitness Q&As throughout the week at DubDub. And we also have people

546
00:59:33 --> 00:59:45
throughout the year who are checking in on your questions. It's a really great way not just to ask questions, but to learn from other people's questions. It's a really great learning resource. Sometimes you're hitting a wall and someone else has figured it out.

547
00:59:46 --> 01:00:05
So I really encourage you to use that. And if you have feedback, whether you're running into bugs or have enhancement requests for new stuff you want to do, check out Feedback Assistant at apple.com. So we really appreciate your feedback. And we want to bring, the big theme is we want to bring the best developer experience to you. So thanks again for joining us. And we

548
01:00:05 --> 01:00:06
hope you have a great WWDC.
