---
title: App Store Connect Group Lab
source: https://developer.apple.com/videos/play/wwdc2026/8010/
session: 8010
collection: wwdc2026
duration: 01:01:56
fetched: 2026-06-12
via: whisper.cpp ggml-large-v3 (session SD video audio)
transcription: machine-generated, no speaker diarization
---

> Machine transcription of the **App Store Connect Group Lab** SD video via whisper.cpp (`ggml-large-v3`).
> Group labs ship no Apple transcript; this is generated from the audio. Cues are timestamped segments, **not** speaker-attributed turns.

## Transcript

1
00:00:03 --> 00:00:07
Hello, hello, and welcome to the App Store Connect group lab.

2
00:00:07 --> 00:00:11
I'm Laurel, and I'm from the App Store Connect engineering team.

3
00:00:12 --> 00:00:15
App Store Connect is the developer gateway to the App Store.

4
00:00:15 --> 00:00:21
We provide helpful tools for developers to launch their apps, get discovered, and build their businesses.

5
00:00:21 --> 00:00:25
I've got a fantastic group of experts here with me today.

6
00:00:26 --> 00:00:31
I also have to shout out our most excellent group behind the scenes that's supporting us.

7
00:00:33 --> 00:00:36
Let me go around and introduce my panel.

8
00:00:36 --> 00:00:44
I'm going to have everybody say their name, what they work on, and their favorite feature that was announced this week for developers.

9
00:00:45 --> 00:00:46
Jeff, you can go first.

10
00:00:46 --> 00:00:46
Excellent.

11
00:00:47 --> 00:00:48
I'm Jeff.

12
00:00:48 --> 00:00:50
I'm an engineering program manager on App Store Connect.

13
00:00:50 --> 00:00:57
And my favorite feature from this week was adding in-app purchases to the enhanced submission experience.

14
00:00:58 --> 00:01:16
It's been a long time coming, and we're finally bringing everything together where developers will have even more control over what they submit together, what gets reviewed together, and also be able to see messaging all in the same place for their app, their app's content like in-app events, and now their in-app purchases too.

15
00:01:17 --> 00:01:18
It's very exciting.

16
00:01:18 --> 00:01:18
Yeah.

17
00:01:18 --> 00:01:20
My name is Lydia.

18
00:01:20 --> 00:01:23
I'm a quality engineer on App Store Connect.

19
00:01:24 --> 00:01:29
I'm really chuffed about the addition of localization for background assets.

20
00:01:30 --> 00:01:40
I think it's going to be great that developers can add localizations, but customers don't have to download all of the localizations, just the ones that they need for their localization.

21
00:01:40 --> 00:01:41
Very nice.

22
00:01:41 --> 00:01:58
My name is Nick, product manager for the App Store, and I'm really excited about all the stuff coming in Xcode related to AI, which makes it easier for anybody who maybe doesn't even know how to code very much to develop a really high-quality app, submit it to the App Store, and bring it live to the world.

23
00:01:59 --> 00:02:02
Hello, everyone. I'm Shobi. I'm an Apple engineer.

24
00:02:02 --> 00:02:12
As a developer myself working on TestFlight and AppStore Connect apps, I'm super excited about using custom images and videos for the product page headers and search results.

25
00:02:12 --> 00:02:17
This is going to enhance my app's presence on AppStore, and I'm pretty thrilled about it.

26
00:02:17 --> 00:02:18
It's pretty cool.

27
00:02:18 --> 00:02:19
It is.

28
00:02:19 --> 00:02:24
Hi, I'm Dave, part of the AppStore engineering, working on TestFlight and AppStore Connect.

29
00:02:24 --> 00:02:35
And I think my favorite feature that we announced is the Asset Library in AppStore Connect, a way of managing your images and using them across different placements.

30
00:02:35 --> 00:02:37
So pretty excited for that.

31
00:02:37 --> 00:02:39
That one too.

32
00:02:39 --> 00:02:42
Okay, so we're going to jump into questions.

33
00:02:42 --> 00:02:45
We have a question here from E Dorphy.

34
00:02:45 --> 00:02:53
What are the most overlooked App Store Connect APIs or capabilities that developers should be taking advantage of today?

35
00:02:53 --> 00:03:00
So maybe specific to the App Store Connect API, what APIs do you think developers should be using if they're not already?

36
00:03:00 --> 00:03:23
can take that basically i would assume when you are actually developing and i trading for testing is the most of the time is spent on doing multiple api calls and re-invoking different builds uploads so i would say build uploads which was a feature that was launched last year basically helps you to do this end-to-end with full automation and you can upload a build you can make that build

37
00:03:23 --> 00:03:47
available for test flight testers and you can get feedback which is also a public api that's available for you to be able to consume act on it and upload another build this whole process of development is completely can be automated with app store connect i think that's the most best api that we have done and that's amazing i was going to say that one too like the combination

38
00:03:47 --> 00:04:08
of test flight feedback apis and webhooks for a long time developers have wanted this to be able to automatically generate tickets in their own ticketing system for things that they can follow up on so i really like that one too i also like um i think the power and performance api there's a your ability to download some reports and see how your apps are actually doing on the device and to

39
00:04:08 --> 00:04:33
give you this information is uh one that's i think a little underutilized yeah it's like uh storage and cpu usage right yeah okay from d fabulich the accessibility nutrition label allows me to declare that my game has captions. My game doesn't have captions because it doesn't have sound.

40
00:04:34 --> 00:04:54
It's fully accessible to anyone with hearing disabilities. How do I signal that hearing impaired user to hearing impaired users that my app is accessible to them? It's a really good question. Really hard. Yeah, it is. I think we can all agree. It's hard for us to know exactly what is the right accessibility questions to answer for someone else's app. And I think it's

41
00:04:54 --> 00:05:17
important to look into answering those things your best ability on what does your app do and so that customers who are looking for those accessibility things can see it and you know how to answer for these questions just kind of you know look through it and answer your best of your abilities yeah and i think you even sort of said it in the question if your game doesn't have captions then don't say

42
00:05:17 --> 00:05:27
that it does there are other ways like in the app description where you could say anything you want to lots of different audiences, and so I would take a look at that.

43
00:05:27 --> 00:05:29
I think app previews is another way.

44
00:05:29 --> 00:05:38
Someone's looking at their app on the App Store, they're going to see a preview of what your app does and how it experiences, and that's a great way of explaining to people when they're first seeing your app in the App Store, what is your app like?

45
00:05:38 --> 00:05:43
All the value propositions that you have, you can put it in there as well and make it really clear.

46
00:05:45 --> 00:06:02
Okay, Claire KCS, what are the most common reasons subscription apps get rejected on their first review, and any pro tips for a new developer on submitting and on following up after rejection to get approved more quickly?

47
00:06:02 --> 00:06:05
This is something I feel like a lot of developers really care about.

48
00:06:05 --> 00:06:10
And we hear about it at WWDC, people really wanting to nail it when they submit to AppReview.

49
00:06:10 --> 00:06:16
I think that preparation is really the key to a successful AppReview submission.

50
00:06:16 --> 00:06:19
We're here to help you get your app onto the App Store.

51
00:06:20 --> 00:06:44
But preparation and ensuring that you've tested on a device the same way that you expect your users to use the app, that you've exercised the functionality to the best for your ability, you've addressed any crashes, that you've tested your IAP, make sure it works, make sure that your whole systems are production ready.

52
00:06:44 --> 00:06:47
And then also provide app review.

53
00:06:47 --> 00:07:06
use the app review notes on the submission page to tell app review how they can use your app and any guidelines they have, including any login or demo credentials. And I think that last piece is really important. If you're a subscription app and there's certain stuff behind the paywall, you need to make sure that you put those credentials in the app review notes. There's

54
00:07:06 --> 00:07:11
a section for it so that when they come in, they can log in, they can see the full app.

55
00:07:12 --> 00:07:13
Some people forget that.

56
00:07:14 --> 00:07:20
The other piece is just relevant information and notes that you might want the reviewer to look at.

57
00:07:20 --> 00:07:22
Maybe you've considered or thought through.

58
00:07:23 --> 00:07:25
That can really help them so they're not looking at it blind.

59
00:07:26 --> 00:07:34
Yeah, I would also add like in-app purchase is something that you can actually test using Sandbox environment when you are either using Xcode as well as when you use TestFlight.

60
00:07:35 --> 00:07:37
We would highly recommend like test your app.

61
00:07:37 --> 00:07:43
Make sure your subscription is actually working so that the app review becomes an easy step.

62
00:07:44 --> 00:07:48
Yeah, getting a good tester base before submitting, I feel like, could be really helpful.

63
00:07:48 --> 00:07:53
And also plug for your favorite feature, the new enhanced in-app purchase permission flow.

64
00:07:54 --> 00:08:01
Because if there's something wrong with the in-app purchase, you're going to have much clearer communication with app review about it, specifically about that.

65
00:08:01 --> 00:08:02
And then just one more thing.

66
00:08:02 --> 00:08:05
The question mentioned how to deal with the rejection quickly.

67
00:08:06 --> 00:08:11
The other thing is to make sure that, you know, you'll get a detailed message from AppReview on your submission.

68
00:08:12 --> 00:08:31
And take the time to, and the App Store Connect feature to write a response, not just submit an update, but respond to AppReview and use that space to have a dialogue to explain the way your app works and ask them any more detailed questions you might have about the rejection itself if it wasn't clear.

69
00:08:31 --> 00:08:31
Yeah.

70
00:08:32 --> 00:08:33
Okay.

71
00:08:34 --> 00:08:46
Brew Install Poppy says, So the retention messages API lets our app say, wait, babe, I can change right as the user is about to cancel.

72
00:08:46 --> 00:08:47
How cool is that?

73
00:08:48 --> 00:08:57
Now, on the analytics side, will ASC show impressions, save rate, and how often the user revisits the cancel flow?

74
00:08:57 --> 00:08:57
Nick.

75
00:08:57 --> 00:09:03
We can all change, and we will be giving a lot of data on this so that you can measure how it's working for you.

76
00:09:03 --> 00:09:26
give how many people saw that retention messaging page, how many people clicked cancel, how many people continued to stay subscribed. And then you can test out different messages to see what resonates the most. You can offer them different plans. There's a lot you could do there, and we have a lot of data for that. Yeah. Retention messaging in general is just very exciting,

77
00:09:26 --> 00:09:54
and I feel like it gives developers a chance to remind users about this app that they may not have used in a while. So it's really exciting. Okay. You can change. Okay. Another one from E. Dorfee. What are the most common security mistakes you see developers making when implementing App Store Connect API integrations? Hot take for me, I don't feel like we see that many. I feel

78
00:09:54 --> 00:10:19
like we see developers doing a really good job managing their API keys. But what are things that developers can do um in terms of managing keys in the app store connect api dave yeah i think the key thing is don't don't hard code your api keys into your repository you don't want to store it there you don't want to like put it into you know any clients that you're shipping uh and you

79
00:10:19 --> 00:10:40
know manage this private key responsibly uh the great part is if you find that you have actually compromised your key or so you did something you should have you shouldn't have you can revoke it App Store Connect, but I think basic key hygiene. And there are a couple of things that I see as well. So sometimes people will use the API for their own purposes and their own systems. Other

80
00:10:40 --> 00:11:07
times they're using it to integrate with third parties. And if you're doing that, you need to be especially careful. If you stop using that service, revoke that key, be really careful about the permissions that you give to that particular key. Don't overshare information with those third parties um so certainly be be careful with those situations and some of that gets into user

81
00:11:07 --> 00:11:30
management too on an account it's like as people are working on the app or no longer working on it that i think is the really important time to um exactly to be working doing security right absolutely private keys are meant for your own private use it's not something that you need to be sharing with anything and if you're particularly creating a key for a specific use case use it for

82
00:11:30 --> 00:11:56
that and then revoke it. Yeah. So that's the best practice. We also have users and access APIs too that people can use to monitor who's on their account and compare that against their own internal systems or other things. So that would be the best thing I would think. Okay. Stigler Farmers asks, in App Store analytics, is there a way to see how many people updated organically

83
00:11:56 --> 00:11:58
versus in the background?

84
00:11:59 --> 00:12:02
So auto-update versus manual.

85
00:12:02 --> 00:12:03
Versus manual, yeah, we do.

86
00:12:03 --> 00:12:08
So there are a couple of different places that you can get data on updates.

87
00:12:08 --> 00:12:12
One is in our dashboard, and then we also have reports.

88
00:12:12 --> 00:12:16
So the breakdown for auto-update versus manual is in the reports only.

89
00:12:17 --> 00:12:27
But if you go online and you look at the analytics reports, you can download that and see a column that breaks down auto versus manual.

90
00:12:27 --> 00:13:02
Okay. Jonathan889 says, I work for a large company that has a lot of apps. I know about some of those. We want to automate uploading of only a few apps from CI. Creating a team API key gives access to all the apps unless I'm mistaken. Yes, it does. But we'd rather have a token that can only access a few apps, is creating an account with a key the only way? This is a good one.

91
00:13:03 --> 00:13:26
It is a good one. We talked a little bit about how you can give API keys to different users and different access controls, whether it's developer, admin, or something like that, like you can for users in App Store Connect. However, for a general access key, you cannot change which apps it has access to. We do have a feature, I don't know if this is going to work in

92
00:13:26 --> 00:13:50
their case, where you can create a key per user of App Store Connect. And it's tied to that user's level of access. And so that'd be like an individual key. So if I happen to have the marketing role and I only have access to a couple of apps on the account, then I can create an API key that matches my same level of access and control. And this is a really great one now

93
00:13:50 --> 00:14:04
because we have some non-technical users like a marketer who maybe want to download reports and they want to download them every day and they just want to download a CSV.

94
00:14:04 --> 00:14:15
And with some handy AI tools, they can do that themselves and just get completely set up with the exact same permissions of what they can do in the UI and they can just automate some of the things that they might be doing manually.

95
00:14:15 --> 00:14:25
I think the thing that's interesting about it, in this case, if you're working in large companies, people may forget that that person's key was the one they were using to upload all the apps.

96
00:14:26 --> 00:14:32
And when they, you know, leave and remove the access to Apps for Connect, all of a sudden automated systems that they've been depending on wasn't working.

97
00:14:32 --> 00:14:38
So I think that's why I say I don't know if it works in this particular case, but this concept of testing things out works great.

98
00:14:40 --> 00:14:40
Okay.

99
00:14:41 --> 00:14:42
This might be a hard one.

100
00:14:43 --> 00:14:52
LazyVar asks, is it possible to set a different name and subtitle depending on target for universal purchases?

101
00:14:52 --> 00:15:06
For example, omit mentioning NFC for a tvOS target and or are there any recommendations or best practices when key app functionality differs per platform on universal purchase apps?

102
00:15:06 --> 00:15:07
This is hard.

103
00:15:07 --> 00:15:15
And this comment, like this comes up where people sometimes have different privacy practices on different platforms.

104
00:15:16 --> 00:15:17
So, yeah.

105
00:15:18 --> 00:15:24
Yeah, there's a whole set of information that's shared across all the apps, all the different platforms that your app is on.

106
00:15:24 --> 00:15:28
And as I mentioned, the name and subtitle, this is what gets shared across.

107
00:15:28 --> 00:15:39
Not only is it name and subtitle, but also your age rating is shared across and the genre that you're in, the idea being because once you get the Apple One platform, then you can download it on the rest.

108
00:15:40 --> 00:15:41
And so this is really what makes your app your app.

109
00:15:42 --> 00:15:47
Description, however, is something that can differ per platform.

110
00:15:47 --> 00:15:58
So your Mac app has one description, and then what people would, or in this case, your tvOS app, can have a different description that doesn't talk about NFC, and your iOS app can talk about NFC.

111
00:15:58 --> 00:16:02
Yeah, that would probably be the best place for it.

112
00:16:04 --> 00:16:04
Okay.

113
00:16:07 --> 00:16:13
E-Dwarfy asks, does the new retention workflow support localization?

114
00:16:14 --> 00:16:23
If so, do we need to or do you recommend to localize our retention message to match all the localizations of the app itself?

115
00:16:23 --> 00:16:24
I can take that one.

116
00:16:24 --> 00:16:27
Yes, localization is supported.

117
00:16:27 --> 00:16:30
And you do want to have consistency in your localization.

118
00:16:30 --> 00:16:39
If you have localizations for your customers and you want to make sure that it's going to be consistent with the retention message that you're offering to them.

119
00:16:40 --> 00:16:52
Yeah, we've heard from some users in different regions that they have kind of a disjointed experience when they go from things that are really translated and personalized for them and then an English part of the experience.

120
00:16:52 --> 00:16:54
So I would definitely say.

121
00:16:54 --> 00:16:57
Match the language when you say, I can change, baby.

122
00:17:00 --> 00:17:01
Yeah.

123
00:17:02 --> 00:17:02
Okay.

124
00:17:03 --> 00:17:05
Ooh, username SixCupsOfCoffee.

125
00:17:05 --> 00:17:06
That's me.

126
00:17:08 --> 00:17:08
Okay.

127
00:17:09 --> 00:17:12
I see a lot of popular apps with dense copy in the description.

128
00:17:13 --> 00:17:19
While a lot of advice is not to use dense copy, what are the best practices for App Store app descriptions?

129
00:17:21 --> 00:17:26
I just think about what your users might be interested in and what you're trying to offer to them.

130
00:17:27 --> 00:17:32
As this question mentioned, sometimes people try to stuff as much information in there.

131
00:17:32 --> 00:17:39
But then for the people who are evaluating your app and thinking about whether they might want to use it, it may not be very enticing.

132
00:17:39 --> 00:17:42
So always keep your user front of mind.

133
00:17:42 --> 00:17:46
I think tailoring your message for your audience is a good idea.

134
00:17:46 --> 00:17:56
And, you know, if you're trying to figure out who your audience might be, you can take advantage of features like custom product pages and drive different audiences to different copies.

135
00:17:56 --> 00:17:56
Yeah.

136
00:17:57 --> 00:17:59
Or product page optimization to test two different.

137
00:18:00 --> 00:18:01
Yeah, that's what I would recommend.

138
00:18:01 --> 00:18:04
Like it's a safe way to see if your description is too verbose.

139
00:18:04 --> 00:18:04
Yeah.

140
00:18:05 --> 00:18:05
Right.

141
00:18:05 --> 00:18:15
Yeah, using the product page headers and search results real estate now where you can upload your custom videos and images can still tailor to the app description.

142
00:18:15 --> 00:18:16
It's not the only place.

143
00:18:17 --> 00:18:21
Yeah, I mean, I hate to be that guy, but a picture is worth a thousand words.

144
00:18:21 --> 00:18:21
Yes.

145
00:18:22 --> 00:18:23
So, you know, we can throw it in there.

146
00:18:23 --> 00:18:27
The new product page header will be at the top.

147
00:18:27 --> 00:18:31
Now the first thing they see, it's a compelling image or video.

148
00:18:32 --> 00:18:33
You can change it.

149
00:18:33 --> 00:19:01
you can use custom product pages or product page optimization so not to vary away from reading reading is great there's also going to be a super important image or video that's going to kind of convey more meaning um and then in your description maybe it could be more factual i don't know but you should use a product page optimization to try okay username hello universe help which world c

150
00:19:01 --> 00:19:17
asks, what are the best practices for using TestFlight for internal team testing? And how about external testing, Shobi? Yes. So if you take care of, you are first developing your app.

151
00:19:17 --> 00:19:38
Before you go ahead and find out who your testers are, the best place to test is within your own team. That's where internal TestFlight comes in. Basically, internal people who are within your team can be added as App Store Connect users, and you can send an email invitation to them to say, hey, go ahead and test the app and provide feedback. This is also extremely useful if you

152
00:19:38 --> 00:20:00
have a QA team to whom you want to distribute bills on a regular basis. Once you have done the due diligence of an internal test flight and your app is working as expected, now it's a great time to go ahead and do external test flight, where you submit to beta app review process, and the app review process exists in order to ensure there is trustworthy and content is all safe for both

153
00:20:00 --> 00:20:06
developers and testers within the world. And once it's approved, you have up to 10,000 testers.

154
00:20:06 --> 00:20:16
And it's very easy to find external testers. You have a link. You can go to any communities where your app is popular. I wouldn't say it's that easy. People say sometimes it's not easy.

155
00:20:17 --> 00:20:21
I know you have to download the TestFlight app in order to be becoming a tester to it.

156
00:20:21 --> 00:20:30
Yes. But this is also to make sure this is coming from TestFlight and it gives the trust and the privacy that what the testers look for.

157
00:20:30 --> 00:20:34
And that's the main reason we want them to download TestFlight and distribute through that.

158
00:20:35 --> 00:20:38
And to help find people, there's forums.

159
00:20:39 --> 00:20:44
You know, if you have a gardening app, there are gardening forums you can post there and try to get feedback.

160
00:20:44 --> 00:20:47
You know, you're not trying to sell your app to that community.

161
00:20:47 --> 00:20:51
You're trying to help the community and honestly looking for feedback.

162
00:20:51 --> 00:21:00
And what we've seen is that when people are excited about their app and they're excited about the community, the community will give really useful and helpful feedback.

163
00:21:01 --> 00:21:05
There's some people who are really excited about testing new software and want to try new things.

164
00:21:05 --> 00:21:06
Especially games.

165
00:21:06 --> 00:21:07
Got to get in front of them.

166
00:21:07 --> 00:21:08
Especially games.

167
00:21:09 --> 00:21:13
And Lydia, talking about like the internal testing, you use TestFlight to test.

168
00:21:14 --> 00:21:15
Yes, absolutely.

169
00:21:16 --> 00:21:19
To TestFlight and internal, you know, pre-release features.

170
00:21:19 --> 00:21:41
this is also how we make sure this is ready to go live yeah every bill that we give to you lydia is through internal test flight book we test test flight through test flight the other part of the question about like what are the best practices for using it for internal team testing there are different kinds of internal teams so we have qa teams that might be dedicated and focused on the

171
00:21:41 --> 00:22:03
app but we also have localization teams who are looking at something totally different and you might want to create different internal groups for those and give those teams different builds and you can do that by creating different groups within the internal test flight and basically have a qa team who will probably get the build on an automatic basis on every build goes to them and

172
00:22:03 --> 00:22:28
you can have to what you said the localization team and you choose what builds you want to give it to the localization team and if you have want to hear from leadership on feedback before you want to go external you can create a team you create a group that is specific for be careful about that build okay what advice would you give to developers oh sorry edorfi back with more api

173
00:22:28 --> 00:22:52
questions which i really enjoy um what advice would you give to developers with respect to api keys vending them to other tooling using them within their own applications and automation scripts keychain principle of least privilege limiting user access to apps are a few things come to mind i feel we talked about this earlier but people are upvoting it so they still have

174
00:22:52 --> 00:23:13
questions about it um yeah i you know it's really important that api keys are meant to be on your server only and we'll keep repeating that because it's it's sometimes it's like oh this is so easy i can just put into my app and then it can make all sorts of different calls if you want to build an app that you see abstract connect api so putting on the the server is key and you know

175
00:23:13 --> 00:23:23
just remember there's no expiration date unless putting the server so that it's all like that There's no expiration date on these keys as well, so, like, they last a long time, so pay attention to that.

176
00:23:24 --> 00:23:28
Yeah, if you create a team key, think about what role it has.

177
00:23:28 --> 00:23:37
Like, maybe you want to give lower-level permissions to just be reading data, but, like, an admin key can do quite a bit.

178
00:23:38 --> 00:23:57
And, you know, if what you're doing is you're trying to figure out how the API works and you're testing it, that's a great time to use those individual keys, you know, to just kind of try it out a little bit, See what you can accomplish before you want to move on to maybe that team-wide key that's going to be, you know, running in the server forever and not just, you know, locally on your computer to try out a few things.

179
00:23:57 --> 00:24:04
And I'm definitely a proponent of principle of least privilege of the e-dorphy called out.

180
00:24:04 --> 00:24:05
So I'd say I agree with that.

181
00:24:07 --> 00:24:07
Okay.

182
00:24:09 --> 00:24:09
All right.

183
00:24:09 --> 00:24:20
From Josh D., we want to be able to use the same product name on the App Store for our Mac app and our iOS and Apple Vision Pro apps combo.

184
00:24:20 --> 00:24:24
So it's like Mac here, and then the other one is Universal with iOS and Apple Vision Pro.

185
00:24:24 --> 00:24:26
But they have different bundle IDs.

186
00:24:27 --> 00:24:28
Last I checked, that wasn't possible.

187
00:24:29 --> 00:24:30
Has that changed?

188
00:24:31 --> 00:24:33
It has not changed.

189
00:24:33 --> 00:24:34
It has not changed.

190
00:24:34 --> 00:24:46
But we recommend, you know, there are a lot of good advantages to adding your Mac app to your iOS and Vision Pro app record and using Universal Purchase.

191
00:24:47 --> 00:24:51
And that would accomplish the goal and maybe give you some other benefits as well.

192
00:24:53 --> 00:25:04
Yeah, and when those names are being shared, it's good to remember that as the ecosystem has evolved, now you can have an iPad app, you download it on your Mac.

193
00:25:04 --> 00:25:11
And so having these names be unique is really important as you're trying to disambiguate different apps.

194
00:25:11 --> 00:25:21
Yeah, I'd say we're building to benefit universal purchase apps, and we definitely want to have more features that make it more convenient for developers to have universal purchase across many platforms.

195
00:25:23 --> 00:25:33
But I think also all the way down in the ecosystem to Xcode, we're going to continue, I think, to see probably more features that support that kind of unified development on one bundle ID.

196
00:25:33 --> 00:25:39
So if it's possible, Josh D., to merge and come together, I know it's a lot.

197
00:25:39 --> 00:25:44
It's a lot to develop and migrate those users, but there are a lot of benefits down the line for that.

198
00:25:45 --> 00:25:46
Okay.

199
00:25:47 --> 00:26:01
So Alsant says, for a developer building their first app with user authentication and third-party API keys, what's the most important thing to get right before submitting to AppReview for the first time?

200
00:26:04 --> 00:26:05
Jeff?

201
00:26:05 --> 00:26:05
Yes.

202
00:26:07 --> 00:26:30
I think making sure that you've, again, same advice as the last time, different variation on it, thoroughly tested the app, thoroughly tested the third-party functionality, the third-party APIs, and that the keys and that your services are ready to receive production traffic and ready for scale before you submit the app review,

203
00:26:30 --> 00:26:35
that everything is good to go from that standpoint is really one bit of advice I would give.

204
00:26:36 --> 00:26:54
The other thing, I don't know exactly what they mean by third-party API keys or how they're using them, but if it's your user's API key, I believe we want to see that you are also upholding privacy on your side of managing other people's keys and doing that in a really privacy-friendly way.

205
00:26:54 --> 00:26:56
So that's something that should be kept in mind.

206
00:26:56 --> 00:27:20
correct and you know let's not again i'm gonna remind them for all my friends in app review that demo account information i think nick you talked about earlier right it's it's really good to have that so we can you know launch the app and review it with all the right setup and everything like that yeah okay beloved melody says i'm nervous about transitioning my app from paid up front

207
00:27:20 --> 00:27:44
version one to freemium version 1.1 sounds like a path that's been paved before with a purchase restoration pathway for those who purchased the v1 it would be devastating to fail to honor users previous purchases how do i know my purchase restoration testing code will work in the real world well thank you melody for thinking of your users and wanting to make sure that she

208
00:27:44 --> 00:28:03
they continue to provide a good experience um anybody have ideas what to do in that case that's a tough one yeah i i think what you want to do is it's great we have with the store kit to app transaction API, you're able to actually see the original app version that a customer purchased.

209
00:28:03 --> 00:28:11
And so you can kind of, if you want to be able to tell what they have and then be able to provide service based off of that, if that's how you want to go forward with that.

210
00:28:12 --> 00:28:29
Shelby talked about it before with TestFlight, that in TestFlight, you can test all of these kinds of features and look at who has what purchases and how you want to behave with them and test out some of those accounts. So I would recommend looking at the StoreKit 2 API and also use TestFlight to make sure you got it right.

211
00:28:30 --> 00:28:34
Yeah, first is Xcode testing, and once it's ready, go for TestFlight testing.

212
00:28:35 --> 00:28:41
And TestFlight, by default, uses the sandbox environment, but your account is still the production account that will be on the account settings.

213
00:28:41 --> 00:28:51
But if you want to explicitly also test the testing controls that is available on a sandbox account, you do need to log out of the production account and log in into the sandbox account.

214
00:28:51 --> 00:28:52
But it will work seamlessly.

215
00:28:53 --> 00:29:02
Well, challenge question for you is, How do you test in TestFlight a paid app, like transitioning from a paid app to freemium?

216
00:29:03 --> 00:29:05
Like, does that really, you know what I mean?

217
00:29:06 --> 00:29:06
What you can test in TestFlight.

218
00:29:07 --> 00:29:07
Sorry, go ahead.

219
00:29:07 --> 00:29:12
I was going to say, TestFlight's not going to be testing the purchases, but at least you can see which version you're on.

220
00:29:12 --> 00:29:16
And if you want to make sure you're actually reading those API keys correctly, you kind of get a sense.

221
00:29:16 --> 00:29:19
And I guess you could go from your App Store version.

222
00:29:19 --> 00:29:25
You could have one account that downloads the App Store version and then upgrades to the freemium version that they're testing.

223
00:29:26 --> 00:29:28
So that's probably what they'd want to look at.

224
00:29:28 --> 00:29:30
going from the paid version that's on the App Store.

225
00:29:31 --> 00:29:35
And I think I want to call out that TestFlight is not the only place you can do sandbox testing.

226
00:29:36 --> 00:29:48
As Shelby was mentioning, in App Store Connect, you can also create sandbox accounts that you can set up in different countries and reset their purchases and all sorts of more complex sandbox management.

227
00:29:48 --> 00:29:57
And so you can do that as well to test all these kinds of in-app purchase features as you're beginning to step into this new world from a purchase app into a premium app.

228
00:29:58 --> 00:30:29
Okay. So hello, universe help, which world C asks, what's the best way to learn the step-by-step procedure to publish your first iOS app? This is a question we get at WWDC and in labs is just give me an overview. Like I want to just know where to start and what to do and how to do this in the best way. Perfect. Maybe you should start with how an app gets created. Then I can go into

229
00:30:29 --> 00:30:54
and we can go around like app review and go around it well i mean what's the best way to learn okay so like i you know i i think what's great is the developer website has a lot of great tutorials and things you can do from you know there's videos documentation there's wwdc sessions it's not just this year's wwdc that has useful information you can go back and you know look at them and see

230
00:30:54 --> 00:31:10
what's there in fact i was watching one of the videos today and they referenced a wwdc session from 2022. So like a lot of this stuff is still great. And then the App Store Connect help guide does have a step-by-step instructions on how to navigate the App Store Connect UI and what to do.

231
00:31:10 --> 00:31:20
So I really think that we provide a lot of, you know, a lot of our friends who are writing great documentation and the videos to really help you on your way. And that's where I would start.

232
00:31:20 --> 00:31:46
And then specifically there's an Apple developer pathway that you can follow, which is like a step-by-step way of getting your app on the store for the very first time. And I'll plug the guides again. We have incredibly detailed guides for every single page, every single action that you might want to take. And it's organized in a very nice way to take you from the beginning to the

233
00:31:46 --> 00:31:52
very end. And so I would take a look at those resources. They're all on developer.apple.com.

234
00:31:53 --> 00:31:59
And there is a Getting Started with TestFlight tech talk, which you can watch, which will actually walk you over, like, how do you build an app?

235
00:31:59 --> 00:32:00
How do you test with TestFlight?

236
00:32:01 --> 00:32:01
Yeah.

237
00:32:01 --> 00:32:02
And then you can submit to AppReview.

238
00:32:03 --> 00:32:06
And once it's approved, wow, it's in the App Store.

239
00:32:06 --> 00:32:07
Okay.

240
00:32:07 --> 00:32:15
So once it's approved, Rustam06 asks, how can I increase visibility for my app?

241
00:32:17 --> 00:32:18
This is a good one.

242
00:32:18 --> 00:32:21
I would say creative assets.

243
00:32:22 --> 00:32:35
You can go ahead and market your app based on not just the definitely provide more screenshots, previews, and creative assets so that your app content is visible for everybody to be able to see what is actually your app can help with.

244
00:32:36 --> 00:32:44
So yeah, and we have actually a lot of different tools that you can use to try to get more visibility on the app store organically.

245
00:32:45 --> 00:32:48
So one thing you can do is create in-app events.

246
00:32:48 --> 00:32:56
You can have custom product pages and tie those custom product pages to specific keywords to try to increase the relevance.

247
00:32:57 --> 00:33:06
You can also submit, do a feature nomination where you take your app and you submit it to our editorial team.

248
00:33:06 --> 00:33:07
And they'll take a look.

249
00:33:07 --> 00:33:14
And if they really like what your app is about, they'll put it on the Today tab and in other parts of the App Store.

250
00:33:14 --> 00:33:20
And that can really give you a huge boost and get your users and your app going.

251
00:33:20 --> 00:33:21
Yeah.

252
00:33:21 --> 00:33:32
And if your app is app of the day or game of the day, there is also with an App Store Connect app, you get a card where you can actually go ahead and share it in the thing, which we call it a shareable moment.

253
00:33:32 --> 00:33:33
And you can advertise it.

254
00:33:34 --> 00:33:38
Through that, you can basically get more users and more visibility of your app.

255
00:33:38 --> 00:33:45
Those same, for what it's worth, you also can create a shareable moment when you have a new version of an app and share that.

256
00:33:45 --> 00:33:52
A lot of what you're going to want to do is off the App Store as well and referencing people back to the App Store.

257
00:33:54 --> 00:33:57
I have a question I already know I want to ask Lydia.

258
00:33:58 --> 00:34:03
Jeff Bash says, I want to develop a universal app.

259
00:34:04 --> 00:34:10
Do I need a different binary for every platform, watch, iOS, et cetera, et cetera?

260
00:34:10 --> 00:34:11
That's a good question.

261
00:34:11 --> 00:34:17
So if I have a universal app, just all my platforms, one bundle ID, how many binaries do I need?

262
00:34:17 --> 00:34:19
I don't think you need the full set.

263
00:34:19 --> 00:34:24
It depends on which platforms you want to distribute to.

264
00:34:24 --> 00:34:31
So, like, and it also depends if your app is eligible for, like, Apple Silicon, et cetera.

265
00:34:31 --> 00:34:34
You can get some platform usage based on that.

266
00:34:34 --> 00:34:48
So you do want to have, like, distinct metadata for each platform and at least, like, an iOS binary, a macOS binary, a watchOS as well.

267
00:34:48 --> 00:34:49
The iOS watch combo, yeah.

268
00:34:49 --> 00:34:52
You can also start with one and add the others as you're ready.

269
00:34:52 --> 00:34:53
Yeah, for sure.

270
00:34:53 --> 00:34:55
That's why you don't need the full set.

271
00:34:55 --> 00:34:56
You can take baby steps.

272
00:34:56 --> 00:35:03
And like you said, more specifically, if you have the iOS app, you can start off by making that available on macOS or on VisionOS.

273
00:35:03 --> 00:35:04
Yeah, same.

274
00:35:04 --> 00:35:14
And once you've done that and you decide, actually, I want to take use of more of the native features and create a VisionOS-specific or macOS-specific one, you can do that.

275
00:35:14 --> 00:35:23
and your users will just update to that latest one, which then becomes a native version that can rev independently from your iOS and your iPadOS apps.

276
00:35:24 --> 00:35:27
Yeah, so I feel like this is a hard one to answer.

277
00:35:28 --> 00:35:29
Do I need a different binary?

278
00:35:30 --> 00:35:31
It depends.

279
00:35:32 --> 00:35:45
Definitely, like, it's great to start with an iOS app, and then because there's so many of these expansion capabilities on macOS and on VisionOS, and then try those out and see kind of, you know, how it's working there.

280
00:35:46 --> 00:36:03
Okay. Lee Shuang Quan asks, if an app is localized to multiple languages, for review notes, would it still be recommended to put those notes in English, or could it be the developer's native language?

281
00:36:04 --> 00:36:06
That is a tricky question.

282
00:36:07 --> 00:36:08
It can be.

283
00:36:09 --> 00:36:15
AppReview has a huge set of language skills.

284
00:36:16 --> 00:36:20
It doesn't mean that there's a 100% match for what your primary language is.

285
00:36:20 --> 00:36:34
But there's often a good chance that if your primary language is not English, that we'll be able to match a reviewer's skills to that language and review it in that.

286
00:36:34 --> 00:36:39
It may not hurt if you put them in English, but it's definitely not an absolute requirement.

287
00:36:40 --> 00:36:43
And reviewers do take care of different languages, so.

288
00:36:43 --> 00:36:44
Yes.

289
00:36:45 --> 00:36:49
I know for a fact that they review apps in many different languages.

290
00:36:51 --> 00:36:51
Yeah.

291
00:36:51 --> 00:36:56
So English could be preferred, but we would support all the options.

292
00:36:57 --> 00:36:57
Right.

293
00:36:57 --> 00:36:58
Yeah.

294
00:36:58 --> 00:37:01
Developers do not need to speak English to release on the App Store.

295
00:37:02 --> 00:37:02
Okay.

296
00:37:04 --> 00:37:07
E-Dorphy is back with more API questions.

297
00:37:08 --> 00:37:15
What new App Store Connect workflows introduced this year should developers revisit, even if they already have mature automation in place?

298
00:37:17 --> 00:37:18
So, one thought.

299
00:37:18 --> 00:37:26
So, obviously, this person is very focused on automation and the API, which might mean that they're not looking at the dashboard as much.

300
00:37:26 --> 00:37:30
We have a lot of new stuff in App Store Connect web.

301
00:37:30 --> 00:37:40
In particular, on the analytics side, we just launched over 100 new metrics to help developers understand their subscription business, their in-app purchase business.

302
00:37:41 --> 00:37:49
And while that data is available in the API, we have great visualizations that we just released in the web UI.

303
00:37:49 --> 00:37:53
And so if that's of interest to you, I would take a look at that.

304
00:37:53 --> 00:37:56
That wouldn't be in any of the automations.

305
00:37:56 --> 00:38:05
Yeah, I think to answer some of those other ones, the Game Center entities, leaderboards, and so forth can now be submitted.

306
00:38:06 --> 00:38:11
In-app purchases, as we mentioned before, the way that the APIs interact with that will evolve.

307
00:38:12 --> 00:38:17
Yeah, offer code APIs, which is now available for both consumables and non-consumables.

308
00:38:18 --> 00:38:20
Yeah, it's hard, E-Dorphy.

309
00:38:20 --> 00:38:23
I want to know what E-Dorphy is really seeking to do.

310
00:38:24 --> 00:38:26
Like, what are the best things?

311
00:38:26 --> 00:38:26
It depends.

312
00:38:26 --> 00:38:47
it depends on if you're a game if you have if you're doing subscription management with this um i think the build delivery api last year was a big one that developers who already had automation could take advantage of to update their build trains and be using that for build submission instead of transporter if they wanted to so there's a lot that could be done i feel like

313
00:38:47 --> 00:39:20
we've given some good advice test flight feedback also um okay all right our took ass what are the most common reasons to be rejected on the App Store? I think generally it's not. Some of the most common rejection reasons have to do with not being fully prepared for app review. So again, to be a little bit repetitive, but bugs and crashes. So make sure you've thoroughly tested

314
00:39:20 --> 00:39:28
your version before you've submitted it as a user would on a device, not just in simulator.

315
00:39:29 --> 00:39:40
Also making sure you've provided complete information, and it's been mentioned a couple of times, any demo credentials, any functionality you want to highlight to AppReview and the AppReview notes.

316
00:39:41 --> 00:39:55
Taking advantage of where it makes sense for, make sure you've thoroughly tested via TestFlight internal, following on with external, and gotten that feedback to refine your app to be ready for AppReview submission.

317
00:39:55 --> 00:40:02
And then just try to make sure your submission is complete and detailed.

318
00:40:03 --> 00:40:23
And then if questions arise from AppReview or you are dealing with things, that feedback loop can be pretty quick and just be conversational with AppReview and definitely answer any questions they have and test any bug fixes you may need before that.

319
00:40:23 --> 00:40:35
But also feel free to be conversational in your response, like ask questions about if you think they may be seeing something that you're not seeing to help guide them for a successful submission.

320
00:40:35 --> 00:40:40
And another thing is the app review guidelines, there's a lot there.

321
00:40:40 --> 00:40:44
And there are some specific sections for specific types of apps.

322
00:40:45 --> 00:40:51
And so if you're a kid's app, for example, there are going to be extra things that you need to make sure that you do.

323
00:40:51 --> 00:41:00
And so as app review goes through, if they see that your category is, you know, kids ages four plus, you know, they will take a closer look at that.

324
00:41:00 --> 00:41:16
If you have crypto or other, you know, content in there where there are specific sections in the guidelines related to that, make sure that you look at those and, you know, hit those guidelines before you submit.

325
00:41:16 --> 00:41:29
One thing Jeff referenced, too, is that we're seeing so many solo developers, so many new people becoming developers, and they can do so much at desk by themselves.

326
00:41:29 --> 00:41:34
But when it comes to testing, sometimes you really need your friends.

327
00:41:34 --> 00:41:41
And, like, you need to cover more corner cases and just besides simulator and your own device.

328
00:41:41 --> 00:42:01
so you'd be surprised like a lot of common rejections are just around maybe where a little bit more testing could have helped so i feel like that's one of the big things if you you know if you're worried about app review like have you had a few different people test your app to make sure it's working it launches and that they can use it i was going to say that exploit simulator is great

329
00:42:01 --> 00:42:28
it's really good but it's just it's not the same thing it's just getting on the device you know yeah multiple different ones and and that talk to your friends talk to your family use test flight public links is there okay this is an exciting one because i feel like i have this request too nanachi says what app metadata or capability changes are known to be validated only after

330
00:42:28 --> 00:43:00
test flight or app store connect upload are there plans to bring more of those checks into xcode or local pre-upload validation Lydia um we battle we validate all all the things all throughout the pipeline and we're always looking at ways we can get feedback as early as possible to the developer so we're continuing to iterate um but yes uh I can't list all the validations

331
00:43:00 --> 00:43:24
because there are a lot um but yes we are very we do want to let you know as soon as possible we're always looking to if there's something we could detect earlier and let you know earlier we make every attempt yes yes yeah at least the warnings are extremely helpful it's not always it has to be an error that is telling you this is but it also gives you a list of warnings on what

332
00:43:24 --> 00:43:48
all you should be uh acting on before you do the next build upload and i can't remember if it was in your question you also in xcode you can validate your app before delivery so there are ways that you can get signal before you actually upload the app which is worth considering yeah i think i i understand the spirit of the question i think we're always trying to move things

333
00:43:48 --> 00:44:21
yep further up in the development workflow further back or earlier earlier and so earlier there's probably more that could be done yeah um but also uploading frequently like continuous delivery is great yeah for a reason and um like that's a great way to figure out what issues you need to resolve okay jonathan889 asks is there a way to organize test flight builds into streams or groups

334
00:44:21 --> 00:44:47
for example keeping separate a major new feature that is being developed over months while regular builds continue? Yes, of course. You have the concept of groups in both the internal as well as external. So what is a group? Just to go into some more details of it, right? It's a organization of who are all the testers and what builds goes into it. So those testers who are belonging to a

335
00:44:47 --> 00:45:07
group will only have access to the builds that are within that group. So you decide based on your use case, how many groups do you want to have? Who are the testers in each of these groups? And what Pulse goes into these groups. For example, there can be a development team, that can be a group, and there can be a QA team, there can be localization team, and you can have like

336
00:45:07 --> 00:45:27
power user team. Another way to organize this is to say like you have an upcoming feature that you're working on, and that could belong to one of the groups where you're working on 2.0 version and building new features for it. And there is also a bug fix release that you're working on, and you want to get immediate feedback because you want to go fix it if possible. Create a

337
00:45:27 --> 00:45:48
separate group for the bug fix release put the builds in and those testers so that you can parallely test multiple iterations of your apps that is going in so you decide you don't have to upload just 2.0 every time you can be uploading 2.0 and then do your 1.1 bug fix at the same time and you know in this case i guess when they're thinking about you know this future thing then

338
00:45:48 --> 00:46:10
maybe it's their you know whatever 3.0 while they're working on the ones and the twos and that will be its own group so groups are the best ways to go ahead and have parallel development happening yeah and you can decide which how many parallel development you want to go ahead and support in a given time and there's a lot that test flight can do but also a big part of this is versioning

339
00:46:10 --> 00:46:31
yeah like which is very personal to what the developer wants to do and source code management and having notes and everything there about what these different trains are for but then once it gets into test flight like dave said you can do it in any order and then from there you're taking and selecting and matching those to the right group so you want to receive them yeah xcode cloud

340
00:46:31 --> 00:46:54
is another way where you can actually automate all of this process so that you can basically take your github have different branches if you're working on 2.0 and 1.5 and you can set them up to basically distribute automatically to these groups so it's all can be fully automated with xcode cloud yeah and ties that back to your to your branch management okay justin from bald winsville

341
00:46:54 --> 00:47:18
says is there a technical or policy reason that there is a few day delay on price changes to in-app purchases or subscriptions including when lowering the price this is a good question yeah you know this really gets down to we want the price to go live everywhere at the same time and just based off uh the way the earth turns and the sun rises it's all about like setting it up so

342
00:47:18 --> 00:47:19
that it can go live at the same time.

343
00:47:20 --> 00:47:26
So we're trying to give time for that to propagate and then give time so it can match those time zones across the world.

344
00:47:30 --> 00:47:30
Okay.

345
00:47:31 --> 00:47:32
All right.

346
00:47:32 --> 00:47:33
Another very good one.

347
00:47:33 --> 00:47:39
Do you have any advice for merging app listings that target different countries into one app listing?

348
00:47:39 --> 00:47:44
We heard this before with our iOS business versus macOS app.

349
00:47:46 --> 00:48:12
Where the builds are, variations of the same core app but features differ slightly by country so they have i've seen this before where you have your u.s app and your japan app and the features are slightly different and you've decided this is a lot to submit all these different builds and now they need to come together and what should you do

350
00:48:12 --> 00:48:34
they do that yeah i really think uh you choose the app you want to persist uh you know and keep around you know it's for those paying attention we did this ourselves this year uh at apple with creator studio with creator studio uh we used to figure out which of these ones we wanted to keep around like with our uh was it numbers i think and pages right and we used to have that too and

351
00:48:34 --> 00:49:09
it's we understand what it takes and you pick the app that you want to kind of be that flagship app and update your other one and send a note to them saying hey go download this other app and uh notify your users that this is happening and and then they transfer on over yeah the other thing is We do see this quite a bit with third-party developers, too, who make a different choice, whether it's branding or platforms, and they need to send a final release that tells users that the app is going away.

352
00:49:09 --> 00:49:23
At that time, they need to have the new app ready to send them to and then give the users a little bit of time to move over before eventually no longer supporting that app and doing kind of a forced upgrade situation.

353
00:49:23 --> 00:49:24
Correct.

354
00:49:24 --> 00:49:28
I think it's good to, you know, I wouldn't throw away that old code right away.

355
00:49:28 --> 00:49:30
You never know what you're going to want to do.

356
00:49:30 --> 00:49:36
Maybe a security update or maybe a small bug fix and help those users who haven't quite, you know, upgraded.

357
00:49:36 --> 00:49:37
It's going to take some time.

358
00:49:38 --> 00:49:41
Humans are creatures of habit, and I think it's not going to happen overnight.

359
00:49:41 --> 00:49:46
And you can actually see how people are propagating over with the analytics that we provide.

360
00:49:47 --> 00:49:54
So you can see sessions by app version and compare and see, you know, how people are migrating over.

361
00:49:56 --> 00:50:22
okay i'm excited about this one s mccoy says the new app store dashboard with analytics sounds really interesting and nick's already referenced it today can you tell me more about the features provided and how to maximize value this was a huge spring release for us and maybe not everybody knows about it yet yeah so we released the biggest update to app analytics since it launched over 10

362
00:50:22 --> 00:50:49
years ago, we added over 100 new metrics, mostly around in-app purchase and subscriptions. And what we did was we made sure that any data coming from the App Store in terms of search or downloads coming from App Store Browse and the Today tab could be tied back to those subscriptions that users might be able to purchase later on. And so what you can see is the full customer journey

363
00:50:49 --> 00:50:54
from download all the way through to purchase, and then through to subscription renewal.

364
00:50:55 --> 00:51:18
And we've got these great visualizations that allow you to see what percentage of people who download convert to paid, what percentage of people who convert to paid and continue paying over time. And it's a great way to understand what the soft spots are in your business, And then you can target those with improvements.

365
00:51:19 --> 00:51:22
On top of that, we also released new benchmarks.

366
00:51:23 --> 00:51:34
And so as you're looking at that full customer journey, you can see whether you're overperforming or underperforming in certain areas, and then take advantage of some of the features that we offer to try to improve.

367
00:51:35 --> 00:51:37
So really big update.

368
00:51:37 --> 00:51:43
If you're a subscription app or an app using in-app purchase, this one's for you.

369
00:51:43 --> 00:51:48
We have a brand new guide as well that goes into detail with images.

370
00:51:48 --> 00:51:49
We have a video.

371
00:51:50 --> 00:51:51
So tons of resources.

372
00:51:51 --> 00:51:52
We're super excited about it.

373
00:51:53 --> 00:51:55
And what we've been hearing so far is developers are loving it.

374
00:51:56 --> 00:51:59
Yeah, and developers especially, the new benchmarks are exciting.

375
00:51:59 --> 00:52:08
We've had benchmarks for a while, and that continues to be a really beloved developer feature because some developers go in and they see the data in App Analytics, but they might not know it's as good.

376
00:52:09 --> 00:52:09
Exactly.

377
00:52:09 --> 00:52:26
Exactly. And oftentimes we tell them to, we tell developers to start with the benchmarks because it's hard to know where you do need to improve. And by looking at the benchmarks, you kind of have a roadmap of what you want to focus on next. So yeah, great question.

378
00:52:26 --> 00:52:44
Okay. All right. Drummer says, any tips for creating a successful featuring nomination submission. So if somebody wants to get featured, they want to ensure the best chance.

379
00:52:44 --> 00:52:58
What I would say is, first of all, your app should be interesting and potentially novel or do something new that we haven't seen before. There are lots of categories that are pretty crowded.

380
00:53:00 --> 00:53:26
And so those are probably be a little bit less likely to get nominated just because there's a lot of them like that. The other thing I would say is if you have great art, that can be a great way to separate yourself from the crowd. We've released a whole bunch of new assets that you can place on your product page that would also be used in your featuring placement. And we want the

381
00:53:26 --> 00:53:51
app store to look great. And so if you have great art and a great, interesting app, that is the best way to make it to the top of that featuring nomination list um and you know um you can try a couple times as well so if you don't get it once you can try again uh but uh but yeah those are some yeah and it's it's not just for the first launch of your app right you know feature

382
00:53:51 --> 00:54:11
nominations uh it's it's a place to talk about updates as well and what's coming and you know roadmap so i you know i agree with you it's not just all about oh i didn't get this first launch out there absolutely looking at the new updates i think also it's like about the story behind your app like a little bit more about the history of how it came to be or what's really interesting

383
00:54:11 --> 00:54:43
about that definitely sometimes you know featuring is about a developer themselves that part's cool okay all right um oh okay d robin says my apps users paste their own asc api keys in today even the minimum role over grants there's no read only analytics only scope but for all apps what's the

384
00:54:43 --> 00:55:04
current least privilege setup you'd recommend for a third party tool so i think it's it's a funny question because what is least privilege probably depends on who you are and what it is it's interesting to you uh i would probably say the sales role has the lowest level what they're describing for what they're describing exactly it's going to give you that kind of thing where

385
00:55:04 --> 00:55:22
you can't you know make the changes i think they're worried about but it definitely depends on the role that you're you know what you're looking to do maybe we can talk a little about what the different roles are yeah there is a developer role using which you can upload builds and you can use internal test flight so that's something that if you want to looking for only

386
00:55:22 --> 00:55:40
uploading and testing then developer role is something that you can use yeah and the developer and marketer role can do things but they can't release yes your app or release a new version Like on the marketing role, you can make changes to the metadata, the product page, in-app events, and those kinds of things.

387
00:55:40 --> 00:55:44
So if that's the role you want to give with this key for API, that's what you'll do.

388
00:55:44 --> 00:55:50
Yeah, so those would be probably the lower permission recommendations that we would make.

389
00:55:51 --> 00:55:54
Okay, another one from D. Robinin.

390
00:55:55 --> 00:56:01
Any advice on fetching live or last 24 hours of App Store Connect data via the API?

391
00:56:01 --> 00:56:28
currently there is no api versus ui parity so while the ui we can preview last 24 hours worth of data via the api we can fetch only a day before or get weekly or monthly aggregates i don't know that much about this yeah well they're i mean they're right um you can see the last 24 hours in the ui in sales and trends and the api updates on a daily basis and so if they

392
00:56:28 --> 00:56:50
do want the latest and greatest which it sounds like they want uh the ap uh the ui is the place to do that but understood that it seems like this developer would like to see faster data in the api uh but currently not possible it is the same data it's just a different cadence depends on how you're that's right you're acquiring it and i know it's kind of implied in the question but we're talking

393
00:56:50 --> 00:57:07
really here about the sales data and analytics we're not talking about like other data in f store connect that's all you know updated whether it's the the rest of that is all pretty much piecemeal but when we're talking about the analytics or the the sales and trends that's what's getting good clarification summarized percent nick you're gonna get hit a lot because

394
00:57:07 --> 00:57:36
we have a lot of questions about app analytics okay ed edward quong asks what's your recommendation to measure the incremental impact visibility and downloads from creating the organic search cpp especially when we don't get keyword level data in asc is that true it is true we don't provide keyword level data in ASC. But what I would say is if you're creating a custom product page,

395
00:57:36 --> 00:57:59
you can go into app analytics and you can see how well that's performing overall. But then you also have the ability to add a filter by source type. And so what you can do there is filter by app store search and take a look at how that's doing. If you're not running any ads through Apple ads, then that app store search will just be your organic search.

396
00:58:00 --> 00:58:11
If you are running ads, then it will be blended together, but you could consider having two custom product pages, one for ads and then one for organic, so that you can tease out the difference there.

397
00:58:12 --> 00:58:14
Okay, I'll give you a break.

398
00:58:15 --> 00:58:26
Jonathan 889 asks, is there a way to see the rollout progress of the current release while a new release is being prepared and submitted to review?

399
00:58:26 --> 00:58:50
so this is we could we could assume that they're using phased release if they're talking about the rollout of the current release yeah that's what it sounds like to me and so you can and after connect you can go in to the version page and then click into phased release rollout and it you know shows you what you know what current day you're on where you are in this phase release rollout cycle yeah

400
00:58:50 --> 00:58:59
you just click on your prepare for distribution version yeah to see it Because you can have up to two versions in App Store Connect per platform.

401
00:58:59 --> 00:59:07
You've got the one that's ready for distribution that's out there, and then you can start working on other versions.

402
00:59:07 --> 00:59:15
So it sounds like what they're talking about here is while they're working on their new version, what's the state of that phased release for the other one?

403
00:59:16 --> 00:59:19
Okay, I think we have time for one more.

404
00:59:19 --> 00:59:29
Louis Courtney asks, I'm making a geospatial augmented reality game about Shakespeare in Stratford-upon-Avon.

405
00:59:29 --> 00:59:30
I'm in already.

406
00:59:31 --> 00:59:37
Building a full working demo version of it for app submission will be incredibly difficult, if not impossible.

407
00:59:38 --> 00:59:44
What options are open to developers building geospatial experiences in terms of passing app review?

408
00:59:45 --> 00:59:48
So wait, do you need to be in Stratford-upon-Avon?

409
00:59:49 --> 00:59:50
That's my question.

410
00:59:50 --> 00:59:52
To review the app, yeah.

411
00:59:53 --> 00:59:55
Is that the demo that's needed?

412
00:59:55 --> 01:00:09
If it's something where the review would need to take place in a specific location, then you can absolutely include a recording of your app with your submission.

413
01:00:09 --> 01:00:09
Yes.

414
01:00:09 --> 01:00:20
But keep in mind that, you know, that, you know, not all of your users, even those who occasionally visit Stratford-upon-Avon like me, will be there all the time.

415
01:00:20 --> 01:00:26
And you do want to provide some functionality for those who, you know, currently reside outside the UK.

416
01:00:27 --> 01:00:29
And this is where the whole ecosystem helps, right?

417
01:00:30 --> 01:00:32
Like, yes, build your app, test it with TestFlight.

418
01:00:32 --> 01:00:36
Once you're ready with internal TestFlight, move on to external TestFlight.

419
01:00:36 --> 01:00:46
And that will help you to pass the app review eventually because you're going through a beta app review process, gives you insights into what your geo experiences of the app is and is app review okay with it.

420
01:00:46 --> 01:00:50
So early feedback is always good and that helps.

421
01:00:50 --> 01:00:52
I'm going to still, I'm going to be looking out for this game though.

422
01:00:53 --> 01:00:53
Yes.

423
01:00:53 --> 01:00:58
And I hope that you can play it outside of Stratford-upon-Avon so that I can play it.

424
01:00:59 --> 01:01:01
So thank you everyone so much.

425
01:01:01 --> 01:01:02
We're out of time.

426
01:01:03 --> 01:01:06
Thank you for all these incredible, thoughtful questions.

427
01:01:06 --> 01:01:10
If we didn't get to your question, there's still a chance.

428
01:01:10 --> 01:01:15
You can go to the Apple Developer Forums at developer.apple.com slash forums.

429
01:01:15 --> 01:01:17
We also love feedback.

430
01:01:18 --> 01:01:23
And so if you have feedback or feature requests, there are some great feature requests in these questions.

431
01:01:23 --> 01:01:25
You can go to feedbackassistant.apple.com.

432
01:01:26 --> 01:01:29
My team looks at those every single week.

433
01:01:29 --> 01:01:32
We go through every single piece of feedback that comes in.

434
01:01:32 --> 01:01:33
I can't emphasize that enough.

435
01:01:33 --> 01:01:34
That's very true.

436
01:01:34 --> 01:01:35
Yes, we really do.

437
01:01:35 --> 01:01:56
so um please submit feedback and i assure you that we're taking that into thoughtful consideration as we build all these incredible products and thank you to my amazing panel shout out to our back of house staff who's helping us be successful today and have an amazing wwdc and hopefully we'll see you on the app store
