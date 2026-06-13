---
title: Keynote
source: https://developer.apple.com/videos/play/wwdc2026/101/
session: 101
collection: wwdc2026
duration: 01:16:02
fetched: 2026-06-13
via: whisper.cpp ggml-large-v3 (HLS stream audio — no SD download offered)
transcription: machine-generated, no speaker diarization
---

> Machine transcription of the **Keynote** SD video via whisper.cpp (`ggml-large-v3`).
> Group labs ship no Apple transcript; this is generated from the audio. Cues are timestamped segments, **not** speaker-attributed turns.

## Transcript

1
00:00:27 --> 00:00:29
- Good morning.

2
00:00:29 --> 00:00:35
Thank you for joining us today at Apple Park and welcome to WWDC.

3
00:00:35 --> 00:00:46
We all look forward to this event and this week as we get to share our latest platform advancements and innovations with our developer community and with our users.

4
00:00:46 --> 00:00:53
WWDC also gives us the chance to celebrate our incredible global community of developers.

5
00:00:53 --> 00:00:58
The energy around Apple platforms has never been stronger.

6
00:00:58 --> 00:01:06
Developers are building more apps than ever, with well over 1,000 submissions to the App Store every hour.

7
00:01:06 --> 00:01:17
We're also investing in the next generation through 20 Apple Developer Academies around the world, which have already helped tens of thousands of students begin their journeys.

8
00:01:17 --> 00:01:26
And with WWDC, we hope to build on that momentum, inspiring even more incredible app experiences for our users.

9
00:01:26 --> 00:01:34
At Apple, we've always believed that technology should be personal, powerful, and easy to use.

10
00:01:34 --> 00:01:42
The tight integration of our hardware and our software unlocks the full potential of each of our products.

11
00:01:42 --> 00:01:56
That same philosophy drives us to design our operating systems to work together seamlessly and to create a common foundation of technologies to help developers build great experiences across their apps.

12
00:01:56 --> 00:02:07
We've seen that when we put powerful tools into people's hands, they can do extraordinary things from extraordinary places, places we only could have dreamed of.

13
00:02:07 --> 00:02:16
This is what WWDC is all about, introducing new technologies and innovations that push the limits of what's possible.

14
00:02:16 --> 00:02:25
Today, we're looking forward to sharing some exciting announcements, including our latest advancements in Apple Intelligence and Siri.

15
00:02:25 --> 00:02:43
With that, I'd like to hand it off to Craig to get us started.

16
00:02:43 --> 00:02:49
Let's talk about our next set of releases, where we have some exciting updates to share.

17
00:02:49 --> 00:02:52
This year, we put our focus into three areas.

18
00:02:52 --> 00:02:59
First, improvements that make our platforms more responsive and easier to use in your day-to-day life.

19
00:02:59 --> 00:03:08
Second, updates in trust and safety, where we're building on our commitment to provide a safe and trusted platform for kids.

20
00:03:08 --> 00:03:21
And third, a big leap forward for Apple Intelligence, with an innovative architecture that unlocks a new Siri across platforms, along with many helpful features integrated into the apps you use every day.

21
00:03:21 --> 00:03:33
This work applies to our new releases across all of our platforms, from iOS and iPadOS to watchOS and tvOS, visionOS and macOS.

22
00:03:33 --> 00:03:48
Speaking of macOS, for those of you that are WWDC regulars, you of course know that this is the moment where I relay the latest exploits of Apple's crack marketing team, and their, shall we say, unconventional methods for naming macOS releases.

23
00:03:48 --> 00:03:52
In this case, I'm afraid the story is incomplete.

24
00:03:52 --> 00:04:01
Last I saw them, they just spilled out of their recently installed Apple Park experiential ideation yurt and piled into their microbus.

25
00:04:01 --> 00:04:08
I tried to catch them, but they just handed me this note out the window before motoring northward.

26
00:04:08 --> 00:04:12
So here's all I've got.

27
00:04:12 --> 00:04:18
Dude, our chakra alignment has set our compass toward the summer of love.

28
00:04:19 --> 00:04:24
But like, further, our corporeal forms know no earthly tether.

29
00:04:24 --> 00:04:34
We shall float on a span of gold over infinite seas, flying so high, marketing is such a great job.

30
00:04:35 --> 00:04:42
Okay, well, I'm afraid they haven't returned, and I'm lacking the sensory amplifiers to crack this code.

31
00:04:42 --> 00:04:46
So I guess the great era of Mac OS names must come to an end.

32
00:04:46 --> 00:04:47
It's GoldenGate, man!

33
00:04:52 --> 00:04:58
Oh, thank goodness. Perfect.

34
00:04:58 --> 00:05:03
Our next version of Mac OS is Mac OS GoldenGate.

35
00:05:03 --> 00:05:10
All right, with that behind us, let's start with improvements that will make our platforms better than ever.

36
00:05:10 --> 00:05:18
Our products are an integral part of daily life, helping us at work, at school, at home, and everywhere in between.

37
00:05:18 --> 00:05:22
So naturally, we all have high expectations for them.

38
00:05:22 --> 00:05:31
And we are always challenging ourselves to make our products ever more responsive, even more reliable, and that much more delightful to use.

39
00:05:31 --> 00:05:40
So instead of just introducing a host of new features, we're also taking the features you already rely on and making them even better.

40
00:05:40 --> 00:05:45
Because we believe the best operating systems aren't just built on big breakthroughs.

41
00:05:45 --> 00:05:48
They're built on sweating the details.

42
00:05:48 --> 00:05:50
To tell you more, here's Stacey.

43
00:05:53 --> 00:06:02
Tension to detail has always been core to Apple's DNA.

44
00:06:02 --> 00:06:10
And this year, our teams doubled down on our relentless dedication to make the experience feel more polished and intuitive.

45
00:06:10 --> 00:06:18
We scoured every part of the OS for opportunities to refine our systems, from the UI to the foundations.

46
00:06:18 --> 00:06:21
Nothing was off-limits, no enhancement too small.

47
00:06:21 --> 00:06:51
we made things faster smoother even easier to use and we took care of a bunch of things you've been asking about we're excited to share some highlights of this work with you to kick things off let's talk about one of the key themes of this effort design to tell you more here's shabam last year we introduced our most ambitious cross-platform design update ever with liquid glass which made

48
00:06:51 --> 00:07:01
apps and experiences even more expressive and delightful like with all major design updates there's a natural process where we take a bold leap forward, and then we continue to iterate.

49
00:07:01 --> 00:07:13
Part of how we do this is by listening to users and developers. Our team really appreciates your feedback, and we considered it deeply as we refined the new design over the past year.

50
00:07:13 --> 00:07:32
Now, we're making some additional refinements, starting with updating the foundations of how Liquid Glass is built. To ensure exceptional readability, we tuned Liquid Glass so it it diffuses complex content behind it much more effectively while also creating more depth and separation. While we think this is a great new default

51
00:07:32 --> 00:07:56
look, we also know that some users would like liquid glass to be even more clear and others prefer a more tinted appearance. Since everyone's preference varies, we're adding a new slider and settings to adjust liquid glass so you can set it anywhere from ultra clear to fully tinted. It's a whole new way to personalize your experience across apps. And for developers who have already

52
00:07:56 --> 00:08:18
adopted Liquid Glass, these customizations apply in your apps right away. In addition to improving Liquid Glass, we also wanted to reincorporate some of the cornerstones of macOS design that our users have always loved. For example, to help better distinguish larger groups of controls and text from the content beneath them, there is now a more uniform toolbar across the top of

53
00:08:18 --> 00:08:40
apps. This keeps text labels and headings legible while also providing greater structure. And to further refine the structure of apps on Mac, sidebars now expand to the very edges of the window. This helps reduce distractions along the left edge and it delivers the same beautiful refractions as content scrolls underneath to create a more expansive feeling across your apps.

54
00:08:40 --> 00:09:01
Sidebar icons also regain their color, making it easier to distinguish apps and identify which window is active in the foreground. And every window on macOS now has the same tighter corner radius, ensuring greater consistency across all of your apps, even if they haven't been updated. Finally, let's talk about app icons.

55
00:09:01 --> 00:09:16
Last year, we redesigned all of our app icons to establish a more harmonious look across apps and platforms. This year, we're taking this new design even further by integrating additional layers of liquid glass directly into the icon artwork itself.

56
00:09:16 --> 00:09:24
Not only does the refraction between these layers add character to each icon, but this new approach also makes icons appear sharper and more defined.

57
00:09:25 --> 00:09:31
They look great in your dock, on your home screen, or when they're put into an all-clear look.

58
00:09:32 --> 00:09:39
Together, these updates culminate in an even more focused and approachable experience across your apps and across platforms.

59
00:09:40 --> 00:09:41
Now, back to Stacey.

60
00:09:42 --> 00:09:46
These design improvements are just the beginning.

61
00:09:46 --> 00:09:55
As we embarked on this journey to raise the bar for our products even higher, we also spent a lot of time digging into the fundamentals.

62
00:09:55 --> 00:10:02
Things like memory usage, CPU utilization, networking operations, display rendering.

63
00:10:02 --> 00:10:04
I could go on and on.

64
00:10:04 --> 00:10:06
But really, the point is this.

65
00:10:06 --> 00:10:12
We optimize the parts of the system that make a big difference in the responsiveness of our products.

66
00:10:12 --> 00:10:23
For example, system animations are smoother, like swiping between home screen pages on your iPhone, entering mission control or moving between spaces on Mac.

67
00:10:23 --> 00:10:26
Plus, a lot of things are faster.

68
00:10:26 --> 00:10:27
I'll just name a few.

69
00:10:28 --> 00:10:33
First, iPhone and iPad apps launch up to 30% faster.

70
00:10:34 --> 00:10:43
It's kind of unbelievable that it could get even faster, but the team found a way to preload the key data apps need when you open them so they're ready to go.

71
00:10:43 --> 00:10:45
This applies to third-party apps, too.

72
00:10:46 --> 00:10:49
And content across the system loads faster.

73
00:10:50 --> 00:10:55
For instance, after you shot some new photos, it used to take a beat for them to load in your library.

74
00:10:56 --> 00:11:00
Now, new photos appear up to 70% faster.

75
00:11:00 --> 00:11:07
And when you share those photos or other files with a friend using AirDrop, they'll transfer up to 80% faster.

76
00:11:07 --> 00:11:17
And browsing files and transferring them from iPad to an external drive is up to five times faster, making it just as fast as the Finder on Mac.

77
00:11:17 --> 00:11:24
We're also doing something that will make older iPhones feel even more responsive, which has to do with our CPU scheduler.

78
00:11:24 --> 00:11:32
The CPU scheduler is a key system component that manages CPU resources across workloads as you use your iPhone throughout your day.

79
00:11:33 --> 00:11:43
Even when you're doing a ton of things at once requiring a whole lot of compute, the CPU scheduler ensures the right work is executed at precisely the right time.

80
00:11:43 --> 00:11:49
On our newer iPhones, iOS already has an advanced CPU scheduler.

81
00:11:49 --> 00:11:55
This year we further optimized it so it's even more efficient when handling performance-intensive workloads.

82
00:11:55 --> 00:12:02
And not only that, but we also figured out a way to bring it to older models, all the way back to iPhone 11.

83
00:12:02 --> 00:12:12
And yes, that does mean that iOS 27 is supported on iPhone 11 and all of the same iPhone models as iOS 26.

84
00:12:12 --> 00:12:22
This continues our industry-leading support and means iOS 27 is available to more users than any iOS release ever.

85
00:12:22 --> 00:12:32
There's also some work we've done that's about getting out of your way, Clearing the path so you can work, communicate, and find the things you're looking for without speed bumps.

86
00:12:32 --> 00:12:40
Like with network transitions, we're making it more seamless for your iPhone to transition between cellular and Wi-Fi networks.

87
00:12:41 --> 00:12:48
We all know those moments where you find yourself jumping into control center to manually toggle off Wi-Fi to force a cellular connection.

88
00:12:48 --> 00:12:53
Like when you walk past your favorite coffee shop with that extra strong Wi-Fi signal.

89
00:12:53 --> 00:12:58
or try to go online as you get off a flight and realize you're still connected to the airline's network.

90
00:12:58 --> 00:13:05
To help you avoid moments like these, we've made your iPhone smarter about when to stay connected and when to switch.

91
00:13:06 --> 00:13:13
And for those times when you're in a low-bandwidth situation, sending a big photo or video and messages will no longer slow your conversation.

92
00:13:14 --> 00:13:24
You'll see a new send indicator for each message when things are taking a little longer to go through, giving you clarity on exactly what has and hasn't yet been delivered.

93
00:13:25 --> 00:13:35
There's one last area I want to mention where we've made a big push to help you accomplish what you need with greater ease and reliability, and that's search.

94
00:13:35 --> 00:13:41
We've all had that moment where you search for something you know is there, but it just won't show up.

95
00:13:41 --> 00:13:50
So on iOS, iPadOS, and macOS, we've rebuilt the foundation of search that powers spotlight, photos, and mail.

96
00:13:50 --> 00:14:00
At its core is the search index, a rich catalog your device builds of all your content so it can understand what you have and where to find it.

97
00:14:00 --> 00:14:08
We re-architected the index so it's more stable, more efficient, and more comprehensive of content, both old and new.

98
00:14:08 --> 00:14:17
After you update, our new search infrastructure goes to work indexing the content of your device so it has a complete picture of what's already there.

99
00:14:17 --> 00:14:22
And as new content comes in, it's indexed almost immediately.

100
00:14:22 --> 00:14:25
So when you go to search, you'll find what you're looking for.

101
00:14:25 --> 00:14:31
Plus, in mail, a completely new ranking system surfaces even more relevant results in your top hits.

102
00:14:31 --> 00:14:39
So the email you're looking for is more likely to be the very first thing you see, even if it was sent months ago.

103
00:14:39 --> 00:14:45
Those are just some highlights of the work we've done this year to make our products smoother and more responsive.

104
00:14:45 --> 00:14:54
There are also some great features across your apps and products that we're excited to deliver, including some we've been hearing about from you.

105
00:14:54 --> 00:14:56
Let me give you a handful of examples.

106
00:14:56 --> 00:15:09
In Photos, your iCloud Shared Albums can now include all of the photos and videos from your latest adventures, because your friends on Android or Windows can join and contribute their own captures to the album.

107
00:15:09 --> 00:15:14
And what's more, Shared Albums now supports full resolution sharing.

108
00:15:14 --> 00:15:20
In the Health app, Cycle Tracking now has support for perimenopause and menopause.

109
00:15:20 --> 00:15:24
You can be notified when your cycle patterns are suggestive of perimenopause.

110
00:15:24 --> 00:15:34
Log your related symptoms and read educational information so you can understand more about what's going on with your body and be better prepared to talk to your doctor.

111
00:15:34 --> 00:15:47
On AirPods, we're delivering custom EQ so you can further personalize how your AirPods sound.

112
00:15:47 --> 00:15:59
On Apple Vision Pro, you can now turn panoramas you've shot into spatial scenes with incredible depth and realism, making your memories even more immersive.

113
00:15:59 --> 00:16:07
And you can use those panoramas as your environment, surrounding yourself with a moment that is personal to you.

114
00:16:07 --> 00:16:12
And lastly, over in Maps, we are giving flyover an incredible boost.

115
00:16:12 --> 00:16:20
Using aerial imagery combined with vision intelligence models, everything is rendered in stunning, sharp detail.

116
00:16:20 --> 00:16:32
From beautiful architectural details, to the shapes of individual trees, to the way light reflects off the glass of skyscrapers, you'll see cities around the world like never before.

117
00:16:32 --> 00:16:40
We are thrilled about how these improvements touch so many parts of the experience across our platforms.

118
00:16:40 --> 00:16:43
Now, back to Craig.

119
00:16:43 --> 00:16:56
- With meaningful design improvements, enhancements that make things even more responsive, and some great new features, we think the experience you have with your Apple products will be more delightful than ever.

120
00:16:56 --> 00:17:00
Now, let's talk about trust and safety.

121
00:17:00 --> 00:17:06
We build products that empower people and enrich their lives while helping them stay safe.

122
00:17:06 --> 00:17:22
We provide end-to-end encryption to help protect your conversations, strong privacy protections in Safari, app permissions that keep you in control of your location, photos, and more, and life-saving features like crash detection.

123
00:17:22 --> 00:17:39
Our longstanding commitment to safety is especially important when it comes to kids and teens who rely on our devices to stay in touch, to explore creativity and enhance learning, and to build their independence as they grow.

124
00:17:39 --> 00:17:46
And as parents ourselves, we're committed to building a safe and trusted platform for kids.

125
00:17:46 --> 00:17:54
So this year, we're taking a big step, expanding our child safety features with powerful and intuitive tools.

126
00:17:54 --> 00:18:00
To tell you about our approach, here's Sumbul.

127
00:18:00 --> 00:18:04
- Our work in this area is grounded in two core principles.

128
00:18:04 --> 00:18:12
First, we recognize that every child is unique and parents are in the best position to decide what works for their family.

129
00:18:12 --> 00:18:18
Second, we believe in shaping Apple's child safety features based on expert health research.

130
00:18:18 --> 00:18:33
The best available research from clinical and child development experts, as well as online safety experts, emphasizes the importance of balancing learning, creativity, and connection with establishing boundaries around technology use.

131
00:18:33 --> 00:18:44
This is critical in protecting adequate time for in-person social interaction, academics, physical activity, and sleep, all of which are essential for healthy child development.

132
00:18:44 --> 00:18:54
The research also shows that kids under 18 using personal devices benefit from age-based protections guided by adult supervision.

133
00:18:55 --> 00:19:01
And for those under the age of 13, access to personal devices should be limited and expanded when a child is ready.

134
00:19:02 --> 00:19:06
As parents, we want our kids to develop healthy digital habits.

135
00:19:06 --> 00:19:12
To help parents accomplish this, the American Academy of Pediatrics created the Family Media Plan.

136
00:19:12 --> 00:19:22
And we are working with them to adapt it into a guide for parents that helps them establish those healthy digital habits using our child safety features.

137
00:19:23 --> 00:19:33
We also continue to collaborate with researchers to understand the impact of technology on children's well-being and are committed to advancing the science in this area.

138
00:19:33 --> 00:19:40
We know many families are looking for help to make sure their kids experience only what parents think they're ready for.

139
00:19:40 --> 00:19:44
Here's Raja to show how they can get started.

140
00:19:44 --> 00:19:48
As parents, we want our kids to get started on the right foot.

141
00:19:48 --> 00:19:54
So it's important to know that the first and most important step is to create a child account.

142
00:19:54 --> 00:20:06
It immediately enables safeguards across the system tailored to their age, like blocking adult websites, only allowing age-appropriate media, and setting age-based restrictions in the app store.

143
00:20:06 --> 00:20:12
A child account also provides access to powerful parental controls.

144
00:20:12 --> 00:20:15
If your kids aren't on a child account today, don't worry.

145
00:20:15 --> 00:20:18
You can convert their existing account.

146
00:20:18 --> 00:20:24
Building off the child account this year, we focused on tackling the things parents are most concerned about.

147
00:20:24 --> 00:20:34
Like what content our kids can see, who they're allowed to talk to, when kids have access, and how parents can guide their kids' digital journey.

148
00:20:34 --> 00:20:41
And we're providing parents with recommendations based on expert guidance to help them decide what's right for their kids.

149
00:20:41 --> 00:20:45
But start with what content kids can see.

150
00:20:45 --> 00:20:49
On one hand, there's so many benefits to your child having their own device.

151
00:20:49 --> 00:20:57
You rest easier since you can stay in touch, know where they are, and provide them with great apps that help them learn and grow.

152
00:20:57 --> 00:21:04
We also worry about opening the door to too much, too quickly, and letting kids access things they're not ready for yet.

153
00:21:04 --> 00:21:12
Starting this year, parents can ensure their kids start focused by giving them access to only the content they are comfortable with.

154
00:21:12 --> 00:21:16
Then, gradually and when appropriate, add more over time.

155
00:21:17 --> 00:21:29
It begins with an easy-to-use setup assistant where parents can choose exactly what they want to allow, a few essential apps, a recommended set, or the specific ones they want.

156
00:21:30 --> 00:21:35
Over time, a child will be ready to add more, and parents can remain in control.

157
00:21:35 --> 00:21:37
To tell you more, here's Anne.

158
00:21:37 --> 00:21:46
The best way to stay on top of what our kids can do is to manage which apps they download.

159
00:21:47 --> 00:21:54
On the App Store, every app has an age rating that helps us parents decide if it's appropriate for our kids.

160
00:21:54 --> 00:21:59
When a child finds an app they want, they need to ask for permission.

161
00:21:59 --> 00:22:08
Parents then decide, with helpful context, if it's the right app for their child and review these requests right in Messages with Ask to Buy.

162
00:22:08 --> 00:22:15
This year, we're expanding what parents can approve beyond apps to the web with Ask to Browse.

163
00:22:15 --> 00:22:19
Parents need to ask when they want to view a new website.

164
00:22:19 --> 00:22:22
Parents can take a closer look before approving.

165
00:22:22 --> 00:22:27
This works seamlessly across iPhone, iPad, and Mac with Safari.

166
00:22:27 --> 00:22:33
Ask to Browse and Ask to Buy are both on by default for kids under 13.

167
00:22:33 --> 00:22:36
Parents can also enable these settings for teens.

168
00:22:36 --> 00:22:39
How about who kids can talk to?

169
00:22:39 --> 00:22:41
Parents are in control here, too.

170
00:22:41 --> 00:22:44
You can start off with just the immediate family.

171
00:22:44 --> 00:22:45
Add grandparents.

172
00:22:45 --> 00:22:49
And as kids grow, they want to connect with new people.

173
00:22:49 --> 00:22:55
Just like with apps, parents can have their kids ask for permission before connecting with anyone new.

174
00:22:55 --> 00:23:01
As parents, we also want our kids' conversations to be safe, free from inappropriate content.

175
00:23:01 --> 00:23:11
Today, if a child receives or attempts to send images or videos that might contain nudity, communication safety warns them and blurs the content.

176
00:23:11 --> 00:23:14
It even works on live FaceTime calls.

177
00:23:14 --> 00:23:22
This year, communication safety also intervenes before kids can see gore or violent content in shared images or videos.

178
00:23:22 --> 00:23:30
Next, let's talk about managing when your child has access to the apps on their personal devices.

179
00:23:30 --> 00:23:36
With Screen Time today, parents can see how much time a child is spending on their device.

180
00:23:36 --> 00:23:42
At my house, it can be tough to get the kids to put down their favorite videos or games.

181
00:23:42 --> 00:23:47
We know parents want to better manage the time their kids spend in certain apps.

182
00:23:47 --> 00:23:53
So we're providing them with a flexible and easy new way to help them do this with time allowances.

183
00:23:53 --> 00:24:06
Now, front and center are entertainment, games, and social media, with a daily time allowance recommendation that spans all three, giving parents a helpful starting point.

184
00:24:06 --> 00:24:12
Each of these categories also has its own time allowance recommendation to start with.

185
00:24:12 --> 00:24:23
These are based on a child's age and developed with the best clinical and child development experts, including, as Sumbul mentioned, the American Academy of Pediatrics.

186
00:24:23 --> 00:24:32
And we will actively work with them to update these recommendations as the science evolves and we get feedback from parents.

187
00:24:32 --> 00:24:36
When it comes to social media, we know parents are concerned.

188
00:24:36 --> 00:25:02
Experts recommend children under 13 don't use social media and that parents carefully consider their teen is ready. And because every child is unique, parents are in control and can always adjust any of the suggested allowances based on what's best for their child. We also know that every day isn't the same. A school day isn't the same as a weekend. And when it comes to school,

189
00:25:02 --> 00:25:27
we believe devices in the classroom should be used to support learning. So now parents can easily set up a schedule and choose which apps are available to kids at different times of the day. This helps kids stay focused when it matters, like during school. And schedules work hand in hand with time allowances, letting you do things like offer extra time to watch a movie on the weekend.

190
00:25:27 --> 00:25:48
And of course, parents have the ability to fully customize these schedules to fit their family's routines. Parents also want simple and intuitive ways to guide their kid's digital journey to be healthy and positive. So we've completely redesigned screen time. At a glance, can see how their kids have been using their devices.

191
00:25:48 --> 00:25:53
And in the moment, parents can quickly adjust access with just a tap.

192
00:25:53 --> 00:25:58
- Now, what about the experiences kids have when exploring within apps?

193
00:25:58 --> 00:26:06
What's possible today in apps feels almost limitless for more ways to connect with others, to AI and immersive gaming.

194
00:26:06 --> 00:26:12
But as parents, we want to make sure our children are ready for these experiences.

195
00:26:12 --> 00:26:27
While Apple's powerful controls help parents manage which apps their child can access and when, it's developers who play an important role in ensuring kids are getting age-appropriate experiences within apps.

196
00:26:27 --> 00:26:34
Many apps already offer parental controls to help shield kids from content they shouldn't see.

197
00:26:34 --> 00:26:38
We believe every app has that same responsibility.

198
00:26:38 --> 00:26:45
To help developers get started, we offer a full suite of APIs and resources.

199
00:26:45 --> 00:26:54
For example, to help protect against nudity and violent content in apps, or help ensure parents approve any new contacts in apps.

200
00:26:54 --> 00:26:59
Developers can also use the declared age range API.

201
00:26:59 --> 00:27:08
This allows them, in a privacy-preserving way, to use a child's age range to tailor their app experience accordingly.

202
00:27:08 --> 00:27:15
These and many more resources are available to help developers build safer experiences for kids.

203
00:27:15 --> 00:27:18
And now, back to Craig.

204
00:27:18 --> 00:27:29
To sum it up, we're giving parents powerful, easy-to-use tools to help manage what kids can see, who they can talk to, and when they have access.

205
00:27:29 --> 00:27:40
And for parents who want to learn more, we have a new website that highlights all of our child safety features and provides answers to common questions, including how to get started.

206
00:27:40 --> 00:27:49
Together, this makes for a big update, underscoring our ongoing commitment to building a safe and trusted platform for kids.

207
00:27:49 --> 00:27:52
Now, let's turn to intelligence.

208
00:27:53 --> 00:28:03
AI is incredibly powerful technology with the potential to shape society in profound ways and, with proper care, unlock meaningful benefits for people everywhere.

209
00:28:04 --> 00:28:15
Still, some appear to be racing forward, seemingly pursuing AI for the sake of AI, without clear regard for the people, all of us, that it's ultimately meant to serve.

210
00:28:15 --> 00:28:24
At Apple, our mission has always been to turn the potential of advanced technology into helpful and intuitive products for everyone.

211
00:28:25 --> 00:28:30
This has been true since the dawn of personal computing, and it's never been more important than today.

212
00:28:31 --> 00:28:37
We believe that truly helpful AI must be centered around you and your needs.

213
00:28:37 --> 00:28:51
This means integrating AI deep into the products you use every day, grounding it in your personal context and the apps you rely on, and of course, designing it with privacy at every step.

214
00:28:51 --> 00:29:04
This is our vision for Apple Intelligence, and today we're taking a big step forward with the next generation of Apple Intelligence that makes your Apple products more personal and useful.

215
00:29:05 --> 00:29:16
We'll tell you about its bold new architecture, how it unlocks an entirely new Siri that's much more responsive to your needs, and how it makes your apps smarter and more useful.

216
00:29:16 --> 00:29:20
So let's get started with our new architecture.

217
00:29:20 --> 00:29:24
It's centered around you and the Apple products that you use every day.

218
00:29:25 --> 00:29:29
And at the heart of Apple Intelligence are Apple Foundation Models.

219
00:29:29 --> 00:29:37
This year, we embarked on a deep collaboration with Google, leveraging the technologies behind their Gemini family of models.

220
00:29:37 --> 00:29:51
Together, we created the next generation of Apple Foundation Models for our integrated Apple Intelligence experiences and adapted these new models to run on-device and on-servers using private cloud compute.

221
00:29:52 --> 00:30:12
These powerful server and on-device models unlock a huge upgrade for Apple Intelligence with state-of-the-art understanding and reasoning and multiple modalities, like powerful image understanding and generation, so you can create realistic images, edit photos like never before, and get answers about visual content.

222
00:30:12 --> 00:30:22
And we went further, optimizing for the most capable of our Apple Silicon systems with a second, even more powerful version of our on-device model.

223
00:30:22 --> 00:30:29
This model lets supported products understand and generate speech, along with understanding text and images.

224
00:30:29 --> 00:30:38
And it delivers even higher accuracy for system-wide dictation, better natural language understanding, and incredibly expressive voices.

225
00:30:39 --> 00:30:46
Next, we integrated our models deeply into our platforms, enabling a wide range of system-wide capabilities.

226
00:30:47 --> 00:30:52
And Apple Intelligence securely coordinates across them with a new system orchestrator.

227
00:30:53 --> 00:30:56
These capabilities start with personal context understanding.

228
00:30:57 --> 00:31:04
Whether you're looking at a photo from years ago or a note you took recently, you can find what you're looking for just by asking.

229
00:31:05 --> 00:31:13
Behind the scenes, Apple Intelligence uses Spotlight and its powerful Semantic Index, which quickly surfaces what you need in the moment.

230
00:31:13 --> 00:31:17
and it works with any app that integrates with it.

231
00:31:17 --> 00:31:30
Plus, with broad-world knowledge, Apple Intelligence can go out to the web to get up-to-date information, then use private cloud compute to generate an answer, whether you're researching for school or brainstorming for a party.

232
00:31:30 --> 00:31:39
And with App Actions, Apple Intelligence can draw on the app toolbox to find the right tools from your apps to complete your request.

233
00:31:39 --> 00:31:44
Like when you're drafting an email from scratch, or editing and sharing a set of photos.

234
00:31:44 --> 00:31:53
Finally, with on-screen awareness, Apple Intelligence can tailor its assistance in the moment based on the app you're using and what you're doing.

235
00:31:54 --> 00:32:04
This powerful new architecture unlocks a wide range of helpful experiences, from an all-new Siri to intelligent features across your most used apps.

236
00:32:04 --> 00:32:10
And because they're available system-wide, they're even more useful for the things you do every day.

237
00:32:11 --> 00:32:16
Of course, like everything we do at Apple, this architecture is built privacy-first.

238
00:32:16 --> 00:32:43
Today, many AI providers talk about privacy, but by default, most of them retain your personal interactions, leaving the onus on you to defend your privacy, like using temporary chats, deleting conversations, or even turning off entire features. At Apple, we believe privacy in AI is non-negotiable. Apple Intelligence uses on-device processing, as well as private cloud

239
00:32:43 --> 00:33:08
compute, which ensures your data is not stored or accessible to Apple or anyone else. Your data is only used to execute your request, and outside experts can continue to verify this promise at any time. Now, we're excited to show you all the ways that Apple intelligence elevates your Apple products, starting with an all-new Siri. To tell you more, here's Mike.

240
00:33:10 --> 00:33:30
Siri is the personal assistant that helps you get things done just by asking.

241
00:33:31 --> 00:33:34
Still, we know there are times when you expect more from Siri.

242
00:33:35 --> 00:33:38
So we've rebuilt Siri with powerful AI at the core.

243
00:33:38 --> 00:33:44
And today, we're introducing an entirely new version of Siri, unlocked by Apple Intelligence.

244
00:33:45 --> 00:33:47
We call it Siri AI.

245
00:33:49 --> 00:33:56
Siri AI uses our new Apple Intelligence capabilities, and you can tap into them in the same ways you access Siri today.

246
00:33:56 --> 00:33:58
Like by saying, hey Siri.

247
00:33:59 --> 00:34:09
This includes personal context understanding, app actions, on-screen awareness, image understanding, and access to broad world knowledge.

248
00:34:09 --> 00:34:16
So Siri is now a profoundly more capable assistant that helps you find what you need and gets more done.

249
00:34:16 --> 00:34:23
It's also more conversational, so you can go back and forth like never before and get detailed, engaging answers.

250
00:34:24 --> 00:34:30
As you have these rich conversations, you can refer back to them with a new dedicated Siri app.

251
00:34:30 --> 00:34:43
And not only that, now Siri offers helpful visual intelligence across platforms, along with integrated tools so you can write and edit with Siri virtually anywhere you type.

252
00:34:43 --> 00:34:51
As you get more done with Siri, you'll notice how it looks fluid and intuitive, with a brand new design that reflects the power of Apple Intelligence.

253
00:34:51 --> 00:34:57
Let's take a closer look, starting with how Siri is a much more capable assistant.

254
00:34:57 --> 00:35:02
I want to get right into it and show you a few things that you can now do with Siri in your day-to-day.

255
00:35:02 --> 00:35:06
Let's start with something simple, but super useful.

256
00:35:06 --> 00:35:08
Say you've heard about a local concert.

257
00:35:08 --> 00:35:12
When's that Suki Waterhouse show in SF?

258
00:35:12 --> 00:35:16
Siri can draw on current world knowledge to ground its answers to your questions.

259
00:35:17 --> 00:35:23
"Okay, looks like the concert is on July 26th. How can I get tickets?"

260
00:35:23 --> 00:35:32
Okay, you have to enter a lottery to get the tickets.

261
00:35:32 --> 00:35:35
Remind me to sign up when the lottery opens.

262
00:35:35 --> 00:35:43
Okay, I got a reminder.

263
00:35:43 --> 00:35:47
Now let's hear one of her new singles.

264
00:35:47 --> 00:35:54
Reminders, music, the things you'd expect Siri to do, but now that much more capable.

265
00:35:58 --> 00:36:05
Next, let's see how Siri can use awareness of what's on your screen.

266
00:36:05 --> 00:36:07
Where is this exactly?

267
00:36:07 --> 00:36:14
Here, Siri can identify the location of this beautiful shot along the Santa Cruz coast.

268
00:36:14 --> 00:36:31
Oh cool, Natural Bridges State Beach. My friend recently moved somewhere around there, I wonder if he's nearby. Where's Jeff's new place? I remember he sent his new address, but I really actually don't know where it is and I certainly didn't save it anywhere.

269
00:36:32 --> 00:36:54
But Siri can just find it for me. Nice. So if I was being spontaneous, I could say, give me directions to the arch with a stop at Jeff's. With a little world knowledge and some personal context, Siri's pulling it all together. And there you go, my route, just like that.

270
00:36:54 --> 00:37:00
Let me show you one more example of Siri helping me to get something done using my personal context.

271
00:37:00 --> 00:37:29
Show me photos from up in Shasta last weekend. Siri is searching through my photos to find the ones from my family's recent ranch trip. Let's see. Okay, that's a lot of cows. Add just the ones with Bryce, Madison, and Quinn to our shared family album. So without having to go into the photos app at all, Siri can help me find just the photos I had in mind. Alright, those are awesome.

272
00:37:29 --> 00:37:53
And share them with the whole family. Easy as that. These are just a few of the ways that Siri AI is much more capable. For products that support our most advanced on-device model, we have two enhancements that are really useful throughout your day. First, a brand new voice experience that enable Siri to sound incredible and a lot more expressive.

273
00:37:54 --> 00:37:55
Just listen.

274
00:37:56 --> 00:38:00
Jaws said, team, these apricots are peak.

275
00:38:00 --> 00:38:02
Have they always been this good?

276
00:38:02 --> 00:38:03
Why has no one told me?

277
00:38:03 --> 00:38:05
I'm telling you, man, I've been changed.

278
00:38:06 --> 00:38:07
Clear the fridge.

279
00:38:07 --> 00:38:09
No, clear all the fridges.

280
00:38:10 --> 00:38:11
On it, Jaws.

281
00:38:11 --> 00:38:19
And you can customize Siri's voice like never before, adjusting expressivity and pace until it clicks.

282
00:38:20 --> 00:38:23
- Hi, let's find the right voice for you.

283
00:38:23 --> 00:38:27
I'll practice some things I like to say while you listen.

284
00:38:27 --> 00:38:28
Let me try this one out.

285
00:38:28 --> 00:38:29
You have one new message.

286
00:38:29 --> 00:38:30
Here's another one.

287
00:38:30 --> 00:38:32
Your timer is set for one minute.

288
00:38:32 --> 00:38:34
Now for some directions.

289
00:38:34 --> 00:38:37
At the next stop sign, turn right.

290
00:38:38 --> 00:38:45
- Plus, we're making a big update to system-wide dictation with a major boost in accuracy.

291
00:38:45 --> 00:38:50
It's much more precise in capturing what you say from spelling to punctuation and capitalization.

292
00:38:50 --> 00:39:02
And because it's built right into the keyboard, you can use it across the system, whether you're texting, the group chat while you're on the subway, or thinking out loud while using your journaling app.

293
00:39:02 --> 00:39:07
All these updates to Siri extend to CarPlay as well as AirPods.

294
00:39:07 --> 00:39:09
And that's far from all.

295
00:39:09 --> 00:39:10
We're going even further.

296
00:39:10 --> 00:39:15
Here's Justin to tell you more.

297
00:39:15 --> 00:39:21
- We're enabling Siri to be much more conversational, so it gives you richer answers and gets more done for you.

298
00:39:21 --> 00:39:24
This goes well beyond quick questions or one-shot tasks.

299
00:39:24 --> 00:39:33
With Siri AI, you can ask for an in-depth plan, go back and forth during a creative brainstorm, get feedback on a document, and so much more.

300
00:39:33 --> 00:39:36
And this capability is available to you system-wide.

301
00:39:36 --> 00:39:40
Plus, we've tailored this experience for each platform.

302
00:39:40 --> 00:39:46
On iOS, you can swipe down from the dynamic island to search or to start a conversation with Siri by typing.

303
00:39:46 --> 00:39:51
And of course, you can say, "Hey, Siri," or use the side button to get an in-depth answer.

304
00:39:51 --> 00:39:56
Let me show you.

305
00:39:56 --> 00:39:58
I'm excited to watch the World Cup.

306
00:39:58 --> 00:40:08
Let me ask, what's the schedule for opening weekend of the World Cup.

307
00:40:08 --> 00:40:10
There we go, there's the schedule.

308
00:40:10 --> 00:40:11
Now check this out.

309
00:40:11 --> 00:40:21
I can pull down at the bottom and I'm in a brand new conversational experience with a lot more information.

310
00:40:21 --> 00:40:25
This Brazil versus Morocco match would be great for a watch party.

311
00:40:25 --> 00:40:29
I wanna plan a watch party for the Brazil versus Morocco match.

312
00:40:29 --> 00:40:39
Give me iconic dishes from both countries.

313
00:40:39 --> 00:40:40
Here we go.

314
00:40:40 --> 00:40:42
Now check this out.

315
00:40:42 --> 00:40:47
Siri even included images.

316
00:40:47 --> 00:40:50
This is great, I would love to make tagine.

317
00:40:50 --> 00:40:55
Now I know my daughter would like to contribute one of her creations to this feast.

318
00:40:55 --> 00:41:02
What was that dessert Maria mentioned recently?

319
00:41:02 --> 00:41:08
Siri's searching across my phone.

320
00:41:08 --> 00:41:11
Pulled her message right into the conversation.

321
00:41:11 --> 00:41:13
Oh, coconut cookies.

322
00:41:13 --> 00:41:15
Sounds amazing.

323
00:41:15 --> 00:41:18
Now let's bring this all together.

324
00:41:18 --> 00:41:28
Bring these together for a one-of-a-kind menu for my watch party.

325
00:41:28 --> 00:41:33
Okay, let's see what Siri cooked up.

326
00:41:33 --> 00:41:35
I love the playful names here.

327
00:41:35 --> 00:41:38
This looks awesome.

328
00:41:38 --> 00:41:42
And Maria's coconut cookies are included here for dessert.

329
00:41:42 --> 00:41:46
I could refine this menu further with Siri or ask it to provide me recipes for these dishes.

330
00:41:46 --> 00:41:51
But for now, let's get some friends in on the plan.

331
00:41:51 --> 00:41:56
Send a message to the Gold Chasers group chat asking if they're up for a watch party.

332
00:41:56 --> 00:42:08
Include the menu.

333
00:42:08 --> 00:42:10
All right, let's see.

334
00:42:10 --> 00:42:11
Siri called out the game.

335
00:42:11 --> 00:42:13
I can scroll through.

336
00:42:13 --> 00:42:16
I can edit this if I want.

337
00:42:16 --> 00:42:17
This looks perfect.

338
00:42:17 --> 00:42:21
Let's send it.

339
00:42:21 --> 00:42:22
There we go, done.

340
00:42:22 --> 00:42:32
So as you just saw, Siri used its world knowledge, helped find personal content, and collaborated with me to land on a super original menu for the party.

341
00:42:32 --> 00:42:34
Now let's talk about Siri on macOS.

342
00:42:34 --> 00:42:38
It offers a conversational experience that's just right for the larger display.

343
00:42:38 --> 00:42:40
And you can tap into it from anywhere across your Mac.

344
00:42:40 --> 00:42:43
because we're integrating Siri into Spotlight.

345
00:42:43 --> 00:42:47
Spotlight is already where you go to find your apps, files, and more.

346
00:42:47 --> 00:42:51
And now it helps you find answers to your questions too.

347
00:42:51 --> 00:42:54
You can also kick off a conversation with system-wide context menus.

348
00:42:54 --> 00:42:58
Just control click to ask Siri about images, files, or text.

349
00:42:58 --> 00:43:01
Let me give you a closer look.

350
00:43:01 --> 00:43:06
Spotlight is of course great for finding just about anything on my Mac.

351
00:43:06 --> 00:43:11
Like this folder we use to keep track of family stuff.

352
00:43:11 --> 00:43:14
Now my son is super into 3D printing.

353
00:43:14 --> 00:43:18
His school has a setup and I want to help them expand into a dedicated space.

354
00:43:18 --> 00:43:22
I can just ask Siri for help right from Spotlight.

355
00:43:22 --> 00:43:28
How should I think about building a maker space in a shed?

356
00:43:31 --> 00:43:36
As I typed, Spotlight recognized this is a query for Siri.

357
00:43:36 --> 00:43:43
I can press return and start a conversation.

358
00:43:43 --> 00:43:51
Now, of course, this is a Mac, so I can drag this anywhere and resize it to see more.

359
00:43:51 --> 00:43:55
There's some great info in here for me to get started.

360
00:43:55 --> 00:44:04
Now, Siri can also answer specific questions about content on my Mac, like these quotes I have for different sheds.

361
00:44:04 --> 00:44:10
They're all formatted differently, really hard to compare.

362
00:44:10 --> 00:44:22
I can select multiple files, bring up the context menu, and I've got a new Siri field that I can use to quickly ask a question about my selection.

363
00:44:22 --> 00:44:28
Compare these and help me pick one.

364
00:44:28 --> 00:44:32
Siri's analyzing the three files, and check this out.

365
00:44:32 --> 00:44:37
Gave me a great table to help visualize it and explain how they stack up.

366
00:44:37 --> 00:44:44
Let's also factor in a problem my son hit with the old setup at school.

367
00:44:44 --> 00:44:50
Luke mentioned an electrical problem.

368
00:44:50 --> 00:44:53
Which one will fix it?

369
00:44:53 --> 00:44:58
Now check this out, Siri can also deal with my misspelling.

370
00:44:58 --> 00:45:08
Siri's searching across my messages and emails to find what my son shared.

371
00:45:08 --> 00:45:09
All right, here we go.

372
00:45:09 --> 00:45:13
Siri found the message and used it to give me a clear recommendation.

373
00:45:13 --> 00:45:17
Now if I go with this recommendation, there's a lengthy lead time.

374
00:45:17 --> 00:45:29
Going to ask Siri, draft a friendly email to them asking if they can accelerate the delivery.

375
00:45:29 --> 00:45:32
Now Siri's writing an email from scratch to Modular Workshops.

376
00:45:32 --> 00:45:41
And just like that, Siri extracted the contractor's email and name, called out why we picked them, and pitched to shorter lead time.

377
00:45:41 --> 00:45:43
This is awesome.

378
00:45:43 --> 00:45:48
So those are just a few ways you can get more done with Siri AI across iPhone and Mac.

379
00:45:48 --> 00:45:50
Now back to Mike.

380
00:45:50 --> 00:45:58
- We're bringing Siri AI, including its rich conversational experience, to even more platforms, like iPadOS.

381
00:45:58 --> 00:46:05
And for moments when you wanna go back to a detailed response, you can turn to the dedicated Siri app across your Apple products.

382
00:46:05 --> 00:46:12
The Siri app makes it easy to revisit an existing conversation or kick off a new one.

383
00:46:12 --> 00:46:19
And when you open it, you can see an overview of your conversational history, which is synced privately with iCloud.

384
00:46:19 --> 00:46:27
So you can start chatting with Siri on your iPhone, pick up where you left off on your iPad, and wrap up on your Mac.

385
00:46:27 --> 00:46:36
We're also tailoring Siri AI for watchOS, so you can ask questions and take action right on your wrist.

386
00:46:36 --> 00:46:44
And you can also tap into the Siri app using the new app grid on Apple Watch.

387
00:46:44 --> 00:46:53
On VisionOS, Siri AI takes full advantage of the power of spatial computing with a 3D visualization of Siri that you can place anywhere in your space.

388
00:46:54 --> 00:46:58
And you don't have to say, hey Siri, just look at Siri and start speaking.

389
00:46:59 --> 00:47:02
Tell me about the show Silo and check if I'm free when it premieres.

390
00:47:02 --> 00:47:09
We can't wait for all the ways that rich conversations with Siri will elevate your experience across our platforms.

391
00:47:10 --> 00:47:12
Now, over to Seb.

392
00:47:13 --> 00:47:19
There are even more ways to get things done with Siri AI.

393
00:47:19 --> 00:47:24
First, Siri now offers powerful visual intelligence across our platforms.

394
00:47:24 --> 00:47:29
On iPhone, it's integrated right into the camera app with a brand new Siri mode.

395
00:47:29 --> 00:47:33
It allows you to get information and take action on what's in front of you.

396
00:47:33 --> 00:47:39
Simply tap the shutter button to let Siri see what you see and give you a useful response.

397
00:47:39 --> 00:47:44
And you can pull down to get rich details and ask follow-up questions.

398
00:47:44 --> 00:47:49
Your images and conversations are saved to the Siri app so you can easily revisit them.

399
00:47:49 --> 00:47:57
And this deep image understanding is powered by Apple Foundation models that run on private cloud compute to protect your privacy.

400
00:47:57 --> 00:48:02
The Siri mode also suggests relevant actions in the moment based on what's in front of you.

401
00:48:02 --> 00:48:15
For example, you can point your iPhone at a plate of food to get nutritional insights, or if you're grabbing a bite with friends, you can point your iPhone at the bill, then select what you ordered to split the tab with Apple Cash.

402
00:48:15 --> 00:48:32
We're also bringing visual intelligence to macOS, where you can access it with a dedicated keyboard shortcut, then select something on your Mac display and type directly to Siri to get a helpful answer.

403
00:48:32 --> 00:48:38
And you can go back and forth to learn more.

404
00:48:38 --> 00:48:40
Plus it's easy to take action with what's on your screen.

405
00:48:40 --> 00:48:50
So if you're looking at a schedule, just bring up visual intelligence and it suggests relevant actions like adding multiple events to your calendar all in one go.

406
00:48:50 --> 00:48:57
On iPad, you can use visual intelligence to ask Siri about what's on your screen, search visually and take action.

407
00:48:57 --> 00:49:01
And it's integrated right into the screenshot experience.

408
00:49:01 --> 00:49:07
- Visual Intelligence with Siri is also coming to Vision OS, where you can ask about things just by looking at them.

409
00:49:07 --> 00:49:12
- Will this work as a carry-on for my flight in September?

410
00:49:12 --> 00:49:19
- Siri recognizes what you're looking at and it can combine world knowledge and personal context to provide a helpful answer.

411
00:49:19 --> 00:49:28
- Yes, the REI Flash 22 pack should easily work as a carry-on for your flight to Iceland in September.

412
00:49:28 --> 00:49:33
- You can also ask Siri about physical objects around you.

413
00:49:33 --> 00:49:41
- Yes, your Loa hiking boots will physically fit inside the REI Flash 22 pack, but it will be a tight squeeze.

414
00:49:41 --> 00:49:47
- Now, in addition to visual intelligence, writing tools are now more powerful and integrated into the Siri experience.

415
00:49:47 --> 00:49:51
So you can write with Siri virtually anywhere you type.

416
00:49:51 --> 00:49:54
Suppose you're kicking off a document at work.

417
00:49:54 --> 00:50:02
Just describe what you need in natural language and Siri can generate a draft from scratch to get the ball rolling.

418
00:50:02 --> 00:50:09
And when you write with Siri in mail and messages, it can reflect how you usually communicate with a specific colleague or friend.

419
00:50:09 --> 00:50:16
For example, if you normally send your manager quick and direct bullet points, that's what you'll get when you draft an email with Siri.

420
00:50:16 --> 00:50:18
You can also get feedback on your writing.

421
00:50:18 --> 00:50:26
Just select what you've written, then ask Siri how it sounds, and Siri gives you helpful tips and suggestions to improve your work.

422
00:50:26 --> 00:50:33
What's great is as you type in apps across the system, Apple Intelligence now automatically proofreads for you.

423
00:50:33 --> 00:50:35
No need to take an extra step.

424
00:50:35 --> 00:50:42
And automatic proofreading is available system-wide, including most third-party apps.

425
00:50:42 --> 00:50:52
- Those are just some of the ways that Siri AI is deeply integrated across our platforms, putting a wide range of useful tools right at your fingertips.

426
00:50:52 --> 00:50:55
And now, back to Craig.

427
00:50:55 --> 00:51:02
- We couldn't be happier to bring you Siri AI, our new version of Siri powered by Apple Intelligence.

428
00:51:02 --> 00:51:06
It's more intelligent, knowledgeable, and capable.

429
00:51:06 --> 00:51:11
It enables rich conversations and lets you refer back to them with a dedicated app.

430
00:51:11 --> 00:51:15
Plus, Siri AI has expansive visual intelligence.

431
00:51:15 --> 00:51:18
integrated tools for writing, and more.

432
00:51:18 --> 00:51:25
Siri AI will be available in English to start, and will quickly expand support for more languages.

433
00:51:25 --> 00:51:31
Now, let's turn to how Apple Intelligence is integrated deeply across your apps.

434
00:51:31 --> 00:51:47
With its new architecture and capabilities, it powers so many helpful features to simplify the things you do every day, from browsing the web and Safari, to staying on top of your communication and schedule, editing your favorite photos, and more.

435
00:51:47 --> 00:51:51
To tell you about some of the features Apple Intelligence powers in apps, here's Beth.

436
00:52:05 --> 00:52:14
With Apple Intelligence, we're bringing you exciting new features across apps like Safari, passwords, messages, and more.

437
00:52:14 --> 00:52:15
Let's start with Safari.

438
00:52:16 --> 00:52:21
Safari was designed to help you move fast and stay focused on what matters.

439
00:52:21 --> 00:52:27
One of the ways we're delivering on that this year is a simpler, smarter way to manage your tabs.

440
00:52:27 --> 00:52:32
We all know how quickly a browsing session can lead to lots of open tabs.

441
00:52:32 --> 00:52:38
Today, Safari provides helpful tools like profiles and tab groups to manage your browsing.

442
00:52:38 --> 00:52:41
But it can still be a lot of work to keep track of everything.

443
00:52:42 --> 00:52:47
Now, Safari can use Apple Intelligence to tame your tabs by organizing them into topics.

444
00:52:48 --> 00:52:57
Safari analyzes each page, identifies similarities, and then brings related tabs together, making it easier to find what you're looking for.

445
00:52:57 --> 00:53:04
And Safari keeps you organized. It adds new related tabs to a topic, even as you browse.

446
00:53:04 --> 00:53:10
When you're done for the day, you can close an entire topic, or save it for later as a tab group.

447
00:53:10 --> 00:53:15
Safari also uses Apple Intelligence to help you stay on top of updates you care about.

448
00:53:15 --> 00:53:23
Sometimes we keep tabs open because we're waiting for something, like camp sign-ups, or a product to come back in stock.

449
00:53:23 --> 00:53:28
That can lead you to constantly refresh to see if something's been updated.

450
00:53:28 --> 00:53:34
Now, Safari can automatically monitor a page on your behalf with Notify Me.

451
00:53:34 --> 00:53:41
You can use natural language to tell Safari what you're looking for, and then close that tab with peace of mind.

452
00:53:41 --> 00:53:47
And later, when Safari detects the change, you'll get a notification so you can take action.

453
00:53:47 --> 00:53:53
Of course, all the intelligence we're adding to Safari is built privacy first.

454
00:53:53 --> 00:54:03
Unlike some browsers with AI that track your every move, Safari helps you stay in control of your sensitive browsing data by not sharing it with anyone, not even Apple.

455
00:54:03 --> 00:54:08
Next, let's talk about how Safari is even more personal.

456
00:54:08 --> 00:54:13
Wouldn't it be great if you could tailor the content of a webpage to your particular needs and preferences?

457
00:54:13 --> 00:54:17
Like adding a button to the toolbar to save and rate recipes you've tried?

458
00:54:17 --> 00:54:21
Now, with Describe an Extension, you can do just that.

459
00:54:21 --> 00:54:30
Simply describe what you want in natural language, and Safari can create a custom extension that adapts webpages just for you.

460
00:54:30 --> 00:54:37
Safari also works with the Passwords app to upgrade your security using Apple Intelligence.

461
00:54:37 --> 00:54:44
Passwords already alerts you about weak and compromised passwords, but going to each site and changing them can take time.

462
00:54:44 --> 00:54:51
Now, you can automatically update eligible accounts to strong passwords with just a tap.

463
00:54:51 --> 00:55:06
Behind the scenes, Passwords uses Apple Intelligence and Safari to agentically take action on your behalf, securely navigating through each website to sign in and fix your passwords, giving you peace of mind without the hassle.

464
00:55:06 --> 00:55:20
Apple Intelligence also helps you stay on top of your communication and schedule with useful features that put relevant information at your fingertips across messages, mail, calendar, and the phone app right when you need it.

465
00:55:20 --> 00:55:33
Now, Messages uses Apple Intelligence to understand the context of your conversations and offers one-tap suggestions, making it easier than ever to get things done, like creating a reminder or a note.

466
00:55:33 --> 00:55:38
Or if someone asks for photos, Messages helps you find the right ones.

467
00:55:38 --> 00:55:48
Just tap Search for Photos, and Messages recognizes keywords, locations, and people named in your library to find the best options.

468
00:55:48 --> 00:55:59
When you receive an email, Mail also offers more capable suggestions based on the context, letting you quickly take action with your favorite apps, including third-party apps.

469
00:55:59 --> 00:56:07
Plus, Calendar now uses Apple Intelligence to let you add events to your schedule just by describing them in natural language.

470
00:56:07 --> 00:56:15
As you type, Calendar identifies Elena as someone in your contacts, puts magic donuts in the location, and adds a title.

471
00:56:16 --> 00:56:18
It's also easier to edit events.

472
00:56:18 --> 00:56:25
So if you update a weekly team lunch to every other week, the calendar app adjusts the frequency for you.

473
00:56:25 --> 00:56:29
Apple intelligence also powers a useful feature in the phone app.

474
00:56:29 --> 00:56:37
Now, when you call a business, call context can proactively surface relevant information from across your apps.

475
00:56:37 --> 00:56:44
So if you call an airline to change your flight, the phone app can automatically find your confirmation code in mail.

476
00:56:44 --> 00:56:48
So you have exactly what you need right when the call starts.

477
00:56:48 --> 00:56:54
And the phone app looks at who you're calling, not what you're saying, to surface helpful info.

478
00:56:54 --> 00:56:59
It runs entirely on device, so nothing is shared with Apple or anyone else.

479
00:56:59 --> 00:57:09
Now, over to Cecilia to show you a few more apps with brand new intelligence.

480
00:57:09 --> 00:57:17
- Let's talk about how you can use Apple Intelligence to streamline the things you do every day in home and shortcuts.

481
00:57:17 --> 00:57:24
Now, Apple Intelligence makes it easier to stay on top of things at home, starting with accessory notifications.

482
00:57:24 --> 00:57:28
They're a great way to know what's going on, but they can also add up.

483
00:57:28 --> 00:57:35
The Home app draws on Apple Intelligence to understand related notifications as a single activity.

484
00:57:35 --> 00:57:40
So you get one notification that continues to update as this activity happens.

485
00:57:40 --> 00:57:46
We've also got some cool new tricks for one of the most popular home accessories, cameras.

486
00:57:46 --> 00:57:55
The Home app can now use Apple Intelligence to analyze recorded clips from compatible cameras and generate descriptions that summarize what happened in them.

487
00:57:55 --> 00:58:02
And because it understands what happened in the video, it can pull up relevant footage from all your cameras.

488
00:58:02 --> 00:58:11
So when you play a clip, the Home app seamlessly connects them together to give you the whole picture, like what exactly has been going on in your backyard.

489
00:58:11 --> 00:58:16
This deeper level of understanding also enables you to search through camera clips by what was captured.

490
00:58:16 --> 00:58:23
Before you even type, the Home app elevates the most important clips you might want to review at the very top of search.

491
00:58:23 --> 00:58:32
Or if you're still looking for that one specific moment, like a package delivery, you can find exactly what you're looking for across multiple cameras.

492
00:58:32 --> 00:58:37
So that's how Apple Intelligence helps you understand what's going on at home.

493
00:58:37 --> 00:58:45
Plus, you can now view your recorded clips in even greater detail with 4K resolution on supported cameras.

494
00:58:45 --> 00:58:50
Another place where Apple Intelligence is enhancing your daily workflows is Shortcuts.

495
00:58:50 --> 00:58:56
Shortcuts helps people get more done faster by automating all kinds of things.

496
00:58:56 --> 00:59:01
And we're taking a big step forward to make shortcuts more approachable than ever.

497
00:59:01 --> 00:59:08
You can already connect hundreds of system and app actions to simplify your common multi-step tasks.

498
00:59:08 --> 00:59:14
While super powerful, the process of creating these shortcuts can feel, well, complicated.

499
00:59:14 --> 00:59:20
So we're making it easier than ever by letting you simply describe a shortcut.

500
00:59:20 --> 00:59:29
Shortcuts now uses Apple Intelligence to reason over your natural language description and assemble all the required steps for you just like that.

501
00:59:29 --> 00:59:34
Say you want to let your partner know what time you'll get home every time you leave work.

502
00:59:34 --> 00:59:40
You could type something like, when I'm leaving work, message Pedro I'm on my way with my ETA.

503
00:59:40 --> 00:59:44
Shortcuts then pulls together the system and app actions you need.

504
00:59:44 --> 00:59:53
In this case, it creates an automation that runs the shortcut when you leave your work address, calculates your ETA home with maps, and sends it with messages.

505
00:59:53 --> 01:00:01
If you see something you need to tweak or add, just describe it, and Shortcuts makes the change, like automatically playing your favorite podcast too.

506
01:00:01 --> 01:00:06
It's the perfect superpower to stay on top of things every day.

507
01:00:06 --> 01:00:09
There's never been a better time to try out shortcuts.

508
01:00:09 --> 01:00:17
Now, I'll pass it over to Leslie to tell you about how Apple Intelligence helps you express yourself.

509
01:00:17 --> 01:00:25
- Apple Intelligence helps you bring your creativity to the next level with an entirely new version of Image Playground.

510
01:00:25 --> 01:00:30
This year, we're re-imagining the experience with powerful image models at the core.

511
01:00:30 --> 01:00:42
Image Playground now lets you make high-quality images in pretty much any style you want, including photorealistic, thanks to our new generative model that runs on private cloud compute.

512
01:00:42 --> 01:00:51
This is a major upgrade for image generation across our platforms, giving you a more powerful way to bring your imagination to life.

513
01:00:51 --> 01:01:04
From stunning nature scenes to fun images using multiple people from your photos library, you can also transform your photos into endless styles just by describing what you want in natural language.

514
01:01:04 --> 01:01:10
And with Private Cloud Compute, your photos are never stored or shared, even with Apple.

515
01:01:10 --> 01:01:13
We're also giving you brand new ways to modify your images.

516
01:01:13 --> 01:01:19
Let's say you're planning a birthday party for your friends and you want to make an image for the invite.

517
01:01:19 --> 01:01:26
You can start with a person from your photos library and a simple description to make a fun image of her with a birthday cake.

518
01:01:26 --> 01:01:29
Then describe the changes you want to make.

519
01:01:29 --> 01:01:34
And since this is a mystery-themed party, let's make sure she looks the part.

520
01:01:34 --> 01:01:38
It's really easy to refine your images using touch too.

521
01:01:38 --> 01:01:47
Just circle over an object to highlight it, like that cake, to do things like move or resize it, or describe your change in natural language.

522
01:01:47 --> 01:01:52
So with the cake selected, you can add candles to give it that perfect finishing touch.

523
01:01:52 --> 01:02:05
You also have more ways to use the images you create, like choosing the right dimensions for what you're working on, including a landscape image for your small business's website, or a portrait image for your flyer.

524
01:02:05 --> 01:02:12
Image Playground is integrated across the system, so it's great for making images for things like messages backgrounds.

525
01:02:12 --> 01:02:19
And we're bringing it into more experiences so you can generate contact posters and lock screen wallpapers.

526
01:02:19 --> 01:02:28
Plus, you get suggestions made just for you, which are inspired by your photos with your favorite locations and activities.

527
01:02:28 --> 01:02:33
And for developers, these new features are available with the Image Playground API.

528
01:02:33 --> 01:02:39
This all-new Image Playground experience opens up a whole new world of possibilities for your creativity.

529
01:02:40 --> 01:02:41
Now, over to Alok.

530
01:02:41 --> 01:02:51
In addition to the images you dream up, we're giving you brand new Apple Intelligence capabilities for the photos you capture.

531
01:02:51 --> 01:02:55
At Apple, we have a deep respect for the craft of photography.

532
01:02:56 --> 01:03:05
And so our goal for bringing AI into the Photos app is to help photographers enhance their images in ways that respect the original moment.

533
01:03:06 --> 01:03:14
And now photos can tap into the new, more powerful image models of Apple intelligence to make incredible edits possible.

534
01:03:14 --> 01:03:20
We have three features to help make your compositions frame worthy.

535
01:03:20 --> 01:03:24
First, the popular cleanup tool is getting a big upgrade.

536
01:03:24 --> 01:03:34
So you can remove distractions with better quality and more realistic infill, even when the scene is complex like this.

537
01:03:34 --> 01:03:38
Next, sometimes you wish you had a little more space around your subject.

538
01:03:38 --> 01:03:41
You need a different aspect ratio for a project.

539
01:03:41 --> 01:03:53
Now you can expand your images with the extend tool to give your subjects more breathing room or to straighten a crooked horizon without cropping out anything important.

540
01:03:53 --> 01:03:57
And we have one more super impressive new editing tool.

541
01:03:57 --> 01:04:05
One that builds on cleanup and extend and our deep understanding of spatial models thanks to Apple Vision Pro.

542
01:04:05 --> 01:04:11
Have you ever looked at a photo and wished you would have had more time to think about the composition when you captured it.

543
01:04:11 --> 01:04:23
Maybe you accidentally framed a sign above someone's head, or the photo would have been more symmetrical if you had stepped to the right, or you missed eye contact with your subject by a fraction of a second.

544
01:04:23 --> 01:04:29
Now, with spatial reframing, you can fix all these things after the photo was taken.

545
01:04:29 --> 01:04:31
Let me show you how it works.

546
01:04:31 --> 01:04:36
Let's take a look at a picture I recently took of my kids.

547
01:04:36 --> 01:04:44
I love this cute photo of my son and daughter celebrating their last day of school, but I wish I'd spent more time thinking about the framing.

548
01:04:44 --> 01:04:50
When I tap edit, you'll see a new tools option in the lower right.

549
01:04:50 --> 01:04:59
Here I'll find all the editing features that use our Apple intelligence image models and private cloud compute, including spatial reframing.

550
01:04:59 --> 01:05:01
Let's tap reframe.

551
01:05:01 --> 01:05:05
Once the image loads, you'll see that the UI is super simple.

552
01:05:05 --> 01:05:09
You can interact with the photo directly to adjust the framing.

553
01:05:09 --> 01:05:18
I can touch, drag, and the perspective shifts as if I had repositioned the camera in the original scene.

554
01:05:18 --> 01:05:25
This capability uses on-device spatial models, so you can preview the effect in real time.

555
01:05:25 --> 01:05:34
And as you adjust the photo, you'll see a blur around the edges of the original image, which will be filled in later by our generative models.

556
01:05:34 --> 01:05:40
And I can pinch to zoom in, closer, and there.

557
01:05:40 --> 01:05:41
That looks great.

558
01:05:41 --> 01:05:44
Now I'll tap a reframe.

559
01:05:44 --> 01:05:55
This feature is super cool because it combines our advanced on-device spatial models with our powerful image generation model on private cloud compute to create the final photo.

560
01:05:55 --> 01:06:02
And even better, it only generates new content to fill in the gaps where the perspective has been shifted.

561
01:06:02 --> 01:06:08
This ensures that the reframe photo stays consistent with the original scene.

562
01:06:08 --> 01:06:11
Here's the final result, and it looks great.

563
01:06:11 --> 01:06:23
As I toggle before and after, you can see my kids are much better framed in the scene after moving the camera down, and the content that was filled in looked so natural.

564
01:06:23 --> 01:06:29
It's like I was able to go back in time and adjust my camera in the moment I snapped the photo.

565
01:06:29 --> 01:06:33
So those are the new Apple Intelligence capabilities in Photos.

566
01:06:33 --> 01:06:42
And what's great is that these features work on almost any photo in your library, even older photos or photos taken with other cameras.

567
01:06:42 --> 01:06:47
And now, back to Craig.

568
01:06:47 --> 01:06:55
- Those are just some of the ways that Apple Intelligence is up-leveling so many of the apps you use every day.

569
01:06:55 --> 01:07:05
These new features across apps will be coming to all our Apple Intelligence-supported languages, and they'll be available for free with our latest software releases.

570
01:07:05 --> 01:07:12
Some features, including image generation, have daily usage limits because they rely on powerful server models.

571
01:07:12 --> 01:07:21
Increased access is available with most iCloud+ subscription plans, which also include Apple Intelligence support for compatible home cameras.

572
01:07:21 --> 01:07:38
So that's our next generation of Apple intelligence, from an entirely new Siri that's more capable and conversational, to new ways for browsing the web, expressing your creativity, and editing your photos, all with privacy built in.

573
01:07:38 --> 01:07:48
This is a big step forward on our journey to integrate powerful AI into the core of our platforms and make our products even more personal and useful.

574
01:07:48 --> 01:07:49
And that's not all.

575
01:07:49 --> 01:07:54
we're bringing many more Apple Intelligence features to our users.

576
01:07:54 --> 01:08:00
This update will be supported on the same product models that support Apple Intelligence today.

577
01:08:00 --> 01:08:11
Our most powerful on-device model and the features it enables like expressive voices and more advanced dictation will be coming to our most capable iPhone, iPad, and Mac systems.

578
01:08:11 --> 01:08:16
Developers can start trying out our new version of Siri today.

579
01:08:16 --> 01:08:20
And for customers, we'll be launching Siri AI in beta later this year.

580
01:08:20 --> 01:08:23
we're excited to hear your feedback.

581
01:08:23 --> 01:08:29
Siri AI will not be available initially in the EU on iOS and iPadOS.

582
01:08:29 --> 01:08:34
We're working hard to find a path forward that preserves our users' privacy and security.

583
01:08:34 --> 01:08:42
In China, Siri AI and the other new Apple Intelligence features will not be available while we work through regulatory requirements.

584
01:08:42 --> 01:08:51
Next, for developers, there's a lot to be excited about with new tools and technologies to help you build incredible apps.

585
01:08:51 --> 01:09:04
Now you can bring Apple intelligence right into your apps using technologies you already know, like App Intents to help users surface the right information and get things done just by asking Siri.

586
01:09:04 --> 01:09:14
For example, because the messaging app Line indexes its content into Spotlight, users can ask Siri to help them find information from their conversations in the app.

587
01:09:14 --> 01:09:25
Or because the Calendar app, Structured, adopts App Intents, a user can just ask Siri to create an event and quickly add it to their timeline, and it's done.

588
01:09:25 --> 01:09:38
And so many of you are already building the next generation of intelligent apps, powerful, fast, and private using the on-device model through the foundation models framework.

589
01:09:38 --> 01:09:50
You can now use images as input in addition to text, extend the model capabilities with custom skills, and even use models running on servers all through the same Swift API.

590
01:09:50 --> 01:09:59
For example, many people ask me, "How can you possibly do this many presentations and always be in some version of a blue shirt?"

591
01:09:59 --> 01:10:02
Answer, I apply advanced technology.

592
01:10:02 --> 01:10:10
With an app like Daydream, I can select a photo from an event and the app can use the on-device model to identify every piece of my outfit.

593
01:10:10 --> 01:10:19
Then it uses its deep fashion expertise to help me find something right within my narrow fashion comfort zone.

594
01:10:19 --> 01:10:32
In addition to Apple Foundation models, you can now easily bring other models to run locally in your app with the full power of Apple Silicon using the new core AI framework available on all our platforms.

595
01:10:32 --> 01:10:36
That's apps and intelligence working together.

596
01:10:36 --> 01:10:41
And it comes back to what has always made the apps you make so great.

597
01:10:41 --> 01:10:47
Rich, native experiences, now enhanced with intelligence, not replaced by it.

598
01:10:47 --> 01:10:50
our app ecosystem has never been more vibrant.

599
01:10:50 --> 01:10:56
More developers, more apps, more innovation than ever before.

600
01:10:56 --> 01:11:03
Like with agentic coding, it's changing how apps get built and Xcode is the best place to build them.

601
01:11:03 --> 01:11:08
The coding assistant can now localize an entire app and interact with simulated devices.

602
01:11:08 --> 01:11:13
And you can extend its capabilities with custom skills.

603
01:11:13 --> 01:11:21
You can also choose the model and agent of your choice, now including Gemini, and connect to tools like Figma and GitHub.

604
01:11:21 --> 01:11:32
And we're re-imagining how you can test apps with the all-new Device Hub that brings every device, simulated and real, into one unified interface.

605
01:11:32 --> 01:11:44
You can now simulate multi-touch controls like swipe and pinch, change your app's appearance in one click, and even resize it dynamically to iterate like never before.

606
01:11:44 --> 01:11:51
You'll hear much more about our tools, languages, and frameworks in the platform's State of the Union.

607
01:11:51 --> 01:11:59
And we have a big week ahead with technical sessions, live forums, group labs with Apple engineers, and more.

608
01:11:59 --> 01:12:03
It's going to be an amazing WWDC.

609
01:12:03 --> 01:12:06
Now, back to Tim.

610
01:12:06 --> 01:12:09
- Thank you, Craig, and thanks to all of our presenters.

611
01:12:09 --> 01:12:18
We're excited to bring a wide range of improvements to each of our platforms, making them even more useful and delightful.

612
01:12:18 --> 01:12:29
We're also continuing to provide more features that will help kids enjoy their Apple products to the fullest while ensuring their experiences are most appropriate for them.

613
01:12:29 --> 01:12:43
And the next generation of Apple intelligence introduces new capabilities, including Siri AI that delivers rich conversations and draws upon your personal context to help you get more done.

614
01:12:43 --> 01:12:51
This, along with our other new features and capabilities, will help users get even more out of our products.

615
01:12:51 --> 01:12:58
Our new OS releases will be available as developer betas today, with a public beta coming next month.

616
01:12:58 --> 01:13:02
And they will be available to users this fall.

617
01:13:02 --> 01:13:10
On a personal note, some of the greatest highlights of my time as CEO have been events like this.

618
01:13:10 --> 01:13:21
Sharing powerful new tools with all of you, and then seeing what you create with them has been a constant reminder that imagination has no limits.

619
01:13:21 --> 01:13:30
Over the years, you have helped people connect, create, learn, and experience the world in extraordinary new ways.

620
01:13:30 --> 01:13:40
And with the incredible capabilities we introduced today and so many more still to come, I truly believe the best is still ahead.

621
01:13:40 --> 01:13:49
At Apple, creating the best products in the world to deliver experiences that enrich people's lives has always been our North Star.

622
01:13:49 --> 01:14:01
It's been the honor of a lifetime to help advance that mission with teams whose creativity, care and conviction continue to make a lasting difference in people's lives.

623
01:14:02 --> 01:14:03
Thank you all for joining us.

624
01:14:03 --> 01:14:05
Let's have a great WWDC.

625
01:14:40 --> 01:15:00
township then post them on my tiktok can you see me on your screen i've been everywhere can you see me on your screen i could take it i know that you got a lot of dreams you can make it i'm loaded i'm loaded i'm loaded i'm loaded with gratitude i'm on a journey geo gets a tide guide it's brilliant how the temple runs it's gotta be my eyes bloom colors luminar the feather grain

626
01:15:00 --> 01:15:18
touch too we outlandish too i fly delta reddit on the jet blue world of airports i'd rather kayak The gap will give me confidence, my outlook explores seven continents Heads up, this a league of legends in your head space Move slowly with the rover, but my friends know my fan base So can you see me on your screen? I've been everywhere

627
01:15:18 --> 01:15:36
I know that you got a lot of dreams, you can make it I'm loaded, I'm loaded, I'm loaded, I'm loaded up Study plus, I swing vision and speed test I took the steps, the same shop on StockX The star maker, the skyscraper, the sky god The townsfolk, the true skate and the tie-dye And lift I roll, the food are really too good to go

628
01:15:36 --> 01:15:53
DJ unfold, my set was cold, I hit them slopes I'm on Red Z for Mrs. Zip Me I'm noted at Nordstrom and NBA Don't leave a hole in I'm a time rider, now I'm striving on this driver Transformers on the Twitch, I gotta tip my Uber driver Old Navy on my ways, Warframe Wonder which of us on watch duty Name the gender, be real or AMC, I watch movies

629
01:15:53 --> 01:16:02
Good notes, I'm Minecraft, I'm LinkedIn The craft is expensive, attentive, Wimbledon Evernote's for letterheads, the latter one I loaded in So thank you for developing these apps for me and all my friends
