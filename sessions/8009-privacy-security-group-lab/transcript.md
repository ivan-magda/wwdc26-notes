---
title: Privacy and Security Group Lab
source: https://developer.apple.com/videos/play/wwdc2026/8009/
session: 8009
collection: wwdc2026
duration: 01:01:40
fetched: 2026-06-12
via: whisper.cpp ggml-large-v3 (session SD video audio)
transcription: machine-generated, no speaker diarization
---

> Machine transcription of the **Privacy and Security Group Lab** SD video via whisper.cpp (`ggml-large-v3`).
> Group labs ship no Apple transcript; this is generated from the audio. Cues are timestamped segments, **not** speaker-attributed turns.

## Transcript

1
00:00:03 --> 00:00:04
Hi, my name's David.

2
00:00:04 --> 00:00:09
I'm a technology evangelist in Apple's worldwide developer relations team.

3
00:00:09 --> 00:00:17
I cover privacy and security, and my job is to talk to developers about privacy and security, so super happy to be here.

4
00:00:17 --> 00:00:22
Thanks for tuning in today to ask questions about privacy and security.

5
00:00:22 --> 00:00:35
You tuning in and being here with us is what is gonna allow us to have a great conversation, ask questions, upvote questions, and really, when you chime in, It's going to help us talk to you about the things you want to talk about.

6
00:00:36 --> 00:00:41
Privacy and security is our favorite topic, and I've assembled an awesome panel to talk to you about it.

7
00:00:42 --> 00:00:49
I'm going to ask each panelist to introduce themselves and then tell you their focus and then tell you about their favorite privacy and security feature.

8
00:00:50 --> 00:00:53
But first, I'm going to tell you my favorite privacy and security feature.

9
00:00:54 --> 00:00:54
It's PassKeys.

10
00:00:54 --> 00:01:01
I love PassKeys, how simple it makes everything to log in and how much it protects everybody from phishing.

11
00:01:02 --> 00:01:04
Thank you, David. I love passkeys as well.

12
00:01:05 --> 00:01:09
My name is Yash. I am a security engineer at Apple.

13
00:01:09 --> 00:01:12
I work on a number of our platform security technologies.

14
00:01:13 --> 00:01:16
A common one that a lot of you may know about is code signing.

15
00:01:17 --> 00:01:22
And I would say one of my favorite privacy and security feature is developer mode.

16
00:01:24 --> 00:01:29
I'm Katie, and I lead our privacy engineering quality and tools team.

17
00:01:29 --> 00:01:33
And I think my favorite feature is advanced data protection.

18
00:01:35 --> 00:01:42
I'm Dan. I'm an engineering manager in our security engineering team, specifically our secure design team.

19
00:01:42 --> 00:01:50
We make sure that all of the features that you use as users are secure when you use them in the same way that Katie's team helps make sure that they are private.

20
00:01:51 --> 00:01:54
And my favorite security feature is stolen device protection.

21
00:01:54 --> 00:02:04
Thanks, Dan. My name is Rohit. I'm on the privacy engineering team, and my focus is on hardware and sensor privacy, so things like camera and microphone.

22
00:02:05 --> 00:02:09
My favorite security privacy feature is iCloud Private Relay.

23
00:02:09 --> 00:02:14
It's just an incredible technology, and I encourage anyone who doesn't really know about it to read up on it.

24
00:02:14 --> 00:02:15
It's quite impressive.

25
00:02:16 --> 00:02:17
Thanks, Rohith.

26
00:02:17 --> 00:02:18
Hi, I'm Emily.

27
00:02:18 --> 00:02:22
I'm a security engineer on the secure design team with Dan.

28
00:02:23 --> 00:02:30
My primary focus is private cloud compute, and coincidentally, it's also my favorite security feature.

29
00:02:31 --> 00:02:32
Easy peasy.

30
00:02:33 --> 00:02:35
All right, I'm going to jump right into the questions.

31
00:02:35 --> 00:02:37
Again, thank you for asking them.

32
00:02:37 --> 00:02:39
If they're important to you, click that upvote.

33
00:02:40 --> 00:02:43
And, you know, we'll talk about as much as we can today in the hour that we have.

34
00:02:44 --> 00:02:47
Some of us wish we had two hours if we were talking about it.

35
00:02:48 --> 00:02:53
Our first question is from Tanya RNDT.

36
00:02:53 --> 00:02:54
Tanya, I don't know, Taya?

37
00:02:55 --> 00:02:55
Tanya, maybe.

38
00:02:56 --> 00:02:57
I don't know how to say that.

39
00:02:58 --> 00:02:59
Thank you for asking.

40
00:02:59 --> 00:03:04
How is Apple ensuring that models are not hijacked through prompt injection techniques?

41
00:03:05 --> 00:03:05
Who would answer that?

42
00:03:05 --> 00:03:31
think i can probably take that so um firstly it's important to note that uh with a lot of these new agentic technologies it brings a whole new category of security and privacy risks uh one of these is a attack called indirect prompt injection so some of uh you watching may be familiar with prompt injection which is where you can try and make a model do something that it's not supposed

43
00:03:31 --> 00:03:55
to do. Indirect prompt injection is an attack where an attacker can send, say, a malicious document which contains some instructions or a message, and then when the user goes to use an agentic tool, it will read that into its context, and then it might cause that model to do something that the user wouldn't want to do. This kind of relates to a model that's been kind of discussed

44
00:03:55 --> 00:04:18
externally by researchers called the lethal trifecta, like models are at the most risk when they have access to your private data, when they can perform actions, and when they have access to untrusted context. So when we were developing features like Siri AI, we spent a lot of time figuring out how we can best mitigate this. And this is a new area. So we did a lot of really fun

45
00:04:18 --> 00:04:46
and complex and innovative security engineering and privacy engineering. And there's a range of mitigation strategies we use and we use these in combination we use some deterministic mitigations such as confirmation prompts that the user might have to tap on and we also use probabilistic mitigations as well such as spotlighting in a prompt so highlighting where a prompt may contain

46
00:04:46 --> 00:05:00
some untrusted content and we actually have a really great WWDC session on this topic so if you go into the developer app, you'll be able to go and find a session. It's around securing agentic features in the apps that you are looking to build, and it covers a lot of these same topics.

47
00:05:02 --> 00:05:24
Excellent. Moving forward, another question from the same person, and I'm not going to say the name this time. When my app sends user data to private cloud compute or to a third-party model like Anthropic or Google through the new language model protocol, what actually happens to that data, And what do I need to tell my users about it?

48
00:05:24 --> 00:05:27
I like this kind of secure and private there.

49
00:05:27 --> 00:05:29
I think this one might be for Emily.

50
00:05:29 --> 00:05:30
Yes, absolutely.

51
00:05:31 --> 00:05:35
So I can definitely talk about PCC, and I'll do that in just a second here.

52
00:05:35 --> 00:05:40
For any of the third-party AI APIs, though, our PCC guarantees won't apply to those.

53
00:05:41 --> 00:05:44
So if you send it off to Anthropic or Google or anywhere else, we can't guarantee that.

54
00:05:45 --> 00:06:00
But if you're using our PCC interfaces through the foundation model APIs and other interfaces that use PCC within our APIs, you get all the full backing of PCC.

55
00:06:00 --> 00:06:09
This includes our stateless computation guarantees as well as the non-targetability guarantees that we have with private cloud compute.

56
00:06:09 --> 00:06:16
So when a user's request goes into private cloud compute, we cannot target that user in any way.

57
00:06:17 --> 00:06:20
All of this is backed by our enforceable guarantees as well.

58
00:06:20 --> 00:06:37
So this is all cryptographically provable as well, which means that you as a developer or any security researcher out there could download all of our documentation and tooling and actually prove that we're actually backing up everything that we say we're doing for PCC.

59
00:06:37 --> 00:06:58
Well, and I think one other thing to talk about with PCC that all of the technologies Emily talked about enable us to guarantee is that when your data is sent to private cloud compute, whether it's with CRI or other features, that it's only used to fulfill that request and it is not available to be seen by Apple or anyone else.

60
00:06:58 --> 00:06:59
Absolutely.

61
00:06:59 --> 00:07:03
Apple does not collect your data at all with PCC.

62
00:07:03 --> 00:07:09
It is on that node, and when we're done with that, it is removed from that node completely.

63
00:07:10 --> 00:07:17
And if the node reboots even, there's no possible way for that node to retain any of the data either.

64
00:07:17 --> 00:07:32
I was going to add, that's a little nice tidbit to add there, which is we run in this sort of ephemeral mode where every single time you reboot the system, the volume that can hold data persistently across reboots is always wiped away and clean.

65
00:07:33 --> 00:07:42
And again, provably attestable, we prove to everybody that it is that with the attestations that come from each of the nodes.

66
00:07:42 --> 00:07:43
Yeah.

67
00:07:43 --> 00:07:57
And Emily touched quite a lot on the first part of the question, but I just wanted to add for any developer who has decided to integrate with any third-party APIs to make sure you're reading and understanding what those companies might be doing with your data.

68
00:07:57 --> 00:08:04
But look at their terms, look at any other documentation that they have, because it's very important to make sure that you understand how your users' data might be treated.

69
00:08:04 --> 00:08:14
And then you also are clear to the users with anything that you find out when you look through that. So then they also understand and can make the right choice for themselves.

70
00:08:15 --> 00:08:21
Making it clear. All right. Move to the next question from Claire Casey. Thank you.

71
00:08:21 --> 00:08:45
What distinction does Apple draw between data I collect versus data a third-party processor handles on my behalf. My voice and photo features in my app send data to outside services purely for processing. Do those count as my collection? Theirs? Both? Other? I think this question might be around nutrition labels a little bit. And so maybe for people who are a little bit less

72
00:08:45 --> 00:09:06
familiar, we have a feature in the app store called app nutrition labels. And just like a normal nutrition label, this is something that makes it easy for users to compare two different apps, get to see their privacy practices, what data they collect. So this question, whether it's linked to the user's identity or not, what are the different kinds of purposes that you use it for?

73
00:09:06 --> 00:09:28
So you are responsible for declaring all data that is collected from your app, whether it is sent to your servers or some other company service. So it really ties back to the thing that Rohith just said, where it's your responsibility to understand what are those third parties doing with that data what are their uses so then you can correctly represent the total

74
00:09:28 --> 00:09:56
picture of your app to your users all right moving on to scott g ai models do not always do what you expect what security risks are there when using the new agentic coding features in xcode and what are the best practices to mitigate them i can probably spend a bit more time talking about on this topic. So this kind of relates back to the question that came up earlier around how we

75
00:09:56 --> 00:10:22
protected against prompt injection risks. So whenever we design any of these features, so not just Siri AI, but also the features that we've added to Safari and the features that we have in Xcode, we look at implementing these in a secure by design approach. We look at the risks and particularly with the features in Xcode, we have some additional mitigations such as you can allow

76
00:10:22 --> 00:10:46
list certain common tools that you would allow Xcode to or things to call into Xcode using when you use Xcode as an MCP server. So we look on each of these use cases to figure out what is the right set of mitigations that makes sense for the user experience. But yeah, we spend a lot of time and figuring out what we need to do to secure things before they ship.

77
00:10:48 --> 00:10:49
All right.

78
00:10:50 --> 00:10:57
Yingxu asks, what is the core architecture behind private cloud compute for cloud AI processing?

79
00:10:57 --> 00:11:02
For privacy-sensitive apps, there's always an underlying worry about cloud safety.

80
00:11:03 --> 00:11:09
How does Apple mathematically ensure no one can see this data, and how do we verify that there are no hidden flaws?

81
00:11:09 --> 00:11:10
I think it's just more in-depth.

82
00:11:11 --> 00:11:11
Absolutely.

83
00:11:12 --> 00:11:14
We would love to go in-depth.

84
00:11:14 --> 00:11:16
I could speak for hours on this.

85
00:11:17 --> 00:11:19
But we can click a little bit more into that.

86
00:11:19 --> 00:11:28
First and foremost, I will start with the – we have our in-depth PCC security guide available on Apple.com.

87
00:11:28 --> 00:11:32
So I would highly recommend going and taking a read through that.

88
00:11:33 --> 00:11:34
It's really good.

89
00:11:35 --> 00:11:35
I second this.

90
00:11:35 --> 00:11:39
And I also think we have documentation that's at several different levels.

91
00:11:39 --> 00:11:53
So depending on whether you want to go to the six-hour lecture by Emily, which I know I do, but if you want it really in-depth that you have that information, but there's also ways to kind of like start if you're less familiar with these technologies.

92
00:11:54 --> 00:11:54
But go ahead.

93
00:11:54 --> 00:12:08
But absolutely. In addition to everything we've talked about so far about the stateless compute, the verifiable privacy and transparency that we have with the platform, we go to great lengths beyond that.

94
00:12:08 --> 00:12:27
Even with the new Google Cloud platform that we have for PCC as well, to add additional layers of security to enforce the privacy in PCC beyond your traditional confidential compute.

95
00:12:29 --> 00:12:40
Things like for Google Cloud, we must have two verifiable attestations for every piece of hardware, not just a single attestation.

96
00:12:40 --> 00:12:41
From two separate vendors.

97
00:12:41 --> 00:12:42
From two separate vendors.

98
00:12:42 --> 00:12:42
Thank you, Yash.

99
00:12:42 --> 00:12:45
That's a very important point there.

100
00:12:45 --> 00:12:57
So that alone is a step above what most cloud platforms actually will enforce for their privacy guarantees.

101
00:12:57 --> 00:13:17
On top of that, when it comes to processing various types of data, for example, as we have the diffusion capabilities for spatial reframing and other things like that at this point, that we've just released this week for things like that.

102
00:13:17 --> 00:13:19
We're parsing images in the cloud.

103
00:13:20 --> 00:13:21
It's a complex data format.

104
00:13:22 --> 00:13:30
We go to great lengths to isolate that parsing into separate nodes that have extra sandboxing around the processes that process that data.

105
00:13:31 --> 00:13:46
We believe this is really important for isolating all of that processing, and sometimes it adds a little latency in the stack, But that extra layer of protection makes a big difference for the overall security of the stack.

106
00:13:46 --> 00:13:56
I think it's a very interesting point that you're touching on, which is there's this whole platform that we've built around the idea of attestation and cryptographic verifiability.

107
00:13:56 --> 00:14:07
But that doesn't take away the fact that we had to harden these systems at their core outside of this concept of transparency or attestation.

108
00:14:07 --> 00:14:14
Like, these are hardened operating systems that are purpose-built to process user data in a very safe and isolated manner.

109
00:14:15 --> 00:14:20
And I think that's exactly why this as a platform is so critical for Apple.

110
00:14:20 --> 00:14:26
This is what enables us to actually process your private sensitive data in the cloud.

111
00:14:26 --> 00:14:33
Like a bunch of the functionality you would think would be part of it where it was removed to help harden that.

112
00:14:33 --> 00:14:36
Do you want to touch as well a bit on the security researcher story?

113
00:14:37 --> 00:14:37
Sure.

114
00:14:38 --> 00:14:45
So as part of the security guide that we release, we also release the virtual research environment as part of PCC.

115
00:14:46 --> 00:14:56
This is a virtual machine that is specially tooled for allowing researchers to verify our claims of each of our applications.

116
00:14:56 --> 00:15:05
So for every application that we've got in PCC, there's a way to execute that within the virtual research environment.

117
00:15:05 --> 00:15:22
And I think that this feature is really cool, because you could be an independent researcher, you could have your own laptop at home, and you can run the exact set of software that we are running on our server platforms on your Mac in a virtualized format.

118
00:15:22 --> 00:15:28
And that research, I must add, is eligible for Apple security bounty.

119
00:15:28 --> 00:15:32
And to be clear, it is not limited to just security researchers.

120
00:15:32 --> 00:15:33
Anybody can download this.

121
00:15:33 --> 00:15:36
It is fully public, and we do not limit who can download it.

122
00:15:36 --> 00:15:49
And so that means whether it's somebody that just wants to understand more about PCC or an enterprise that wants to help verify our claims so that they can make our capabilities available to their users.

123
00:15:50 --> 00:15:52
It doesn't matter who wants to research.

124
00:15:53 --> 00:15:54
It's the research for everybody.

125
00:15:54 --> 00:16:10
And it directly really addresses one of the key challenges that led us to create PCC, which is that when you send data to a server, you don't know even if this is like a company says that they're doing one thing.

126
00:16:10 --> 00:16:13
You know, like you don't know if it's going to change another day.

127
00:16:13 --> 00:16:14
It could change at any point.

128
00:16:14 --> 00:16:17
And so that's why I think this is such an amazing step forward.

129
00:16:17 --> 00:16:20
A really good point is like this is continuous transparency.

130
00:16:20 --> 00:16:24
It's not just, oh, we came out with this today, and so you can verify it today.

131
00:16:24 --> 00:16:29
If the back end that we use to process something changes, that is noticeable to you.

132
00:16:30 --> 00:16:34
And you can take that, you can take the newest software deployment and verify that once again.

133
00:16:34 --> 00:16:36
It's all done in real time.

134
00:16:36 --> 00:16:46
And to be clear, if anybody would try to compromise one of the nodes and deploy rogue software to one of the nodes, the attestation will change.

135
00:16:47 --> 00:16:47
That's right.

136
00:16:47 --> 00:16:50
And your devices will just automatically reject it.

137
00:16:50 --> 00:16:55
So, you know, it's, again, enforceable guarantees.

138
00:16:57 --> 00:16:58
Yeah, I love that.

139
00:16:58 --> 00:17:02
The values that Apple has around privacy make me proud to work here, but that it's verifiable.

140
00:17:02 --> 00:17:04
But, like, anyone can say it's private.

141
00:17:04 --> 00:17:07
But, like, being able to prove it and being open about it.

142
00:17:07 --> 00:17:07
Love it.

143
00:17:07 --> 00:17:08
Don't have to just trust us.

144
00:17:09 --> 00:17:10
You can prove it yourself.

145
00:17:10 --> 00:17:11
Don't just have to trust it.

146
00:17:11 --> 00:17:15
Okay, so a few questions have been getting upvoted, so I'm going to focus on those next.

147
00:17:15 --> 00:17:20
Michael Rowe 01 is asking security and Swift data things.

148
00:17:20 --> 00:17:21
Let's see how we do.

149
00:17:22 --> 00:17:28
Are there any new features to do security audits of my own app to ensure that my user's data remains private and secure?

150
00:17:29 --> 00:17:38
I've been using Swift data with CloudKit so far so that users own their own data but would love advice on how to audit this privacy feature.

151
00:17:38 --> 00:17:45
And maybe just throw in there, tell folks a little bit about why those are secure in the first place if someone has a moment to do that.

152
00:17:45 --> 00:17:58
I mean, I was going to talk about how I think there are a lot of great ways to do it, but I think we've all been experiencing a new way with generative AI.

153
00:17:58 --> 00:18:23
it's a great, whatever model you have, it's a great tool to use to have it look at what are your privacy and security guarantees? What do you expect that your app does? And then use it as a tool to help check that. I think it's also one thing that I'm really excited about is, you know, some, there's a lot of great security and privacy features. We only named a few that I would love

154
00:18:23 --> 00:18:46
developers to have the chance to add to their app, whether it's out-of-process pickers to help users select photos, etc. And they're also a great tool to help you build these things in and identify where you might have the opportunity to use a more private or secure technology. So I would really like have a conversation with your coding agent is a great thing to do. I think we also have

155
00:18:46 --> 00:18:55
a lot of privacy and security WWDC talks that I would recommend also taking advantage of and looking to find these things out.

156
00:18:55 --> 00:18:56
Dan, you have more ideas?

157
00:18:57 --> 00:19:10
Yeah, I think also going back to David's comment earlier, a lot of the frameworks that we provide to developers our intent is to ensure that those are secure by default and where we can private by default as well, such as the out-of-process pickers.

158
00:19:11 --> 00:19:29
So a good example here is when you use the network framework to make a request to a web server, when that uses TLS, we've spent a lot of time, particularly in the last couple of years, ensuring that that request uses the latest post-quantum cipher suites.

159
00:19:30 --> 00:19:44
And so when iOS 26 was released last year, and we launched support for these cipher suites, you could actually see on various sites a huge uptick in the amount of post-quantum TLS traffic over the internet.

160
00:19:45 --> 00:19:57
And it's really cool to see that happen when, like, we as Apple have tried to make these things easy to use where, like, developers didn't need to change anything if they used the default configuration of these APIs.

161
00:19:58 --> 00:20:02
The OS was just able to default to that more secure default.

162
00:20:02 --> 00:20:16
That's a big thing that we focus on when we're thinking about what can we make available as part of the SDK is to make it easier for you to make your app private and secure without having to tackle some of these things that are pretty difficult.

163
00:20:16 --> 00:20:25
So CloudKit's a great example of if it's a way to sync your data or sync your user's data between your user's devices.

164
00:20:25 --> 00:20:38
And so that is something where Apple can take care of syncing that data securely and making sure all of that works without you having to manage a server where all that data is being stored.

165
00:20:39 --> 00:20:43
Because that can be a lot of risk for you to take on if you're not sure about how to implement that correctly?

166
00:20:43 --> 00:21:05
I think one thing I would add, though, is, you know, we provide frameworks and API tooling to make all of this easy, but as a developer, this is a great question because it's something that I want to touch on, which is, you know, as a developer, you are in part responsible for the privacy and security of the users who choose to use your applications, right?

167
00:21:05 --> 00:21:11
And so it is great that you're thinking about the different ways in which you can provide that.

168
00:21:11 --> 00:21:19
And one of the ways is to use sort of platform-native methods of achieving that, such as the CloudKit APIs, right?

169
00:21:20 --> 00:21:26
And, I mean, providing information about security architecture will take us a long, long, long time.

170
00:21:26 --> 00:21:32
But the two things that I will add to that is think about inputs to your application.

171
00:21:32 --> 00:21:38
That is one of the best ways to think about security is what are the inputs coming into your application and how much do you trust them?

172
00:21:38 --> 00:21:43
And then for privacy, I would always say, you're collecting a user's information.

173
00:21:43 --> 00:21:45
Think about where you vend that.

174
00:21:46 --> 00:21:47
Like, who do you give that out to, right?

175
00:21:47 --> 00:21:54
And those two questions are great ways to start thinking about how do you build the most secure and the most private application for our set of users.

176
00:21:55 --> 00:21:58
And, like, I want to stress this part because it's very important.

177
00:21:58 --> 00:21:59
You know, we're all developers.

178
00:22:00 --> 00:22:01
You're all developers.

179
00:22:01 --> 00:22:03
But we're also users, right?

180
00:22:04 --> 00:22:06
You are a user of someone else's app.

181
00:22:06 --> 00:22:09
Someone else who's a developer is a user of your application.

182
00:22:09 --> 00:22:14
Think about what kind of applications you want to use and how you want applications to treat your data.

183
00:22:15 --> 00:22:16
Right.

184
00:22:17 --> 00:22:23
On to another upvoted one, kind of both sides of privacy and security here.

185
00:22:24 --> 00:22:35
Patita, if we are starting our journey to develop apps for Apple platforms, what are the main tools or frameworks we should be focusing on from a privacy and security point of view?

186
00:22:35 --> 00:22:38
So maybe we'll tackle privacy first, Rohit, what do you think?

187
00:22:38 --> 00:22:39
And then we'll do security afterwards.

188
00:22:40 --> 00:22:46
Yeah, I mean, I think we kind of touched on a lot of those frameworks that we 100% recommend any developer use.

189
00:22:46 --> 00:22:54
CloudKit is great for syncing data between users' devices, take advantage of secure on-device things like Keychain.

190
00:22:55 --> 00:23:00
We have a great CryptoKit library and encourage your users to use Passkeys.

191
00:23:00 --> 00:23:10
Like all those things are very, you know, fundamental to a lot of the protections that we can provide you as a developer to help protect your users' data.

192
00:23:11 --> 00:23:20
But I think another thing just to be mindful of is consider if you need to have access to users' data in the first place.

193
00:23:20 --> 00:23:30
Because if you don't ask a user for data, then you don't have to worry about if you're protecting it, if there's some sort of privacy or security concern that could come up in the future.

194
00:23:31 --> 00:23:36
So data minimization is always very important for any of these types of...

195
00:23:36 --> 00:23:37
I think that's a great point.

196
00:23:37 --> 00:23:44
And I think the one I would add is when thinking about data minimization is what amount of data do you really need to access?

197
00:23:45 --> 00:24:04
Because a lot of our protected data categories like location, photos, contacts, you can choose to, instead of asking for all of the data, use an out-of-process picker, which allows the user to select exactly which pieces of data they want to or think about for location, do you need the information just once?

198
00:24:05 --> 00:24:10
There's a lot of great tools to help you pick and think about exactly what data you need.

199
00:24:10 --> 00:24:27
I think that's really important to call out, too, that using Keychain or CloudKit or the other first-class SDK frameworks allows you to have the security tools for classifying what level of data protection you want to apply to each of these as well.

200
00:24:27 --> 00:24:36
So with the data protection classes that you get for free with Keychain, you can add the right ACL to your data to make sure that it's classified at that right level.

201
00:24:37 --> 00:24:49
So, for example, if it's available after first unlock and then subsequently, then you can set that versus making it always available, which would be not exactly a great thing.

202
00:24:50 --> 00:24:54
So having those tools available to you in those frameworks is also an added benefit.

203
00:24:55 --> 00:24:58
Is there some advice perhaps that we should share when it comes to telemetry?

204
00:25:00 --> 00:25:10
I think I would – data minimization, which Rohit said, is I think where you start, which is thinking about what is actually the question you want to answer.

205
00:25:11 --> 00:25:18
And one thing we emphasize is what are you going to actually do once you have that question answered?

206
00:25:18 --> 00:25:21
Like what – are you going to make a code change?

207
00:25:21 --> 00:25:23
Are you going to make a different business decision?

208
00:25:23 --> 00:25:27
kind of moving past the, like, I think this is interesting to let's make sure it's actionable.

209
00:25:28 --> 00:25:31
You want to chime in on a couple more, like, strategies to think about?

210
00:25:31 --> 00:25:52
I think another important thing with telemetry that a lot of people probably don't think about is that you should communicate to your users what data you're using and why because you want to at least let them make the decision because it is ultimately their data that they're kind of, you know, donating to you for improvement of your app

211
00:25:52 --> 00:25:55
or whatever other types of business reasons you might have to collect it.

212
00:25:56 --> 00:25:59
And ultimately, you want to make sure that they are in control of their data.

213
00:25:59 --> 00:26:05
And it's good to start by thinking about what kind of identifier do you need to collect this data with?

214
00:26:05 --> 00:26:10
Is it actually their identity? Is it Katie? Or is it just a user?

215
00:26:11 --> 00:26:18
Is this a rotating identifier where we can maybe do a session-based identifier versus a device-based identifier?

216
00:26:18 --> 00:26:21
So I think that's a great initial question to think about.

217
00:26:21 --> 00:26:29
And then there's lots of strategies out there to bucket your data, de-res your data, getting back to what's the right question.

218
00:26:29 --> 00:26:46
And then there's great exciting tools like private federated learning and differential privacy that can enable you to collect more sensitive data, but in a way that has a mathematical guarantee that it can't be connected back to the original person.

219
00:26:46 --> 00:27:02
So there's a lot of great ways to both make great decisions, how to keep improving your app but doing it in a way where users understand, feel in control, and it doesn't add a lot of risk of you storing that level of sensitive data.

220
00:27:02 --> 00:27:07
- I'd also make one final plug, which kind of goes back to Rohith's first comment and David's theory of technology.

221
00:27:07 --> 00:27:12
If you're gonna have user accounts, don't bother with allowing them to set a password.

222
00:27:12 --> 00:27:14
Just adopt pass keys from the start.

223
00:27:14 --> 00:27:17
Like, don't bother with any of the password nonsense.

224
00:27:17 --> 00:27:19
Just pass keys from default.

225
00:27:19 --> 00:27:21
You'll be in a much better place for the future.

226
00:27:21 --> 00:27:22
your users will be protected.

227
00:27:22 --> 00:27:24
- Zero to pass keys.

228
00:27:24 --> 00:27:28
- They're designed to be a complete replacement for passwords, not fishable.

229
00:27:28 --> 00:27:34
So they're syncable, you can share them if you really want to, and just not fishable.

230
00:27:34 --> 00:27:40
And it actually, as a developer, it will reduce the frictions of your password login.

231
00:27:40 --> 00:28:01
So often people forget their passwords and you have to do all these extra things that actually can even incur costs sometimes because you have to process all these requests, Making it really simple for someone to log in in a secure, unfishable way means that they can get at the value of your app or your systems or your accounts or whatever it is that you're providing much faster.

232
00:28:01 --> 00:28:02
So it's way better.

233
00:28:03 --> 00:28:04
I see why it's your favorite feature.

234
00:28:06 --> 00:28:16
I like this question, so I'm going to surface it because it lets us kind of step into some of the iCloud things, specifically some of the encryption stuff that's available in CloudKit.

235
00:28:16 --> 00:28:27
If a user stores highly personal journal entries, readings, or history in Swift data, what are Apple's recommended approaches for protecting that data while still enabling search and synchronization?

236
00:28:29 --> 00:28:30
I can take a stab at that one.

237
00:28:31 --> 00:28:36
So as I mentioned before, we have data protection classes, first of all.

238
00:28:36 --> 00:28:45
So when the data is local on device, or even before then, in the keychain, you can create a key to encrypt the data.

239
00:28:46 --> 00:28:55
And you can set the data protection class on that key to, for example, for highly sensitive journal entries, I would recommend something like, you know, only when unlocked.

240
00:28:55 --> 00:29:08
And you can even set an ACL that says only when biometrically unlocked so that the user has to give their fingerprint or their face to actually unlock that key.

241
00:29:09 --> 00:29:22
Then when storing the data locally on the device, you can further set a data protection class for the files on disk to make sure that, again, highly recommend for something like this that you would have it only when device is unlocked.

242
00:29:22 --> 00:29:45
And then when you want to extend this to CloudKit to store the data so you can synchronize across devices, we would recommend then using the CK record encrypted values to make sure that for people with advanced data protection, they are now end-to-end encrypted across all of the devices as well.

243
00:29:45 --> 00:29:55
So I think those are some of the key features that we have for protecting data when working with very sensitive data in your application.

244
00:29:55 --> 00:30:00
I think the question asked something about how do we make sure things like search and indexing would work.

245
00:30:01 --> 00:30:08
I'd like to add that I can't remember the exact name of the API, so I'd refer people to some of the developer documentation.

246
00:30:08 --> 00:30:14
But we have a class of key that allows you to lock your device but keep the data available for a certain period of time.

247
00:30:14 --> 00:30:18
I think it's, not sure if it's configurable, but it's some period of hours.

248
00:30:18 --> 00:30:25
And so, you know, we have those mechanisms where a user inputs their sensitive data and then decides to lock their device.

249
00:30:25 --> 00:30:36
If that data is encrypted and stored with this class of the key that's only viable for the next few hours, you know, that is a great moment for your application to take some background processing and go index whatever it needs to build.

250
00:30:36 --> 00:30:41
And soon thereafter, you know, the key is locked and that data becomes not visible to anything.

251
00:30:42 --> 00:30:43
Great call out.

252
00:30:43 --> 00:30:43
Yeah.

253
00:30:43 --> 00:30:47
All this talk of data classes, I say it to people sometimes and their eyes kind of glaze over.

254
00:30:48 --> 00:30:56
An example I like to tell them is, you know, that time when you've rebooted your phone or maybe you lost power because you used it all day at WWDC or something.

255
00:30:56 --> 00:31:02
And you turn it back on and you go to look at your messages or just on the lock screen and all you see is phone numbers.

256
00:31:02 --> 00:31:09
And that's because all your contacts are a data class that don't become available until your phone is fully unlocked with a passcode.

257
00:31:10 --> 00:31:14
So that's the example I use so people are going to realize what's up there.

258
00:31:16 --> 00:31:20
Next up, evolving this design is the name of the person.

259
00:31:21 --> 00:31:22
I like your username.

260
00:31:22 --> 00:31:33
With the Xcode AI coding features, can Xcode be run inside a virtual machine on an air-gapped Mac walled off from the internet to help improve security and privacy?

261
00:31:33 --> 00:31:35
I like how this person thinks.

262
00:31:37 --> 00:31:40
Imagining someone in a bunker with a MacBook.

263
00:31:41 --> 00:31:41
Yes.

264
00:31:42 --> 00:31:48
So firstly, for the existing code completion features, they're entirely on-device.

265
00:31:48 --> 00:31:48
They work offline.

266
00:31:49 --> 00:32:04
In general, though, other features, particularly ones that might use or integrate with something like Clawed Code or OpenAI's Codex, those will inevitably require your device to go out to the internet.

267
00:32:05 --> 00:32:09
So it is going to vary, but certainly code completion itself should work offline.

268
00:32:10 --> 00:32:12
Gotcha. Thank you.

269
00:32:12 --> 00:32:19
Next up, from CMDDev, how does the new Siri AI ensure privacy and security?

270
00:32:19 --> 00:32:22
For example, avoiding sharing private context with apps.

271
00:32:23 --> 00:32:26
Knowing Apple, there's definitely something cool under the hood.

272
00:32:26 --> 00:32:31
I assume this is a combination of factors, sandboxing, TCC, and more.

273
00:32:32 --> 00:32:33
Why don't you talk about that?

274
00:32:34 --> 00:32:34
Sure.

275
00:32:34 --> 00:32:41
I can maybe start, and then I think because it uses PCC, maybe we're going to go back to Emily again.

276
00:32:41 --> 00:32:51
So I do think one of the key technologies is Siri can do a lot of stuff on device, but it does want to call on larger models that are hosted within PCC.

277
00:32:51 --> 00:33:05
And so I think all the things we talked about are one of the big reasons why we can have the power of PCC models, but also take advantage of all of the personal information on your device.

278
00:33:05 --> 00:33:08
It's really that combination that I think makes Siri AI special.

279
00:33:09 --> 00:33:24
And it's really PCC that is key to that of the context, the information from your semantic index being able to be processed to enable you to get done what you want, all while doing it in a way that is protected, where PCC is protected.

280
00:33:24 --> 00:33:45
Before we get into the PCC part, because I don't want to talk about that, there is a lot of protection built on device in the realm of entitlements and sandboxing to make sure that the process that has access to your user data, that's collecting all that user data before sending it off to PCC, not every process can do that.

281
00:33:45 --> 00:33:56
It's built in a very, very secure container where your prompts get processed, provided to this very secure daemon, processes things, collects them in a format, and then vends them off to PCC.

282
00:33:57 --> 00:34:00
So there's great use of platform technology, all of them.

283
00:34:00 --> 00:34:13
My team has done a really good job over the time we've been building some of these features to ensure we have an architecture that we feel comfortable and confident we can secure using a lot of those same platform technologies that are available to developers.

284
00:34:13 --> 00:34:18
And even when third-party apps are involved, we make sure that the data is minimized.

285
00:34:18 --> 00:34:29
When you have a directive for Siri to do something, send some data to a third-party app, It's not going to just give extra data that you were not, you know, intending to donate to that app.

286
00:34:29 --> 00:34:30
It's very directed based on the query.

287
00:34:30 --> 00:34:37
Well, and I think there's – I think the question maybe also or maybe David said something about TCC.

288
00:34:38 --> 00:34:46
There's also user choice of if you want to use location with Siri, like you can choose whether you want to do that or not, you know.

289
00:34:46 --> 00:34:50
So those same technologies apply to some of our third-party apps.

290
00:34:50 --> 00:35:09
Yeah, and I think it's important to note, too, we try to stay on device as much as possible, but it's that big Apple Foundation model that when you have to go to PCC to use that foundation model, that's when we go to PCC or any other operation just is just too big to deal on device.

291
00:35:10 --> 00:35:13
Obviously, we have very powerful devices that can do a lot.

292
00:35:14 --> 00:35:24
And in many cases, the call to PCC will happen, and then it'll come back to the device and get more context if needed, and we do multi-turn.

293
00:35:25 --> 00:35:29
So we'll go back and forth with PCC to fulfill the entire request.

294
00:35:29 --> 00:35:44
So sometimes PCC might need more context from your device, and it'll surface, and then all of the protections Dan was just referencing on device still apply, where TCC might be prompting you to okay some access to additional data that hadn't been accessed before.

295
00:35:44 --> 00:36:03
And I sometimes talk about actually our silicon is in a roundabout way one of our privacy and security technologies because it enables us to do so much processing on device and be able to then have that be a place where we can have security boundary, like all of the technologies that everyone's talking about can apply there.

296
00:36:04 --> 00:36:08
It amazes me all the time what our AI ML team could do.

297
00:36:08 --> 00:36:11
I don't know if the Silicon team gets a cool talk like this, but shout out to them.

298
00:36:11 --> 00:36:20
Between the Silicon team and our AI ML team and what they can do to optimize the models to run on device, I'm amazed every day.

299
00:36:21 --> 00:36:24
And the more they can do on device, the more easier my job becomes too on VCC.

300
00:36:25 --> 00:36:31
And also to enable developers to run their own models using things like the core AI framework that we just released.

301
00:36:32 --> 00:36:39
It's incredible that we have a platform or a range of platforms that are such capable devices for using these models and technologies.

302
00:36:39 --> 00:36:40
All right, you have all convinced me.

303
00:36:40 --> 00:36:44
I'm changing my favorite privacy and security feature to Apple Silicon.

304
00:36:45 --> 00:36:46
Nice.

305
00:36:47 --> 00:36:51
So we all said TCC about six or seven times in there.

306
00:36:51 --> 00:36:56
So let's talk about it a little bit and tell everybody what TCC is.

307
00:36:56 --> 00:36:59
It's always good to just spell it out because it's really, really important.

308
00:36:59 --> 00:36:59
Rohith?

309
00:37:00 --> 00:37:13
Yeah, so TCC stands for Transparency, Consent, and Control, and it refers to various frameworks and protections that we have on device that allows for apps to ask users questions to get access to additional data.

310
00:37:13 --> 00:37:24
So if you as a developer want access to the user's photos or access to the microphone for some feature that you're building, the TCC framework allows you to prompt that dialogue and the user can make a decision.

311
00:37:25 --> 00:37:39
And really the benefit of this is that we can, as a user, have very high level of trust that an app does not have access to your microphone data if it has not prompted you for it and you've not said, yes, I want you to have my microphone data.

312
00:37:39 --> 00:37:41
Thanks.

313
00:37:42 --> 00:37:46
No, I don't think this one's going to take a long time to ask, but it's very upvoted.

314
00:37:46 --> 00:37:50
So Brandon7269, I hope that's not your pin.

315
00:37:51 --> 00:37:57
Will Siri AI have a new configurable policy in an MDM solution when released?

316
00:37:58 --> 00:38:00
Short answer is yes.

317
00:38:01 --> 00:38:06
The existing or there are already existing MDM controls available to control Siri.

318
00:38:06 --> 00:38:08
Those should still apply.

319
00:38:09 --> 00:38:16
I think this is a great one to think through, like, how you as an enterprise might want to configure some of these tools.

320
00:38:16 --> 00:38:18
Like, are there bits that you would want to use over others?

321
00:38:19 --> 00:38:30
And submit a feedback request if you have proposals or ideas of how you would want it to be configured to allow you to use some of the benefits in your enterprise environment.

322
00:38:30 --> 00:38:35
Like the new Safari feature to allow the automatic changing of passwords, for example.

323
00:38:36 --> 00:38:49
In a lot of businesses, particularly small businesses who don't necessarily have more complex single sign-on solutions available to them, Like this is a great enabler to enable you to like uplift your security for sites that don't yet support.

324
00:38:49 --> 00:38:50
Fantastic feature, by the way.

325
00:38:51 --> 00:38:51
Yeah, it's great.

326
00:38:54 --> 00:38:59
Speaking about some web stuff, expanding the things a little bit here, I wouldn't mind talking about this one.

327
00:38:59 --> 00:39:10
User Contil, are there any new enhancements to the Safari ITP tracking preventions slash trackers on the new OSs?

328
00:39:11 --> 00:39:13
Anybody want to talk about that?

329
00:39:13 --> 00:39:13
Tracking.

330
00:39:14 --> 00:39:21
So I think, you know, ITP has been a technology that we've continued to invest in for a lot of years.

331
00:39:21 --> 00:39:23
It continues to evolve.

332
00:39:23 --> 00:39:30
And I think that while we don't have this year, there aren't any specific things we want to reference.

333
00:39:30 --> 00:39:37
I think we really think it's a technology that we think is really important and we continue to invest in over the years.

334
00:39:37 --> 00:39:45
And if you want to learn more about it, then you can go find out on WebKit.org and figure out more about what it can do.

335
00:39:46 --> 00:39:46
Gotcha.

336
00:39:47 --> 00:39:48
Thank you.

337
00:39:49 --> 00:39:51
I like this one.

338
00:39:52 --> 00:39:53
M3Lixer.

339
00:39:53 --> 00:39:56
Maybe that's some sort of elite code one I should read.

340
00:39:58 --> 00:39:58
I don't know.

341
00:39:58 --> 00:39:59
Malixer.

342
00:39:59 --> 00:40:00
Malixer.

343
00:40:00 --> 00:40:00
Malixer.

344
00:40:00 --> 00:40:13
How do you effectively convey to users that your app is secure and privacy-preserving without it coming across as snake oil, security theater, or unverifiable marketing language?

345
00:40:13 --> 00:40:14
How do you make trust observable?

346
00:40:15 --> 00:40:15
I like it.

347
00:40:15 --> 00:40:17
It's a very high-level version of things.

348
00:40:17 --> 00:40:18
It's great.

349
00:40:18 --> 00:40:27
Katie mentioned earlier, but nutrition labels are one of the easiest ways before users even downloaded your app when they're thinking about, you know, maybe this is an app that I might want to use.

350
00:40:27 --> 00:40:50
it can just at a glance easily verify what is this app doing with your data if you're collecting data and you're not linking it to a user you can make that very clear saying you know saying just like a as a claim oh we don't collect any identifiable data people may not necessarily know how much credence to put into it um but it's you know the standardization of nutrition labels

351
00:40:50 --> 00:41:14
it can be a little bit uh have a little more weight we know users care a lot about nutrition labeled i've seen screenshots online yeah yeah and and within your app also just having more transparency to the user like if you have their data don't hide it from them right have a way for them to see that you have that data in their app so that there's no surprise about oh wow you've

352
00:41:14 --> 00:41:36
collected all this information about me and i had no idea there's a lot of things you can do in that way to build that level of trust without making it seem like it's some sort of security theater or something yeah one of the things we do internally and i think you guys do similar is we talk about privacy assurances and for every feature that we build and we you know help

353
00:41:36 --> 00:41:59
ensure we build privacy into across all of apple is thinking about what are our kind of promises to the user that are like simple understandable privacy promises and then and this isn't really usually a how it's a simple statement and that enables us to look at any architecture any implementation hold it up to that statement and see if it still applies and so it can really be

354
00:41:59 --> 00:42:21
kind of like a north store guiding light for a whole feature that everyone can kind of focus on to make sure we can hit it and i think that that kind of specificity helps then users feel comfortable because just like it's getting a little bit more into yes you care about privacy yes you say the right things on your website but i think the more it can be something that users

355
00:42:21 --> 00:42:41
understand what exact promise you're making that can help and then consistency like keep it up because trust is something that's not just something you earn in one day it's something that by continuing to show up build these technologies invest in them like choose to be clear with your users about how you're using their data you know that's where that trust will

356
00:42:41 --> 00:42:55
grow it's organic yeah yeah and when we start working like same as on the privacy side like when we start working on a new feature and thinking about the security of it, we do think about, like, what security story do we want to tell?

357
00:42:55 --> 00:42:57
What claims might we want to be able to make?

358
00:42:57 --> 00:43:11
And you see some of this coming through with, like, technologies like memory integrity enforcement, like maybe not the most understandable to the, like, the average user or, like, iMessages post-quantum encryption, PQ3.

359
00:43:12 --> 00:43:34
Like, we think about how we want to be able to talk about this, what promise we want to be able to make, what guarantee is a fantastic example of this you know like um we uh we know what sort of protections we want to provide to the user but it's done in a sort of beautiful way where like you don't have to concern yourself too much about what's going on underneath but you build the trust

360
00:43:34 --> 00:43:55
with a user you know by by making them understand that like okay this feature is something really that like over time you can realize it's not just going to work for anybody's face it works for your face and that's kind of how you build the trust is that organic methodology of doing the right thing again and again and again you should is you kind of want to avoid a situation where you end up

361
00:43:55 --> 00:44:19
like going in the opposite direction and reducing trust particularly around like security like demonstrate that you care about security through the security features people expect being present when they expect them to be so like when they go to create an account have again pass keys or sign in with apple yes um make sure that like you are only requesting the user data that you need

362
00:44:19 --> 00:44:41
like use the various uh like apis we provide to avoid needing to collect more data like for example if you sell like a bluetooth accessory rather than giving your app access to the entire set of bluetooth apis we have an incredible api called accessory setup kit which actually gives really good user experience for that setup process and so like give the user

363
00:44:41 --> 00:45:03
a better user experience rather than like something more complex where they give up more at the same process and also never never try and claim that you use something like bank grade encryption or military grade encryption like most users don't know what that means and like if you're doing things by default you will generally do the same or better and you know taking a page out of our

364
00:45:03 --> 00:45:32
our own book here, if you really want to demonstrate that you're doing everything right, transparency is key. We walk the walk with this one, and PCC, we make the full PCC transparent by sharing source code for part of it. Not saying you have to open source your application, but showing the world that, hey, I'm doing the things I'm claiming goes a long way as well.

365
00:45:32 --> 00:45:39
Right. And, you know, a lot of that quality and doing the right thing that you're trying, that you've conveyed is spot on.

366
00:45:39 --> 00:45:41
But Apple's already doing it for everybody.

367
00:45:41 --> 00:45:45
So the more you can be consistent and like the Apple experience, the more you can lean into it.

368
00:45:46 --> 00:45:52
There's a giant company conveying all of this trust to developers, to customers.

369
00:45:52 --> 00:46:03
So if you're like the Apple experience, if you're using the Apple technologies, if you're fully integrated, That's going to convey that sense of trust that some other giant marketing budget is doing for you.

370
00:46:03 --> 00:46:05
So I recommend you lean into that.

371
00:46:05 --> 00:46:11
And you also leaned into, like, you know, what to pay attention to and how to pay attention to it.

372
00:46:11 --> 00:46:15
But what's really important sometimes is when you pay attention to it.

373
00:46:15 --> 00:46:19
Don't ask for all of the accesses up front when someone launches the app.

374
00:46:19 --> 00:46:20
That's not the right time.

375
00:46:20 --> 00:46:22
Do it when the moment is arriving.

376
00:46:22 --> 00:46:31
Like when you want a picture is when you ask for some access to photos, whether out of process or whichever level of access you want.

377
00:46:31 --> 00:46:35
When you do, it's really important because you can provide a value exchange.

378
00:46:35 --> 00:46:41
Like someone understands what they're doing then, and you're a lot more likely to convey that without doing that.

379
00:46:41 --> 00:46:45
Well, and I think you're heading towards the advice of have a great purpose string.

380
00:46:45 --> 00:47:02
So when you're asking the user for access to something like their photos, contacts, or a particular one, explaining why you need it and what you're going to do with it, people are more likely to say yes and then be able to experience the great features within your app.

381
00:47:02 --> 00:47:10
Because I think if you don't explain, they can just say no, and they might miss out on maybe one of your coolest experiences that you've worked really hard to provide.

382
00:47:10 --> 00:47:12
And then they've got to go to settings and find your app.

383
00:47:13 --> 00:47:14
Maybe they should give up at this point.

384
00:47:15 --> 00:47:16
People do mind-managing lists.

385
00:47:17 --> 00:47:19
I know I've given up on apps in the past.

386
00:47:19 --> 00:47:22
If they ask for a camera or something like that, it's like, why are you asking for this?

387
00:47:22 --> 00:47:23
You didn't tell me.

388
00:47:23 --> 00:47:37
One of the things that's an immediate no for me is when an app requests permission for my microphone or camera, but then we have sensor indicator lights on an iPhone, like we've got this light, and one of them turns on without me expecting for it to turn on.

389
00:47:37 --> 00:47:39
That is a big no-no for me.

390
00:47:40 --> 00:47:48
If a user has granted you access to the sensors on their device, make sure that those sensors are only used when the user is expecting for them to be used.

391
00:47:49 --> 00:47:54
Okay. One of our viewers is providing a recruitment moment for us.

392
00:47:54 --> 00:47:59
Username ThumbDrive. I kind of like that as a security username, by the way.

393
00:48:00 --> 00:48:06
I am a CS, I guess that's computer science student maybe, studying cybersecurity.

394
00:48:06 --> 00:48:27
What Apple frameworks or security concepts would you recommend learning first in order to build a foundation that is relevant to both app and platform security and the broader field? This is a question a lot of us who work in security get. It's great to have students who are really interested in cybersecurity, like we are often also hiring for cybersecurity and privacy

395
00:48:27 --> 00:48:39
roles, so do always keep an eye out. We have a range of really good sources, so there's the, like I think for a student, the platform security guide is a really, really good starting point.

396
00:48:39 --> 00:48:48
It goes into a bit more depth than just our like standard documentation or like information about the OSs that we produce on like Apple.com.

397
00:48:48 --> 00:48:56
It goes really deep into some of the security technologies like data protection, the secure enclave, how that all works, how that all ties together.

398
00:48:57 --> 00:49:09
But we also on the security side, we have a blog called security.apple.com, which is also where you can find details about our bug bounty program and how to report vulnerabilities to Apple.

399
00:49:09 --> 00:49:33
the security blog has had some really great blog posts we had the one earlier this week about extending um private cloud compute onto uh google and nvidia's hardware shout out emily yeah and uh we've had really good blogs in the past again around memory integrity enforcement around iMessage around changes to how we do memory allocation in the kernel of the operating

400
00:49:33 --> 00:49:47
system there's like a real range all the way from things that are like very accessible and kind of topics that most people who are interested in security would find interesting, down to like the really in-depth details about how our operating system works.

401
00:49:47 --> 00:49:57
Yeah, and I would add, you know, just on a very sort of generic note there, like if you're more interested in low-level security, you want to learn about things like binary exploitation.

402
00:49:57 --> 00:49:59
You want to learn about reverse engineering.

403
00:49:59 --> 00:50:05
Really, like when you're talking about a platform, it really comes down to how well can you understand how something works?

404
00:50:06 --> 00:50:10
And that is how you learn whether how to break it or how to defend it, depending on which path you want to go into.

405
00:50:11 --> 00:50:16
For networking, you know, focus on protocols, learn how TLS works, learn how the Internet works.

406
00:50:17 --> 00:50:24
Security and to a large degree privacy comes down to you need to have a very clear understanding of how something works.

407
00:50:24 --> 00:50:28
And based on that is how you choose to whether attack it or defend it.

408
00:50:28 --> 00:50:29
Nice.

409
00:50:31 --> 00:50:33
Not super upvoted, but I love pass keys.

410
00:50:33 --> 00:50:42
So, Alexer asks, pass keys reduce a lot of password risk, but they also introduced credential lifecycle problems.

411
00:50:43 --> 00:50:44
I don't know if I really think they're problems.

412
00:50:45 --> 00:50:52
How is Apple approaching stale, expired, revoked, or no longer valid passkeys in the passwords app?

413
00:50:52 --> 00:50:56
And are there plans to surface that cleanup safely to users?

414
00:50:56 --> 00:50:59
Anyone want to tackle this or do you want me to go?

415
00:51:00 --> 00:51:01
I think David does.

416
00:51:03 --> 00:51:05
Well, this one has been solved.

417
00:51:06 --> 00:51:15
There's a new set of APIs called the Signal API, And those will signal your app or website that things have changed and you need to do updates.

418
00:51:15 --> 00:51:16
So this has been considered.

419
00:51:17 --> 00:51:21
And, you know, PassKeys is part of a wide standard.

420
00:51:21 --> 00:51:26
And if you watch what the standard is opting towards, Apple is part of that standard's governing body.

421
00:51:27 --> 00:51:32
And we're all working together across companies because PassKeys don't work unless you can sync all over the place.

422
00:51:32 --> 00:51:35
So we're working across companies to make this work great.

423
00:51:36 --> 00:51:41
Answered my own question, so I have to sit down here for a bit and find another one.

424
00:51:42 --> 00:51:51
D. Varevkin asks, I'm researching best practices for protecting sensitive data in memory.

425
00:51:52 --> 00:51:53
We haven't covered in memory yet today.

426
00:51:53 --> 00:52:04
On macOS, so passwords, tokens, cryptographic keys, does Apple recommend encrypting secrets while resident in RAM, or are platform protections generally considered sufficient?

427
00:52:06 --> 00:52:08
I think this one might be for me.

428
00:52:08 --> 00:52:08
Yes.

429
00:52:09 --> 00:52:20
For macOS, we specifically provide functionality called hardened runtime, and we highly recommend everybody who is privacy and security conscious to enable this functionality.

430
00:52:20 --> 00:52:26
With hardened runtime, your application memory cannot be read by anybody else.

431
00:52:26 --> 00:52:33
Somebody can't try and map malicious or unsigned or unverified code into your process's memory.

432
00:52:34 --> 00:52:39
So, you know, we have a very, very secure operating system on macOS systems.

433
00:52:39 --> 00:52:45
And so there's many paths that you would say that an attacker would take when trying to read a process's memory.

434
00:52:45 --> 00:52:51
But the most common ones tend to be you spin up a thread in a process and then you attempt to read information from that.

435
00:52:51 --> 00:52:54
Or you attach a debugger and you try and read information from that.

436
00:52:55 --> 00:52:57
With hardened runtime, those things become not possible.

437
00:52:58 --> 00:53:03
And the protection is guaranteed deep within the operating system for those things.

438
00:53:03 --> 00:53:10
So that is the first thing you should do is if you're even asking the question of how do I protect my in-process memory?

439
00:53:10 --> 00:53:11
Well, you start with hard and run time.

440
00:53:12 --> 00:53:22
When it comes to things like encrypting in process memory, you should really ask yourselves is encrypting the right question versus destruction?

441
00:53:23 --> 00:53:29
You know, like if you have something that needs to stay in memory, but for a very short period of time, encryption is not the answer.

442
00:53:29 --> 00:53:32
But the answer is, you know, you have a token for something.

443
00:53:32 --> 00:53:36
And once you've used the token, you should destroy the token because you don't need it anymore, right?

444
00:53:36 --> 00:54:00
When we consider like extended periods of time where we want to encrypt something in memory we're really talking about like sort of DRM level secrets and then in those worlds we're not really in the realm of pure security anymore so that would be the general advice is hardened runtime token destruction or memory destruction and and those like should be the hard focuses

445
00:54:00 --> 00:54:25
I'll add to that a plug for crypto kit when it comes to keys and other things you're handling in memory, even if it's ephemeral keys. Sticking with the APIs like CryptoKit helps ensure the lifetime of those variables. We've gone to great lengths to make sure that we zeroize the data that backs those keys when you use CryptoKit. If you try to go outside of that and do your own

446
00:54:25 --> 00:54:38
thing, we can't guarantee that the Swift runtime zeroizes the memory out when you say it's going to be zeroized. So definitely sticking with built-in frameworks. Don't do something that That's what the platform does for you.

447
00:54:38 --> 00:54:39
Exactly, yeah.

448
00:54:39 --> 00:54:56
There's also something really cool that you can do with keys, which is we have our secure enclave, and one of the really cool things you can do with the secure enclave through our APIs is you can actually say, like, hey, I want this key that I'm generating on device to be bound to the secure enclave on the device.

449
00:54:56 --> 00:55:06
That means that if, say, an attacker compromises your app or your process or even the device, then, like, that key won't be exportable off the device.

450
00:55:06 --> 00:55:11
It relies on the Secure Enclave hardware of that particular device to be usable.

451
00:55:12 --> 00:55:17
And so you can ensure that if the worst happens, like that key can't be exported off the device and used elsewhere.

452
00:55:18 --> 00:55:27
And you can use an attestation interface as well to attest that that key is held by the Secure Enclave on the device.

453
00:55:28 --> 00:55:28
All right.

454
00:55:29 --> 00:55:39
I think we're getting nearer to the end, so I'm going to ask one that can prompt like a bigger thing, Not to show favoritism, but maybe a privacy one to end.

455
00:55:39 --> 00:55:41
We've had a lot of security ones.

456
00:55:41 --> 00:55:52
So from KTH Chu, which approach do you take when balancing privacy and collecting telemetry that's useful for development?

457
00:55:52 --> 00:55:54
I think it's the basic thing.

458
00:55:54 --> 00:55:57
Like, we do need to track something, but how do we do it?

459
00:55:57 --> 00:55:57
What's the best balance?

460
00:55:59 --> 00:56:00
Katie, do you want to answer that one?

461
00:56:01 --> 00:56:01
Sure, I can start.

462
00:56:01 --> 00:56:14
I think one of the things that actually Craig said on stage a few years ago, which is kind of like a little bit of how we approach things in our guiding light, is the idea of great features and great privacy.

463
00:56:15 --> 00:56:18
And we talk about that's what we're looking for is both, not one.

464
00:56:19 --> 00:56:31
And so sometimes to get both, you have to be a little bit creative, sometimes do something that might take a little bit more, you know, creativity, work, et cetera.

465
00:56:31 --> 00:56:38
but we really can have both. And so we really use our privacy pillars as kind of a way to get there.

466
00:56:38 --> 00:57:01
So we think first about data minimization, which I think is really appropriate for this question, is thinking about do you need all the data to answer whatever your question is, which we should be grounded in that question. And so do you need full grain timestamps? Does it matter it happened at, you know, 1222 and how many nanoseconds, or can you find out that maybe it's okay that it

467
00:57:01 --> 00:57:23
happened in the morning or that day? Are you trying to understand if the user did something over a week, maybe you can aggregate, that's a great place to start. And that's really utilizing one of our other pillars, on-device minimization. And I mean, on-device processing, there we go, smush two together. So on-device processing is really taking advantage of what can be calculated.

468
00:57:23 --> 00:57:31
So a lot of those aggregation, you know, and data minimization techniques can happen on device before you send it off to the server.

469
00:57:31 --> 00:57:38
I think then you maybe want to talk about control and transparency and then shout out our security friends.

470
00:57:38 --> 00:57:48
Yeah, I think first also just to like lean in on what you were saying, you know, aggregation is super important because not all data formats are the same.

471
00:57:48 --> 00:58:02
Even if you're fundamentally setting up the same type of data and you have not aggregated it, you might not realize that this time series that you're actually collecting has inherent to it some behavioral patterns about the user, for example, that you're not actually intending to collect.

472
00:58:02 --> 00:58:14
And so just really thinking hard about what ultimately is that question that you're trying to answer and getting the most distilled version of it can help protect users' privacy.

473
00:58:14 --> 00:58:18
But it also could just save on, let's say, the networking bandwidth that your app is incurring.

474
00:58:19 --> 00:58:22
You don't have to send it, you know, in real time.

475
00:58:22 --> 00:58:26
You don't have to send all that extra data that goes along with it.

476
00:58:26 --> 00:58:32
I mean, like, one of my favorite examples of this is if you were collecting telemetry about setting alarms and do they go off.

477
00:58:32 --> 00:58:38
Like, if you collect it with a fine-grained timestamp, you might be, like, collecting what time people wake up in the morning.

478
00:58:38 --> 00:58:40
And that's not actually, like, maybe what you were trying to look for.

479
00:58:41 --> 00:58:41
Right.

480
00:58:42 --> 00:58:53
And then, yeah, so transparency and control, which is the next direction you're going, is just making sure users have control over whether, you know, you're collecting their data or not.

481
00:58:53 --> 00:59:03
You know, fundamentally it is their data, and I think I said this earlier, but it's best to make sure that users have control over their data ultimately and show them what it is that you're actually collecting.

482
00:59:03 --> 00:59:11
You don't want to hide all this data that is, you know, being sent to your server, give them a little bit of an insight into what actually is being collected.

483
00:59:11 --> 00:59:19
And I think the last thing is when you're collecting data, make sure you're taking the security and the protections of that data seriously.

484
00:59:19 --> 00:59:22
So I think security, shout out in two ways here.

485
00:59:22 --> 00:59:28
One, if a user has control and they say no, making sure that's technically enforced and you're not still collecting the data.

486
00:59:28 --> 00:59:40
And secondly, if you're collecting the data, how are you making sure that that's not something that attackers can get access to, et cetera, because that's a big, big difference.

487
00:59:40 --> 00:59:44
And even I think we had a couple of conversations about thinking about when do we delete the data.

488
00:59:44 --> 00:59:48
And that's a great, you know, security technology as well.

489
00:59:49 --> 00:59:49
Yeah.

490
00:59:49 --> 00:59:53
Privacy and security fundamentally have to work hand in hand to protect users.

491
00:59:54 --> 00:59:54
Yeah.

492
00:59:54 --> 00:59:59
I think that, like, we all work so closely together on these kinds of things.

493
00:59:59 --> 01:00:08
And it really is, like, you need the technologies to be able to create these guarantees, promises that all of us and all of you want to.

494
01:00:09 --> 01:00:10
What a great way to end.

495
01:00:11 --> 01:00:13
Privacy and security work together.

496
01:00:13 --> 01:00:18
I did try to type to our scheduler and try to get extended to a six-hour window.

497
01:00:19 --> 01:00:19
No.

498
01:00:19 --> 01:00:20
But they said no.

499
01:00:21 --> 01:00:23
Well, that's all the time we have for today.

500
01:00:23 --> 01:00:24
We're really thankful.

501
01:00:24 --> 01:00:25
Those are great questions.

502
01:00:25 --> 01:00:27
You've let us go all over the place, explore everything.

503
01:00:28 --> 01:00:31
If your question didn't get answered, make sure you ask it on the forums.

504
01:00:32 --> 01:00:32
We all visit the forums.

505
01:00:32 --> 01:00:35
We have experts that answer questions on the forums.

506
01:00:36 --> 01:00:43
And when you ask questions on the forums, we all uplift each other because your answers, other people want to know the same thing.

507
01:00:43 --> 01:00:46
And that's how you go find answers that other people have asked for as well.

508
01:00:46 --> 01:00:47
So that's great.

509
01:00:48 --> 01:00:54
If you have bug reports or feature requests, go to feedbackassistant.apple.com and let us know about them.

510
01:00:54 --> 01:01:04
The more you tell us, the more examples you have, sample code, screenshots, cyst diagnosis, the more that's in there, just like when you work on bugs, the more we can do about it.

511
01:01:04 --> 01:01:08
So make some great bugs for us, bug reports for us.

512
01:01:09 --> 01:01:12
And speaking of feedback, there's going to be a survey afterwards for everybody.

513
01:01:13 --> 01:01:17
Fill in the survey about your WWDC experience, about this group lab.

514
01:01:17 --> 01:01:18
We're really open to feedback.

515
01:01:18 --> 01:01:24
We want to make a better and better WWDC experience for you, for every one of them in the future.

516
01:01:25 --> 01:01:26
And finally, thank you, panelists.

517
01:01:27 --> 01:01:31
I really appreciate just how much passion you have for all of this.

518
01:01:31 --> 01:01:36
It's really evident, and I'm proud to work with you all on privacy security at Apple.

519
01:01:36 --> 01:01:37
And thank you, David.

520
01:01:37 --> 01:01:37
Thank you.

521
01:01:38 --> 01:01:39
Thank you, everybody, for tuning in.

522
01:01:40 --> 01:01:40
Bye.
