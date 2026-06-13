---
title: SwiftUI for Beginners Group Lab
source: https://developer.apple.com/videos/play/wwdc2026/8002/
session: 8002
collection: wwdc2026
duration: 01:01:00
fetched: 2026-06-12
via: whisper.cpp ggml-large-v3 (session SD video audio)
transcription: machine-generated, no speaker diarization
---

> Machine transcription of the **SwiftUI for Beginners Group Lab** SD video via whisper.cpp (`ggml-large-v3`).
> Group labs ship no Apple transcript; this is generated from the audio. Cues are timestamped segments, **not** speaker-attributed turns.

## Transcript

1
00:00:04 --> 00:00:07
Hello, and welcome to the SwiftUI for Beginners Group Lab.

2
00:00:07 --> 00:00:12
My name's Kurt, and I'm part of the Worldwide Developer Relations Team here at Apple.

3
00:00:12 --> 00:00:21
I'm joined by engineers and leaders from the UI Frameworks team, Gabriel, Jeff, Sam, Sima, and Trevor.

4
00:00:22 --> 00:00:28
In addition to us on screen, there's a team behind the scenes helping with the triage of all your inbound questions.

5
00:00:29 --> 00:00:33
We're excited to answer your questions today about getting started with SwiftUI.

6
00:00:33 --> 00:00:43
If you have code-specific questions or we can't get to your question today, We'll continue the conversation on the developer forums at developer.apple.com slash forums.

7
00:00:44 --> 00:00:49
If you have a bug or feature request, please go to feedbackassistant.apple.com.

8
00:00:49 --> 00:00:53
We'll focus on questions today that will help the broadest audience.

9
00:00:53 --> 00:01:02
Whether you're new to programming, new to Apple's platforms, or just new to SwiftUI, we're looking forward to answering your questions.

10
00:01:02 --> 00:01:07
We'll start with a quick overview of SwiftUI, then dig into Q&A.

11
00:01:07 --> 00:01:08
Seema, get us started.

12
00:01:11 --> 00:01:39
SwiftUI is Apple's declarative user interface framework used to build apps across all of Apple's platforms. When you are building a new app or a new feature, SwiftUI is the right tool to use. There are a few reasons for this. SwiftUI comes with a wide range of features. These enable your apps to take advantage of the devices they run on, feel native on Apple's platforms, and add

13
00:01:39 --> 00:01:53
rich interactivity. And adding those features requires less code, enabling you to move from prototype to production faster, and empowering you to focus on what makes your app unique.

14
00:01:54 --> 00:02:07
SwiftUI embraces incremental adoption, so you can use it exactly where you need it. There is no expectation that an entire app needs to be SwiftUI to be able to take advantage of it.

15
00:02:07 --> 00:02:16
These qualities make it easy for anyone to learn how to build an app using SwiftUI.

16
00:02:16 --> 00:02:24
Views are the basic building blocks of user interfaces and are important to everything you do in SwiftUI.

17
00:02:24 --> 00:02:30
Every pixel you see on screen is in some way defined by a view.

18
00:02:30 --> 00:02:32
Views are expressed declaratively.

19
00:02:32 --> 00:02:38
You describe what view you want in your user interface and SwiftUI produces the result.

20
00:02:38 --> 00:02:49
This code creates a horizontal stack that consists of a label, which is a combination of an icon and a title, a spacer, and a text at the end.

21
00:02:49 --> 00:02:55
This same declarative syntax applies to other containers, such as scrollable lists.

22
00:02:55 --> 00:03:02
This list is given a collection of pets, and creates a series of rows using the properties of each pet.

23
00:03:02 --> 00:03:09
At no point did we need to describe the actions necessary to produce this interface, such as adding or removing from the list.

24
00:03:09 --> 00:03:15
This is because SwiftUI views are descriptions of what the current state of the UI should be.

25
00:03:15 --> 00:03:21
They are not long-lived object instances that receive imperative commands over time.

26
00:03:21 --> 00:03:28
As such, SwiftUI views are value types, defined using structures instead of classes.

27
00:03:28 --> 00:03:34
Composition is used throughout SwiftUI, and is an essential part of every user interface.

28
00:03:34 --> 00:03:38
Rearranging and experimenting with container views is effortless.

29
00:03:38 --> 00:03:43
The code itself resembles the hierarchy of views that it creates.

30
00:03:43 --> 00:03:49
This horizontal stack contains three views: an image, vertical stack, and spacer.

31
00:03:49 --> 00:03:55
And the vertical stack contains two views of its own: the label and text.

32
00:03:55 --> 00:04:04
View hierarchies can be encapsulated into custom views and view modifiers, enabling the same compositional features and quick iteration.

33
00:04:04 --> 00:04:08
These custom views can have inputs that change how their body is constructed.

34
00:04:08 --> 00:04:17
This view has a property for the pet that this row represents, which can be used to change the contents of the contained views.

35
00:04:17 --> 00:04:24
This lets the same view display information about whiskers, as well as rufus and bubbles.

36
00:04:24 --> 00:04:28
And custom views can be used like any other view.

37
00:04:28 --> 00:04:33
Here they're used in a list to create fresh rows for each pet.

38
00:04:33 --> 00:04:39
But apps aren't just static screens, they're dynamic, changing with time and interaction.

39
00:04:39 --> 00:04:44
Of course, SwiftUI has you covered there too.

40
00:04:44 --> 00:04:47
Views in SwiftUI are state-driven.

41
00:04:47 --> 00:04:52
You declare what state they depend on, and SwiftUI does the work to keep your views in sync with that data.

42
00:04:53 --> 00:05:07
When your view's state changes over time, SwiftUI automatically keeps the UI that depends on it up to date, while avoiding reevaluating things that won't change, eliminating both boilerplate and update bugs.

43
00:05:07 --> 00:05:11
SwiftUI has several tools for state management.

44
00:05:11 --> 00:05:14
The simplest one is @State.

45
00:05:14 --> 00:05:18
State creates a new internal source of data for a view.

46
00:05:18 --> 00:05:27
When you mark a view property as @State, SwiftUI manages its storage and provides it back for the view to read and write.

47
00:05:27 --> 00:05:31
This view, for example, allows people to rate a pet's tricks.

48
00:05:31 --> 00:05:37
It's using state to keep track of the current rating, allowing it to change over time.

49
00:05:37 --> 00:05:46
The value is displayed prominently in the middle, and it has two buttons to increment and decrement the value.

50
00:05:46 --> 00:05:51
SwiftUI maintains the value of this state behind the scenes.

51
00:05:51 --> 00:05:58
When the button is tapped, its action is called, incrementing the internal state of the view.

52
00:05:58 --> 00:06:06
SwiftUI notices this change, and calls body on rating view, which returns a new text value.

53
00:06:06 --> 00:06:09
The result is then updated on screen.

54
00:06:09 --> 00:06:16
To add animation to this state change, just wrap with animation around the text.

55
00:06:16 --> 00:06:21
Here SwiftUI applied a default crossfade animation to the text.

56
00:06:21 --> 00:06:25
But you can also customize the transition.

57
00:06:25 --> 00:06:30
In this case, using a numeric text content transition fits perfectly.

58
00:06:30 --> 00:06:36
SwiftUI automatically provides adaptivity along several dimensions.

59
00:06:36 --> 00:06:44
The app already looks great in dark mode and supports several accessibility features, such as dynamic type.

60
00:06:44 --> 00:06:49
Many components across SwiftUI have this same adaptive quality.

61
00:06:49 --> 00:06:54
One example is searchable, which is applied to the list of pets here.

62
00:06:54 --> 00:07:00
SwiftUI takes care of all the details to make that happen in an idiomatic way.

63
00:07:00 --> 00:07:11
And through incremental adoption of other modifiers, you can customize the experience, such as adding suggestions, scopes, and tokens.

64
00:07:11 --> 00:07:17
SwiftUI's declarative and adaptive views pack in a lot of functionality.

65
00:07:17 --> 00:07:37
In just a few lines of code, there are controls like button, toggle, and picker, container views like navigation stack, tab view, and list, presentations such as sheets and popovers, and many more examples that you can explore in the documentation.

66
00:07:37 --> 00:07:48
And when you're ready to create unique custom experiences, SwiftUI also has another layer of API that provides low-level control.

67
00:07:48 --> 00:08:03
You can build your own control styles, use Canvas for high-performance imperative drawing, create completely custom layouts, and even apply custom metal shaders directly to SwiftUI views.

68
00:08:03 --> 00:08:09
SwiftUI is available when building an app for any Apple platform.

69
00:08:09 --> 00:08:12
Thanks for that great introduction.

70
00:08:12 --> 00:08:14
Are you excited to start building?

71
00:08:14 --> 00:08:17
We can't wait to help you get started.

72
00:08:17 --> 00:08:22
And so now, we'll go to your questions.

73
00:08:22 --> 00:08:33
Our first question comes from Donald, and I think I'll start this one off with you, Jeff.

74
00:08:33 --> 00:08:36
So the question is, as a beginner, I'm confused.

75
00:08:36 --> 00:08:39
Should I use React Native or SwiftUI?

76
00:08:39 --> 00:08:44
Both are incredible, but I think React Native allows me to release on both platforms.

77
00:08:44 --> 00:08:47
Can you introduce some of the advantages of what SwiftUI can do?

78
00:08:48 --> 00:08:48
Sure.

79
00:08:48 --> 00:08:57
So I would say that one great advantage of SwiftUI is that it's intimately tied to all of Apple's platforms.

80
00:08:58 --> 00:09:16
And so when we introduce new design language, for example, Liquid Glass last year, you get a lot of that for free just by using our native UI framework in your apps versus something like React Native where you might not be getting those same platform advantages.

81
00:09:17 --> 00:09:27
We also evolve the behaviors of our APIs over time to take advantage of new features in our devices and our operating systems as well.

82
00:09:28 --> 00:09:33
Yeah, one of the things I get to do in my role as a technical evangelist is go out and meet with lots of developers.

83
00:09:34 --> 00:09:44
And one of the things we saw in our workshops about the new design was, so the new design released at the same time as some design changes on other platforms.

84
00:09:44 --> 00:09:46
And we sort of all went in different directions.

85
00:09:47 --> 00:09:54
And the developers that had chosen a cross-platform framework were finding that they were landing in an awkward middle spot.

86
00:09:55 --> 00:09:57
They weren't adapting to the platforms as they were.

87
00:09:58 --> 00:10:09
And so those folks were moving away from multi-platform frameworks and towards native frameworks on all the platforms that they deployed on.

88
00:10:09 --> 00:10:19
Because they were finding that the seeming efficiencies at the beginning actually meant a lot more work at the end and more work at a time in the production cycle where it was hard to adapt.

89
00:10:21 --> 00:10:33
And so I think especially when we have the agentic coding that can let us move so fast at the beginning stages of a project, making the final polish stages easier is sort of a thing to emphasize.

90
00:10:33 --> 00:10:50
There's also, like, the aspect to it that, you know, SwiftUI is built on Swift, which is, like, a really incredible programming language, and so just by using SwiftUI, you're able to leverage, like, the power of this incredibly performant, expressive language, which is something you don't get with some other non-native frameworks.

91
00:10:50 --> 00:11:03
All right, the next question, and I'm going to try to read off this person's username, AI Did Lutfi.

92
00:11:05 --> 00:11:12
As an accounting student with zero knowledge in coding, and Sima, I'll let you take a first crack at this.

93
00:11:13 --> 00:11:17
What's the best approach to learn coding and building an app with SwiftUI?

94
00:11:17 --> 00:11:19
That's a great question.

95
00:11:19 --> 00:11:41
so the way I approach this and like I would recommend to approaching this is uh kind of just think about your life but like what is like one of the problems that you encounter every day or like think about your family like what are like what would be an app that would help them and that would motivate you to come up with an idea that would be helpful for you your friends and family

96
00:11:41 --> 00:11:52
and then you get to share it around and see them use your app and actually improve their life with That's how I, like, also, like, years ago got into, like, iOS development.

97
00:11:53 --> 00:12:05
And then I think nowadays with agent-encoding and Xcode, you get to build it faster and, you know, see it come live on screen in, like, minutes.

98
00:12:05 --> 00:12:06
And it's great.

99
00:12:06 --> 00:12:16
And actually this year we have enhanced our agent-encoding capabilities in Xcode with some of the skills that, you know, can help you build even better experiences.

100
00:12:17 --> 00:12:19
So, yeah, go ahead and give it a try.

101
00:12:19 --> 00:12:21
Hopefully it works out great.

102
00:12:21 --> 00:12:27
I love that idea of finding some little problem or thing you want to build.

103
00:12:27 --> 00:12:29
Find what that spark is.

104
00:12:29 --> 00:12:35
I think it might be kind of fun to talk about what some of our first apps were when we got into this.

105
00:12:35 --> 00:12:38
Sima, are you willing to share what one of your first apps was?

106
00:12:38 --> 00:12:40
Yeah, I have two.

107
00:12:40 --> 00:13:08
The first app I built was like a Pomodoro timer because I, back then I was like very into kind of organizing the way I work and I liked some of those productivity tools and I wanted to build a custom experience for myself and I really enjoyed customizing the app for me and kind of seeing myself like take advantage of like using it and like actually make my work

108
00:13:08 --> 00:13:09
more productive.

109
00:13:09 --> 00:13:11
So that was my first app.

110
00:13:11 --> 00:13:11
Nice.

111
00:13:11 --> 00:13:13
Anybody else want to share first apps?

112
00:13:13 --> 00:13:14
I got a good one.

113
00:13:14 --> 00:13:20
When I was a little kid, our dogs, for my family growing up, used to, like, jump on our bed all the time.

114
00:13:20 --> 00:13:23
And I had, like, just started learning about, like, iOS development.

115
00:13:23 --> 00:13:28
And the only way we could find to consistently get them to get off the bed was if they heard, like, our doorbell sound.

116
00:13:29 --> 00:13:31
So I recorded our doorbell sound.

117
00:13:31 --> 00:13:37
I made an app that we called Dog Bell where you could just, like, press the button and it would make the dogs jump off the bed.

118
00:13:38 --> 00:13:39
Very useful.

119
00:13:39 --> 00:13:40
Anybody else have one?

120
00:13:41 --> 00:13:50
I think one of my first ones, but this was probably with AppKit because this was a while ago, was a to-do list app because I was always forgetting to do things.

121
00:13:50 --> 00:13:57
And I probably also didn't spend enough time on it because I'm sure I forgot to finish it.

122
00:13:59 --> 00:14:00
Nice. Excellent.

123
00:14:00 --> 00:14:03
I learned iOS development at a coding boot camp, and they had a ping pong table there.

124
00:14:04 --> 00:14:07
And my friend and I, we were just learning.

125
00:14:07 --> 00:14:09
We would play ping pong all the time when we would take breaks.

126
00:14:10 --> 00:14:12
We always argue over the records and who is better.

127
00:14:13 --> 00:14:17
And so we, at one point, just built a little app to keep track of who won more games historically.

128
00:14:18 --> 00:14:18
Who was better?

129
00:14:19 --> 00:14:19
He was much better.

130
00:14:20 --> 00:14:22
It turns out I knew that all along.

131
00:14:22 --> 00:14:24
Yeah, you should be keeping track, apparently.

132
00:14:25 --> 00:14:29
I have made an app specifically for colorblindness.

133
00:14:29 --> 00:14:49
So as somebody who has red-green colorblindness, I found that it was really enjoyable to actually go through the process of making an app and kind of adjusting it for my own needs, for day-to-day figuring out, you know, differentiating things and kind of making an app that felt natural to the way that I work, I come about my life.

134
00:14:49 --> 00:14:51
Nice. I love it. Excellent.

135
00:14:51 --> 00:14:53
So thanks for sharing.

136
00:14:53 --> 00:14:56
Our next question comes from Sehan.

137
00:14:57 --> 00:14:59
And maybe I'll start with you on this one, Trevor.

138
00:15:00 --> 00:15:04
Sehan writes, I'm a CS student just starting iOS development.

139
00:15:04 --> 00:15:26
If your goal were to become a highly skilled, job-ready iOS engineer in 2026, what roadmap would you follow from Swift fundamentals to advanced app development? And which skills would you prioritize most? There's a lot there. I think we're all going to be able to jump in. Yeah. I would start with Swift, right? Like that's the basic building

140
00:15:26 --> 00:15:42
block, right? Make sure you have a good understanding of the basic language that you're going to be working with on iOS. Like Swift can be really different if you're coming from another language, the type system, concurrency, right?

141
00:15:42 --> 00:16:02
There's lots of features that are going to be kind of new and really interesting, and honestly, I think really great to learn from the start versus maybe learning unsafe habits in another language where Swift will kind of enforce those things for you and allow you to learn them along the way as you're learning, which is great.

142
00:16:03 --> 00:16:12
And then I think the fun part becomes diving into SwiftUI, building a basic app, something that you're going to have fun with, one of those things.

143
00:16:13 --> 00:16:16
There's some really great resources online for SwiftUI.

144
00:16:16 --> 00:16:22
The online interactive tutorials, I think, were the most incredible introduction to SwiftUI.

145
00:16:22 --> 00:16:26
When I saw animations in there, something really clicked for me.

146
00:16:27 --> 00:16:30
And that would be, I think, the first couple months.

147
00:16:31 --> 00:16:34
There's a ton within just those two things, but that's where I would start.

148
00:16:36 --> 00:16:39
Anybody have anything to add to that?

149
00:16:39 --> 00:16:54
I think one of the things that I think is going to be valuable is, like, use the agentic tools, make those part of your learning, but make sure you're thinking about the output they're producing, right?

150
00:16:54 --> 00:17:06
The goal – so I was a teacher in a prior career, and when a teacher assigns an assignment, it's not because they want the answer.

151
00:17:06 --> 00:17:07
They know the answer.

152
00:17:08 --> 00:17:14
What the teacher is looking for is they're trying to give you practice on an area, right, to up-level your skills.

153
00:17:15 --> 00:17:17
And you can do the same thing with your own work.

154
00:17:17 --> 00:17:23
If you're using – if you have an app you want to build and you're using eugenic programming to help you get started, look at the output.

155
00:17:23 --> 00:17:28
understand what it's doing, you know, engage with it deeply, not just at the surface.

156
00:17:28 --> 00:17:29
Anybody want to?

157
00:17:29 --> 00:17:32
Also, I was just going to say a quick add-on.

158
00:17:32 --> 00:17:38
When it comes to, like, using LLMs for programming generally, or for learning specifically, treat them like tutors.

159
00:17:38 --> 00:17:43
Like, you can just, you know, instead of just having them generate code and, like, looking at it and studying it, you can ask them questions about it, right?

160
00:17:43 --> 00:17:50
Like, it is really like having an interactive thing with you that has a really deep domain expertise on a lot of stuff.

161
00:17:50 --> 00:18:07
Yeah, that's totally what I was going to get at, too, is, like, I feel like these tools are really great at explaining concepts, and so this can be a really valuable tool to learn some of the underlying kind of CS concepts as well.

162
00:18:08 --> 00:18:12
Especially when you're building an app, you need to know a lot more parts than just the UI, right?

163
00:18:12 --> 00:18:23
Like there's networking calls and data storage and all that, that it can probably do a great job explaining some of the underlying algorithms that you might want to use for some of those pieces as well.

164
00:18:23 --> 00:18:27
That gets another kind of interesting thing you can do with these.

165
00:18:27 --> 00:18:34
Like instead of asking the agent to write some code for you, you can take a stab at writing the code and ask the agent for a code review.

166
00:18:34 --> 00:18:37
It's like, you know, what could I have done better here?

167
00:18:37 --> 00:18:40
What were their APIs I should have considered or looked at?

168
00:18:40 --> 00:18:44
And so it's nice to have that tutor in your pocket.

169
00:18:45 --> 00:18:45
It's kind of cool.

170
00:18:50 --> 00:18:51
Ah, this is a great question.

171
00:18:51 --> 00:18:52
Sam, I'm going to start with you on this one.

172
00:18:52 --> 00:18:53
Okay, what you got?

173
00:18:53 --> 00:18:57
Since you covered this in our little intro segment.

174
00:18:57 --> 00:18:58
So this is from Will Picks.

175
00:18:59 --> 00:19:04
For a beginner coming to SwiftUI, what exactly happens under the hood when I use at stake?

176
00:19:05 --> 00:19:09
Why can't I just use a standard Swift VAR for data displayed in the UI?

177
00:19:09 --> 00:19:25
- Yeah, so one of the big concepts behind SwiftUI is that we want views to be very lightweight descriptions of the actual thing that's getting displayed rather than some long-lived thing that sits around and you change over time.

178
00:19:25 --> 00:19:36
But we do still need some place to store the information that is going to change over time, like if you have a counter that's incrementing or just some value that needs to change over time.

179
00:19:36 --> 00:19:40
So what state says is, hey, SwiftUI, take this value and own it.

180
00:19:40 --> 00:19:45
Make a persistent thing that you can kind of keep around, a little box for it, and it does this under the hood.

181
00:19:45 --> 00:19:51
It makes a little, it allocates a little piece of memory which just lets it sit with this value.

182
00:19:51 --> 00:19:56
And then it gives you the ability to read it and to write it whenever you have closures that change.

183
00:19:57 --> 00:20:08
And that, the combination of these tricks basically means that you don't have to think too much about where the values are getting stored or memory allocation, or any of the complexities under the hood.

184
00:20:08 --> 00:20:09
You can just make the value and change it.

185
00:20:09 --> 00:20:10
Yep, yep.

186
00:20:11 --> 00:20:34
So I think for folks who maybe have more like a CS background or are coming from apps like UIKit or another sort of traditional object-oriented framework, I mean, it's not required to have that background to use SwiftUI, but that sort of difference that a SwiftUI struck is a value that describes the view.

187
00:20:34 --> 00:20:38
And so SwiftUI is like making an instance of it.

188
00:20:38 --> 00:20:43
It's grabbing the state that you have stored away, putting it all together.

189
00:20:43 --> 00:20:46
It's rendering the pixels, and then just throws it away.

190
00:20:47 --> 00:20:51
And the pixels are on the screen, and memory is preserved for other work now.

191
00:20:51 --> 00:20:57
And so that's sort of one of the things that's really different about SwiftUI is like you've got this template for what the view should look like.

192
00:20:58 --> 00:21:02
It pulls all the pieces together, puts the pixels together, and then it's on its way.

193
00:21:03 --> 00:21:28
Yeah, I do think that for lots of people who like start out the SwiftUI, a lot of times it's confusing in terms of like, when should I use add state? Or like, when should I just like store something as a variable on the view struct? And as Sam, I think, and Kurt, you just touched on, SwiftUI will recreate your view struct many times as your parent view updates and just through like

194
00:21:28 --> 00:21:48
your app's lifecycle, and so it sometimes becomes harder for you to kind of use this, just the normal property, because it'll just get thrown away every time, and so I think state is a great tool to kind of let SwiftUI kind of store this value away and kind of keep it there, alive for you, so that every time Vue is recreated,

195
00:21:48 --> 00:21:54
the new value is available to you, and you're able to actually change the state.

196
00:21:54 --> 00:22:02
- The Swift language is really nice here, because if you try to just declare a variable inside of a view body and then change it, right?

197
00:22:02 --> 00:22:04
Like you'll get a compiler error and you won't be able to build your app.

198
00:22:04 --> 00:22:13
And so like, at least you have that nice warning to say, hey, use that state here to key SwiftUI in on unknowing when it needs to redraw views.

199
00:22:13 --> 00:22:21
- That actually loops back to our first question is like why, and part of Sam's answer to that is why choose SwiftUI?

200
00:22:21 --> 00:22:33
Because it's embedded in this type safe programming language the compiler is going to find your mistakes for you instead of having to tease them out at runtime.

201
00:22:33 --> 00:22:40
I always find it miraculous when using Swift or honestly any strongly typed language how often your code compiles and then it just works after compiling.

202
00:22:41 --> 00:22:42
It sometimes feels a little miraculous.

203
00:22:43 --> 00:22:48
Yeah, full disclosure here, Sam and I are both programming language nerds.

204
00:22:48 --> 00:22:53
And so anytime you give us a chance to talk about programming languages, we will and we won't shut up.

205
00:22:53 --> 00:23:06
But in order to keep that from happening, and also because this next question names four programming languages, Gabriel, I'm going to start with you on this one and then maybe throw it to Jeff.

206
00:23:06 --> 00:23:09
This is from MKWB.

207
00:23:09 --> 00:23:17
I'm primarily a web designer with knowledge only in HTML, CSS, some PHP, some JavaScript, and some Lua.

208
00:23:18 --> 00:23:20
It's a pretty good list, so don't sell yourselves short.

209
00:23:20 --> 00:23:24
But I'm interested in learning to develop apps for Apple platforms.

210
00:23:24 --> 00:23:31
How easy would it be for someone with little to no traditional coding knowledge or skills to get started with Swift and SwiftUI?

211
00:23:32 --> 00:23:38
Yeah, especially for someone who already has experience in stuff like Lua and JavaScript, it's pretty easy.

212
00:23:39 --> 00:23:46
First of all, in terms of Swift as the language itself, it is very similar to many other C-like languages.

213
00:23:46 --> 00:23:49
So you'll be well at home with the syntax.

214
00:23:49 --> 00:23:53
A lot of the concepts will make sense to you if you've done JavaScript.

215
00:23:54 --> 00:24:05
And in terms of SwiftUI as the kind of like embedded language, a lot of it kind of feels similar to what HTML and CSS will be.

216
00:24:06 --> 00:24:09
Obviously, a little bit tangentially different.

217
00:24:09 --> 00:24:32
But, you know, a lot of the concepts and the kind of immediately apparent hierarchy that you're used to by reading through html is kind of the same way that you would declare your views in swift ui um and i again i want to stress don't underestimate the knowledge that you already have it's a huge starting point to have any coding knowledge at all and you know swift enforcing

218
00:24:32 --> 00:24:57
things at compile time and there's also a whole bunch of developer documentation especially now with these agentic tools that are readily available for you what I find particularly helpful so I'm somebody who started with Swift and I've now needed to actually dabble in Objective-C for you know various reasons is I find it actually useful to use these agentic tools and ask them

219
00:24:57 --> 00:25:20
hey I have this like concept of like this flow written out in Swift what is the idiomatic way that this would look in Objective-C or something like that and having that kind of back and forth to say, taking this knowledge that I already have, can you help me convert my mental model into a different idiom? Sure, sure. Jeff, I feel like Gabriel's answer sort of started us walking back

220
00:25:20 --> 00:25:50
your career history. Yeah, so for context, many years before I came to Apple, I started basically doing HTML and CSS, maybe not any PHP. And when I started working on SwiftUI, I found it very similar to to doing that before um especially in terms of like how you describe this structure like like gabriel was talking so um i'd also say that a lot of our kind of uh layout concepts feel pretty

221
00:25:50 --> 00:26:11
at home if you've been working um in kind of building web pages and stuff as well so uh it sounds to me like you have a great startup yeah yeah also the web is like you know was the world of declarative frameworks before apple platforms were right like you know html is itself declarative And then, you know, there are a bunch of frameworks built on top of it that are declarative in a very similar way to Swift UIS.

222
00:26:12 --> 00:26:14
So if you have any experience with those, that helps too.

223
00:26:15 --> 00:26:28
The other thing I'd mention, I think, is if you're coming from the web, you're used to a browser that's updating as you're making edits, which is a little bit different than sort of a compile and run architecture that most native languages have.

224
00:26:29 --> 00:26:34
Check out previews in Xcode so you can see the dynamic updates.

225
00:26:34 --> 00:26:44
So now with Device Hub in Xcode 27, you also have options for showing the simulator right there or actually pulling a mirror of your device on screen.

226
00:26:45 --> 00:26:52
And so there are tools for getting those instantaneous previews, but I think that is one of the road bumps that people hit.

227
00:26:52 --> 00:26:57
And so make sure you check out previews and use that to see how to sort of preview what you're working on.

228
00:27:02 --> 00:27:05
Trevor, I think I'll throw this one to you to start here.

229
00:27:05 --> 00:27:08
This question comes from Classic Flame.

230
00:27:09 --> 00:27:10
My question, where should I start?

231
00:27:11 --> 00:27:18
Especially in SwiftUI, there's so many tutorials, even on the Apple developer side, and it's a little bit confusing what to do first.

232
00:27:18 --> 00:27:19
Where should I begin?

233
00:27:19 --> 00:27:22
I'm a Java developer and want to begin with iOS programming.

234
00:27:23 --> 00:27:23
Thank you.

235
00:27:25 --> 00:27:31
Like I mentioned earlier, those initial tutorials, can we link those somewhere?

236
00:27:32 --> 00:27:35
Yeah, I think we have a mechanism for doing that.

237
00:27:35 --> 00:27:40
I'll ask our folks in the back room if they could respond in the thread with links.

238
00:27:40 --> 00:27:43
Knowing the folks in the back room, they may have already done that.

239
00:27:44 --> 00:27:55
I think those tutorials, there's a series of tutorials that will walk you through everything from, like, here is a view body to state to animations, right?

240
00:27:56 --> 00:27:58
And here's how data flows in SwiftUI.

241
00:27:59 --> 00:28:04
It will give you a really, really solid foundation to start with.

242
00:28:04 --> 00:28:07
And I think that is probably the best initial set of resources.

243
00:28:09 --> 00:28:16
Personally, I think when I started learning SwiftUI, I worked through Paul Hudson's 100 Days of SwiftUI.

244
00:28:16 --> 00:28:17
So say we all.

245
00:28:17 --> 00:28:22
Yeah, which I think lots of people have found that super helpful.

246
00:28:22 --> 00:28:27
And that's a great little very bite-sized chunks of content that are great.

247
00:28:27 --> 00:28:42
Yeah, to add to that, going back to the community resources, There are lots of books people have written about iOS development, lots of, like, articles available online from other authors we have in the community.

248
00:28:43 --> 00:29:03
Yeah, and the official Apple tutorial is great because not only it teaches you SwiftUI, it also teaches you how do you then make your app more production-ready with, I think they also discussed, like, some of the common, like, data storage solutions and, like, databases and how to wire it up to your app.

249
00:29:03 --> 00:29:24
there's lots of depth there so definitely start there and you know if you feel like it's lacking some of the details there's always the community resources that can walk you through and i actually want to throw back to your first answer sima about about getting started um it's like sometimes it's it's instead of just like all right i'm going to do all these tutorials these are what i'm supposed

250
00:29:24 --> 00:29:48
to do sometimes it's a matter of like this is what i want to make and then let that guide the tutorials like you know search or ask the agents yeah yeah that's the most effective way i also uh like like feel like is to learn those tools because when you're we have the goal of like i want my app to do all these features and it's like okay what are the tools that get me to implement

251
00:29:48 --> 00:30:10
those and wire them up all together so that the app does what i initially intended to and that's the best way for you to learn and even if you're following like a tutorial online on like how to build like a certain kind of looking app, I've encouraged you to like think about what would be another feature you can extend this app with to make it more personal to you, not just what the

252
00:30:10 --> 00:30:46
tutorial shows. And that's going to help you kind of really solidify those concepts that you read about. And there's just so much satisfaction in like building something that solved the problem you had. This did make me realize that we haven't talked about the human interface guidelines at all, Which is another great resource on the developer website, which can walk you through some of the kind of design concepts that are common to Apple's platforms and help make your app feel right at home on whatever platform you're developing.

253
00:30:46 --> 00:30:46
Yeah.

254
00:30:46 --> 00:30:54
And that actually raises another thing that I really like to do to, like, learn part of an API or something is, like, play around with other apps.

255
00:30:54 --> 00:30:55
Yeah.

256
00:30:55 --> 00:30:59
Like, there's this really cool thing in mail.

257
00:30:59 --> 00:31:10
If you're in your inbox and you scroll up, the inbox title flows into the title bar, and then the number of messages, like, animates in behind it.

258
00:31:10 --> 00:31:12
And it's just a delightful interaction.

259
00:31:12 --> 00:31:13
I'm like, wait.

260
00:31:14 --> 00:31:16
And I guess I could have – I work at Apple.

261
00:31:16 --> 00:31:18
I could have just gone, ask, how did you build this?

262
00:31:18 --> 00:31:20
But I wanted to learn.

263
00:31:20 --> 00:31:25
And so I, like, tried to figure out how would I build that with the pieces that are in the API.

264
00:31:25 --> 00:31:27
And so it's kind of fun to do that.

265
00:31:27 --> 00:31:29
I'm like, oh, yeah, that's just code.

266
00:31:29 --> 00:31:30
I can do that.

267
00:31:31 --> 00:31:34
I really enjoy that part of the experience, too.

268
00:31:36 --> 00:31:41
All right, so this is a question that may call back to our conversation around states.

269
00:31:41 --> 00:31:42
I'll start with you, Sam.

270
00:31:42 --> 00:31:44
This is from Charlie.

271
00:31:45 --> 00:31:56
Charlie asks, what are the best practices in SwiftUI to handle frequent real-time state updates, like sensor data, without causing unnecessary view redraws or lagging the UI?

272
00:31:57 --> 00:31:58
I'll throw to you after this, Gabriel.

273
00:31:59 --> 00:32:00
I think you'll have something to say too.

274
00:32:00 --> 00:32:05
There's a lot of tools at your disposal here, and it's really going to depend on the specific kind of data.

275
00:32:05 --> 00:32:16
But if you have things that are updating on a per-frame basis or very, very frequently, just add observable is a really good tool for these kinds of things.

276
00:32:17 --> 00:32:39
If you put data in an add observable and then you use it in various parts of your app, SwiftUI makes sure that only things that changed in your observable actually cause UI updates. It's very specific about that. Other things to make sure of are make sure you're not updating values that are dependent on various places in your UI

277
00:32:39 --> 00:32:43
at too high a frequency, right? You want to very much limit your dependencies.

278
00:32:44 --> 00:32:56
Gabriel, do you have a follow-up on that? Yeah, and like what Sam was saying, you kind of want to make sure that you have as cheap a view body as you can get, as much of a leaf view, if you may.

279
00:32:56 --> 00:33:06
for these types of like rapidly updating things because you don't want to have a situation where we throw away large chunks of your data and have to redraw that.

280
00:33:07 --> 00:33:24
And also if you have content that, you know, is like a series of content, there's stuff like timeline view, and also if you need lower level control, you also have things like lazy stacks where you can kind of consolidate the information that you have directly to whatever is in your viewport.

281
00:33:25 --> 00:33:30
A lot of these optimizations are also handled for you if you have stuff in a foreach.

282
00:33:30 --> 00:33:45
So if you have a foreach that lives inside of something that is lazy, like a lazy stack or a list, you can kind of be rest assured that we will try to make sure that we're only invalidating and redrawing as much as we need to.

283
00:33:45 --> 00:33:50
But yeah, so keep your view bodies small and evaluate where you can make things lazy if you can.

284
00:33:50 --> 00:33:50
Yeah.

285
00:33:50 --> 00:33:53
So a couple of other things I would add to that.

286
00:33:53 --> 00:34:02
If you have something that's updating per frame, I would ask the question does your UI actually need to update per frame or can your data model compute some semantic value?

287
00:34:02 --> 00:34:23
like let's say you really have three states that you're computing between You know something that does update at a very high frequency Then you only have to re-evaluate your view body Whenever you transition between those three states and so you can get a lot of performance gains there And then the other like just general best practice is avoid putting those things in the environment, right?

288
00:34:23 --> 00:34:28
Because you may invalidate a lot of views downstream of that.

289
00:34:28 --> 00:34:32
Yeah, and to add to that, I also wanted to touch on the concurrency angle a little bit.

290
00:34:32 --> 00:34:40
I think it's very easy when you have something like timeline view or something kind of updates per frame, kind of accidentally start doing some asynchronous work.

291
00:34:40 --> 00:34:41
And that can add up quickly.

292
00:34:41 --> 00:34:48
And SwiftUI views work best when the inputs to the views are more synchronous.

293
00:34:48 --> 00:34:52
And you kind of keep most of the async stuff outside.

294
00:34:52 --> 00:34:57
and then you also get to test that and really reason about it a little bit better.

295
00:34:57 --> 00:35:04
And that also helps us make your animations way smoother and view updates work more effectively.

296
00:35:04 --> 00:35:15
- Also just to quickly bring it back to somebody who's maybe a little newer to the framework, I think the thread across all these pieces of advice is break things up into small reusable pieces.

297
00:35:15 --> 00:35:23
It'll both help with your code organization so you can understand the things you're writing, But it also plays really nicely with SwiftUI's performance model.

298
00:35:23 --> 00:35:24
So that's how you'll get the best code.

299
00:35:24 --> 00:35:24
Yeah.

300
00:35:25 --> 00:35:26
Sorry.

301
00:35:26 --> 00:35:26
No, please.

302
00:35:27 --> 00:35:33
I was going to say, if you're just getting started with this too, you can take a lot of comfort in that SwiftUI manages so much of this for you, right?

303
00:35:33 --> 00:35:37
And so there's no need to get really stressed out about this performance right off the bat, right?

304
00:35:37 --> 00:35:41
You're going to get really good, lazy performance out of SwiftUI just by using it naturally.

305
00:35:42 --> 00:35:44
And then optimize when you need to, right?

306
00:35:44 --> 00:35:46
Like profile and try to optimize faster.

307
00:35:46 --> 00:35:48
The SwiftUI instrument is really good for profiling.

308
00:35:48 --> 00:35:51
It really helps you get a lot of insights about where you're spending time.

309
00:35:52 --> 00:35:54
Yeah, so instruments is an advanced tool.

310
00:35:54 --> 00:35:58
It's probably not something you're picking up on the first stage of your journey.

311
00:35:58 --> 00:36:07
But if you're coming from maybe another one of Apple's UI frameworks to SwiftUI, maybe you know instruments already.

312
00:36:07 --> 00:36:13
So not all beginners to SwiftUI are beginners in general.

313
00:36:14 --> 00:36:23
You can start by trusting SwiftUI's laziness, And then when you start, like, running into performance bottlenecks, that's when it's a really good time to start breaking out the heavy-hitting tools and doing some analysis.

314
00:36:25 --> 00:36:27
So, Charlie, that was a great question.

315
00:36:27 --> 00:36:31
Clearly, we have a few minutes on that one, so I appreciate that.

316
00:36:32 --> 00:36:36
So the next question, and Sima, maybe I'll start with you on this one.

317
00:36:36 --> 00:36:38
This comes from Itricho.

318
00:36:39 --> 00:36:46
And the question is, has Apple intelligence within Xcode been fed with all the information and documentation around SwiftUI?

319
00:36:47 --> 00:36:52
Having tried to vibe code with Gemini, its ability was okay, but often hit recurring issues with Swift.

320
00:36:53 --> 00:37:09
Yeah, that is a common issue we see with large language models, kind of sometimes producing a little bit incorrect results when it comes to SwiftUI and some of our newer APIs that maybe the model wasn't really trained on yet.

321
00:37:09 --> 00:37:26
But this year, we've introduced SwiftUI skills that actually we're fed with all of our internal knowledge, all the best practices we have for data flow in your apps, for the new APIs that we introduced.

322
00:37:26 --> 00:37:32
And you get them out of the box when you use agent coding in Xcode.

323
00:37:32 --> 00:37:35
It automatically has access to all these skills.

324
00:37:35 --> 00:37:40
So every time you build a new app, it's going to automatically invoke those skills whenever needed.

325
00:37:41 --> 00:37:47
There is a way to export those skills to use with those other third-party models that you may be using.

326
00:37:48 --> 00:37:54
We did cover a way to do that in What's New in SwiftUI talk, so go ahead and check that out.

327
00:37:54 --> 00:37:59
I think it mentions that in the very last bit of that talk, but it is very useful.

328
00:37:59 --> 00:38:01
The third-to-last slide of What's New in SwiftUI.

329
00:38:02 --> 00:38:04
So watch to the end.

330
00:38:05 --> 00:38:09
Anything to add to that?

331
00:38:09 --> 00:38:11
I think that sort of sums it up.

332
00:38:11 --> 00:38:16
The nature of LLMs is they have the data that they were trained on at the time they were trained.

333
00:38:16 --> 00:38:25
And so skills and the other things that we're adding are a great way to level them up as we level ourselves up.

334
00:38:25 --> 00:38:27
Yeah, and also they're always evolving.

335
00:38:27 --> 00:38:33
So, I mean, if you find that it is stumbling in the new Xcode skills that we have, please, please file feedback.

336
00:38:34 --> 00:38:36
it'll really help us out in improving these tools for everybody.

337
00:38:36 --> 00:38:50
But we did find that the models got significantly better reasoning when they were invoking those skills, so we're sure that the skills should provide greater comfort when using those models.

338
00:38:51 --> 00:39:08
Also, while you're mentioning filing feedback, if you're new, especially to Apple Platforms development, file lots of feedback about everything, any issues you hit in the betas, even just things that are confusing to you about the framework, getting to hear your thoughts on the tools we're shipping is like how we decide how to evolve them.

339
00:39:08 --> 00:39:12
Yeah, and there's two ways to give us that feedback.

340
00:39:12 --> 00:39:24
If you're running the betas, their feedback assistant app is part of all the betas now, so you can file feedback right from your device, whether that's your MacBook or your phone or your iPad.

341
00:39:26 --> 00:39:44
If you aren't running the betas, but you're running, well, whatever you're running, I guess, But especially feedback on the skills that we've added in Xcode 27, you can go to feedbackassistant, all one word, .apple.com, and that's the other way to file that if you don't have the betas installed.

342
00:39:45 --> 00:39:50
Do developers need to do anything to enable the skills, or they're just kind of there by default?

343
00:39:50 --> 00:39:51
They should be there.

344
00:39:51 --> 00:40:08
When you go to Xcode and you invoke the coding agent window, or you prompt a model, the skills should be already loaded, And I think that they would be invoked based on the context of what the agent is working on.

345
00:40:08 --> 00:40:11
So you have a SwiftUI file open, and it's going to kind of know.

346
00:40:11 --> 00:40:12
Right, yeah.

347
00:40:12 --> 00:40:18
So it should kind of refer to the appropriate skill when needed based on the context, which is great.

348
00:40:18 --> 00:40:23
And if you're using an LLM outside of Xcode, make sure you check out what's new in SwiftUI so you can export those skills.

349
00:40:23 --> 00:40:25
Because those will not have that baked in by default.

350
00:40:25 --> 00:40:25
Right.

351
00:40:27 --> 00:40:30
And let me try for a segue here.

352
00:40:32 --> 00:40:39
Our next question is from Azi, and Azi, and I think I'm going to start with you on this one, Jeff.

353
00:40:39 --> 00:40:40
So it's back to the performance question.

354
00:40:41 --> 00:40:47
What are the best practices in order to make my SwiftUI views performant and not have unnecessary view updates?

355
00:40:47 --> 00:40:55
And the segue here is that the agentic coding conversation in Xcode is implemented in SwiftUI, and it's highly performant.

356
00:40:55 --> 00:41:02
And so it's definitely possible to make highly performant code with SwiftUI.

357
00:41:02 --> 00:41:24
but i wonder if we could talk a little bit more about what are some of the best practices and we talked about breaking views down but is there any more we can say there yeah and i think we we talked a little bit about using observable this is like a really nice tool um because uh the way it works is uh only properties that that change that are also read by your view uh will establish

358
00:41:24 --> 00:41:45
that dependency to kind of reevaluate the body um so if you if you change a property that is only read by, you know, say one view or two views, then only those are going to update and you don't have to worry about any unnecessary updates outside of that. Yeah. And I think that gets at the mental model that I find helpful for thinking about SwiftUI view updates. Like if you've got

359
00:41:45 --> 00:42:13
a view and it's got, I hope this works on, you know, let me draw with my hand. But if you've got a view and it has a bunch of sub views in it and then they have sub views and so it sort of forms like a tree, when the view at the top has to run its body, anything that it reads will cause its body to run. Then it instantiates the views underneath it. But if they haven't changed,

360
00:42:14 --> 00:42:35
we don't need to run their bodies. It just runs that one at the top. But similarly with observables, so if you've got an object that represents the data that you're showing, if there's a view way down the tree, and it's the only one that reads one of those pieces of data, then only that little one down at the end needs to update. And so like, if you look at your views, and you've got a view

361
00:42:35 --> 00:42:44
that is reading every single property of some observable, well, that view is going to update a lot, right? And so that's an opportunity to maybe break out some pieces of that.

362
00:42:45 --> 00:43:03
Yeah, one advice I have is also, and I think we touched on that, but make sure to, with environment, to make sure to kind of only read the environment that you're actually using in Vue's body because every time environment changes, we also have to invalidate all the Vues that read it so that they stay up to date.

363
00:43:03 --> 00:43:09
And it is an important kind of thing to watch out for to keep the unnecessary environment out.

364
00:43:10 --> 00:43:10
Yeah, yeah.

365
00:43:10 --> 00:43:27
So for folks who haven't experienced or worked with this yet, environment is a mechanism for injecting some values at the top of a Vue hierarchy and then being able to read it down at the leaves without having to pass it along every time.

366
00:43:27 --> 00:43:37
And it was really designed for things like color scheme, screen size, stuff that doesn't change rapidly.

367
00:43:37 --> 00:43:44
And you can also pass observables through it, and that works fine because they're only updating at the point they're read from.

368
00:43:44 --> 00:44:04
but um you know avoid and that's what you're saying sima avoid like you know don't pass the current time in milliseconds in the environment because everything down the tree is going to be updating all the time yeah i i did want to get back i guess a little bit to what you said earlier about breaking things up i think this is also a really important thing to think about in terms of

369
00:44:04 --> 00:44:19
trying to make your your view bodies kind of as small as uh is reasonable for for what they need to do and kind of extracting out pieces into your own custom views and spreading things apart that way.

370
00:44:19 --> 00:44:25
It's also a great tool for being able to reuse components across different places of your app.

371
00:44:25 --> 00:44:38
Yeah, and to add to that, a great way to kind of spot check those places is sometimes you notice how you add the same set of modifiers in different parts of your view hierarchy.

372
00:44:38 --> 00:44:46
And that's a great way to take those modifiers and abstract it into a custom modifier that you can then apply.

373
00:44:46 --> 00:44:52
And same thing with some bits of your views that look repetitive across the app.

374
00:44:52 --> 00:44:55
You can take them into custom views and then reuse them.

375
00:44:55 --> 00:45:00
And that's going to make your code more maintainable, more readable.

376
00:45:00 --> 00:45:03
And yeah, I think it's a great way to go about that.

377
00:45:03 --> 00:45:04
TIMOTHY JORDAN: Yeah, I love that.

378
00:45:04 --> 00:45:11
MARK MANDEL: And there's a couple of things I saw talking to developers in the community and questions that came up a lot that are also related to performance.

379
00:45:11 --> 00:45:23
One way people will sometimes try to simplify a view that has a very large body is to pull pieces of that view out and put them in computer properties in the same view.

380
00:45:23 --> 00:45:27
I wonder, you're smiling, so I'm gonna take this to you, Trevor.

381
00:45:27 --> 00:45:31
Is that effective or is, what's the right approach there?

382
00:45:31 --> 00:45:43
- I think it's just inferior to refactoring that chunk, That chunk of the view into its own custom view.

383
00:45:43 --> 00:45:44
And there's a couple reasons for that, right?

384
00:45:45 --> 00:45:52
One is if you, let's say you have a button that's at the bottom of a view and some custom stack of buttons, let's say.

385
00:45:52 --> 00:46:01
If I refactor that into its own custom view, then one, SwiftUI can only reevaluate that view body when something that actually relies on changes.

386
00:46:01 --> 00:46:02
So you can get better performance there.

387
00:46:02 --> 00:46:08
But two, now I have better modularization of my code base and I can go reuse that stack of buttons.

388
00:46:08 --> 00:46:10
somewhere else.

389
00:46:10 --> 00:46:11
It can help with testing.

390
00:46:11 --> 00:46:16
There's so many other benefits that you would get by just moving it into a custom view.

391
00:46:16 --> 00:46:19
And personally, I think it's much more readable.

392
00:46:19 --> 00:46:25
So I would avoid that pattern of these computer properties inside of a view.

393
00:46:25 --> 00:46:28
I feel like it does also depend on your use case.

394
00:46:28 --> 00:46:34
Because sometimes if the property is really small and is only for readability, it's fine.

395
00:46:34 --> 00:46:45
But because sometimes when you move into a custom view, you also have to move some of the state into it, and maybe you're only planning to really use that code in one view.

396
00:46:45 --> 00:46:55
So it really just depends on your usage, but I think the general guidance should definitely be abstract more pieces outside of your view and put them in the custom views.

397
00:46:55 --> 00:46:57
I think it's a great guidance in general to have.

398
00:46:58 --> 00:47:06
Yeah, from a performance standpoint, moving something out of body and into a computer property doesn't actually buy you anything.

399
00:47:06 --> 00:47:08
It's as if it was in body.

400
00:47:08 --> 00:47:10
It really is only a readability win.

401
00:47:11 --> 00:47:17
So until you break it into a separate view that SwiftUI can invalidate separately, you don't get the performance improvements.

402
00:47:18 --> 00:47:20
Can I say two more things on this?

403
00:47:20 --> 00:47:20
Yes, please.

404
00:47:21 --> 00:47:23
Sorry, I have so many ideas about performance apparently.

405
00:47:25 --> 00:47:30
Avoid doing any heavy work inside of view bodies because they may run frequently.

406
00:47:30 --> 00:47:41
So like one example I think it's easy to reason about is you might create a number formatter inside of a view body to format some number that you're showing inside of text.

407
00:47:41 --> 00:47:47
Can you create that number formatter somewhere else that's cached that's not being allocated every time your view body runs?

408
00:47:48 --> 00:47:51
So if you can avoid those little things, those are easy refactors.

409
00:47:51 --> 00:47:57
Array transformations are another one that people run into with like a for each and things like that that are often a problem.

410
00:47:57 --> 00:48:02
And then the second thing is, don't worry about this too much if you're just starting SwiftUI.

411
00:48:02 --> 00:48:02
That's good.

412
00:48:03 --> 00:48:04
It's going to do so much for you.

413
00:48:04 --> 00:48:06
It's good performance out of that.

414
00:48:06 --> 00:48:08
Also, all of this guidance is in the skills.

415
00:48:09 --> 00:48:16
So if you're using a model to generate that code, it should automatically put refactor views into smaller views.

416
00:48:17 --> 00:48:19
So that's great, I think.

417
00:48:19 --> 00:48:25
And that is a perfect segue to our next question from N5A.

418
00:48:26 --> 00:48:29
I feel like Simo might be reading ahead.

419
00:48:30 --> 00:48:32
This is from N586FL.

420
00:48:33 --> 00:48:34
Rolls off the tongue.

421
00:48:34 --> 00:48:51
In a world of agenic AI, what is the effective way to leverage AI within Xcode and SwiftUI to help ensure that the student is learning SwiftUI and not just having the AI write the code for them?

422
00:48:52 --> 00:48:58
And is there an effective learning plan that incorporates AI without derailing people's education?

423
00:48:59 --> 00:49:03
And the teacher in my heart just loves this question.

424
00:49:03 --> 00:49:05
It's so important.

425
00:49:05 --> 00:49:08
So any thoughts on this?

426
00:49:08 --> 00:49:09
I would love anybody to chime in.

427
00:49:10 --> 00:49:11
Ask questions.

428
00:49:11 --> 00:49:12
Ask AI questions.

429
00:49:12 --> 00:49:19
Like when you see it spit out like a chunk of code and you don't understand what the code is doing, ask it a question.

430
00:49:19 --> 00:49:21
Like, hey, what is this doing?

431
00:49:21 --> 00:49:30
And if you have an idea in your head that's like, oh, maybe I could have done this differently, then ask it again and be like, hey, could I do it this way?

432
00:49:30 --> 00:49:31
And how can I accomplish that?

433
00:49:31 --> 00:49:36
And then I feel like that is the most effective way to learn.

434
00:49:36 --> 00:49:51
And I think if you don't ask those questions, you'll quickly find yourself in a situation where you have an app, but then you're trying to add something to it, but you feel like you don't even know how the code is structured and where should the new piece go.

435
00:49:51 --> 00:49:59
And I think building that confidence with your code through asking those questions is a great way to learn.

436
00:49:59 --> 00:50:05
- And I think, like Sam was mentioning earlier, it's like really treat it like a tutor, especially when you're a beginner.

437
00:50:05 --> 00:50:11
When you're more advanced, you might feel more comfortable trying to put as much speed into your work as possible.

438
00:50:11 --> 00:50:14
But when you're a beginner, treat it like a tutor.

439
00:50:14 --> 00:50:16
You're asking it questions.

440
00:50:16 --> 00:50:21
When it's writing something, it's supposed to be an informative thing.

441
00:50:21 --> 00:50:26
A tutor is not going to tell you the answer to your question and then just leave it at that, right?

442
00:50:26 --> 00:50:28
It doesn't do anything for you.

443
00:50:28 --> 00:50:32
So when you get something generated by an AI, definitely be, like, interrogative about it.

444
00:50:32 --> 00:50:34
Ask a lot of questions.

445
00:50:34 --> 00:50:43
If you don't understand step-by-step what the AI is writing, try just, like, hammering home what is this code doing?

446
00:50:43 --> 00:51:03
because it's honestly it's one of the greatest ways to learn because you get to experiment really fast and you get to learn concepts basically on the fly without even having to have like some pre-recorded thing so also like as you go up the skill curve too and you are getting to the point where you're like more comfortable and making a lot of the decisions

447
00:51:03 --> 00:51:24
for yourself uh i encourage people to like treat llms like less like an employee and more like a co-worker like you know when I'm working with anyone on this panel like so often I'll be like hey I've got this idea I want to build this thing here's what I'm thinking like what do you think of this like is this at all correct like and you sort of go back and forth and then it will you know

448
00:51:24 --> 00:51:53
give you the the LM or your co-workers will like give you an answer and then sometimes you push back on that answer and it's like this real back and forth and like that's how you learn stuff it's the most sophisticated rubber ducky we've ever invented yeah seriously exactly totally yeah I mean, to me, I think, like, programming is a very creative endeavor, and it's important, you know, at least to me, that, like, I maintain that when doing it.

449
00:51:53 --> 00:52:04
And so I think everyone needs to find the right balance between how they want to use these tools and also, you know, get the creative enjoyment out of building whatever apps they're building as well.

450
00:52:04 --> 00:52:19
And I think, you know, having taught computer science, one of the really, like, fascinating things to watch as students sort of grow is you start being able to think in bigger chunks, right?

451
00:52:19 --> 00:52:30
The first time you sit down with the programming language and you're like, let X equals 5, like, this whole idea of storing something is a big deal, and it takes a bit to wrap your head around.

452
00:52:30 --> 00:52:34
And then you've got loops and methods and classes.

453
00:52:34 --> 00:52:39
And we've sort of, we've been moving up and down this abstraction hierarchy in our conversation today.

454
00:52:40 --> 00:52:48
And one of the things about the LLMs is, like, they can go to the fully abstract end of it, right, and skip right over sort of this foundational knowledge.

455
00:52:48 --> 00:52:54
And so, like, maybe take smaller steps, like, explain this loop to me.

456
00:52:54 --> 00:52:55
Why does that solve the problem?

457
00:52:55 --> 00:52:57
Or I need to do this thing.

458
00:52:58 --> 00:52:59
What are three options?

459
00:52:59 --> 00:53:03
Okay, then I can ask the LLM questions, like, well, why would that be a good option?

460
00:53:03 --> 00:53:04
What's interesting about that?

461
00:53:04 --> 00:53:06
Or why might this be a better option?

462
00:53:07 --> 00:53:15
And it's, like, with an LLM, you can be the wonderful or annoying student who's, like, raising their hand to ask a question all the time.

463
00:53:15 --> 00:53:17
And it never gets tired of answering them.

464
00:53:17 --> 00:53:20
Yeah, it's, like, maintain your curiosity, basically.

465
00:53:20 --> 00:53:23
You mentioned, like, the pros versus cons of an approach thing.

466
00:53:23 --> 00:53:31
Like, in my opinion, the difference between, like, a good engineer and a great engineer is their ability to, like, look at multiple possible decisions and, like, weigh the tradeoffs between them.

467
00:53:32 --> 00:53:37
So, like, making sure that you're not letting the LLM do that part of the analysis is, I think, really important.

468
00:53:38 --> 00:53:46
Yeah, I was about to say, like, the same thing almost, where, like, in, like, engineering generally, there are, like, many ways to accomplish one solution.

469
00:53:47 --> 00:53:51
And maybe one way is, like, more performant than another one, or, like, maybe it uses a different tool.

470
00:53:51 --> 00:53:59
And I think when you kind of master some of those concepts, you're able to kind of tell, okay, for this use case, I'm going to use this tool.

471
00:53:59 --> 00:54:00
Like, I'm going to use this data structure.

472
00:54:01 --> 00:54:10
And, like, through this kind of prompting, you'll be able to discover all these tools and kind of see, like, given this specific example I have, like, what would be the best approach?

473
00:54:11 --> 00:54:19
Because I feel like it can seem like the solution is, like, there's only, like, one solution to your current problem when you're, like, using LLMs.

474
00:54:20 --> 00:54:29
But in reality, there can be many alternative approaches that, like, you should be able to kind of weigh on and, like, figure out, like, what is the best one.

475
00:54:29 --> 00:54:38
It feels like the culture around coding with LLMs right now has moved so much to, like, how do we produce more code faster, faster, faster, right?

476
00:54:38 --> 00:54:40
And, like, at the end of the day, the goal isn't code, right?

477
00:54:40 --> 00:54:42
Like, the hard part is understanding.

478
00:54:42 --> 00:54:52
And so I still think, like, maybe I haven't moved up the skill curve, but 90% of the time that I'm using these things, I'm just using them to, like, build a deeper understanding of the code I'm working in.

479
00:54:52 --> 00:54:57
And, like, I still find as an engineer that that is, like, they're incredible for that.

480
00:54:58 --> 00:55:10
And if you can, like, avoid the trap of, like, how do I just produce more code and think, like, how can I use this to, like, really, really, like, gain a deeper understanding that I might not be able to otherwise, I think that's an incredible way to leverage that.

481
00:55:10 --> 00:55:10
Awesome.

482
00:55:11 --> 00:55:16
So the clock says we've only got about five minutes left, and we've got a few questions queued up still.

483
00:55:17 --> 00:55:26
So maybe we switch to game show mode and see how fast we can click through these.

484
00:55:27 --> 00:55:27
I've got a buzzer.

485
00:55:27 --> 00:55:33
So feel free to interrupt your teammates if you think they're going to run out the clock.

486
00:55:33 --> 00:55:37
But let's see how many of these we can wrap up before we wrap up.

487
00:55:37 --> 00:55:38
So next one.

488
00:55:39 --> 00:55:41
And I'm just going to send these down the line.

489
00:55:41 --> 00:55:42
If you need a pass, pass.

490
00:55:43 --> 00:55:44
So, Sam, we'll start with you.

491
00:55:47 --> 00:55:48
Noelle Linger.

492
00:55:48 --> 00:55:50
So now I'm blowing our time by mentioning you.

493
00:55:52 --> 00:55:57
Noelle Linger writes, I'm looking for a high-level resource to build a clear mental model of Swift UIs.

494
00:55:58 --> 00:56:05
They're an official one-page overview, chart, flow diagram that explains how these pieces fit together and what options developers have.

495
00:56:05 --> 00:56:06
I forget what they're called.

496
00:56:06 --> 00:56:10
It's not a document, but there are a lot of talks that were released, especially around the early days of the framework.

497
00:56:10 --> 00:56:17
Yes, we did an intro to Swift UI where Jacob codes up an app using sandwiches.

498
00:56:18 --> 00:56:21
It doesn't actually use the sandwiches to make the app, but it's an app about sandwiches.

499
00:56:22 --> 00:56:24
And also we have SwiftUI Essentials video.

500
00:56:25 --> 00:56:30
And there's a great one-pager on getting started with SwiftUI on the developer site.

501
00:56:30 --> 00:56:31
Definitely check those out for me.

502
00:56:31 --> 00:56:33
I've always found, like, the sessions are killer.

503
00:56:33 --> 00:56:35
They teach so much so quickly.

504
00:56:35 --> 00:56:37
So I can't recommend enough, giving them a watch.

505
00:56:37 --> 00:56:37
All right.

506
00:56:38 --> 00:56:40
Trevor, you get the next one.

507
00:56:40 --> 00:56:41
From Fossil Coder.

508
00:56:42 --> 00:56:43
I feel like a kindred soul.

509
00:56:44 --> 00:56:47
When I use AI tools to write SwiftUI code, I keep hitting the same errors.

510
00:56:48 --> 00:56:50
Code looks right but won't compile or behaves unexpectedly.

511
00:56:51 --> 00:57:00
How can I use Xcode's AI tools more effectively as a beginner so I'm learning correct SwiftUI patterns instead of picking up bad habits from broken suggestions?

512
00:57:01 --> 00:57:03
- Sima, you might be better at this one than I am.

513
00:57:03 --> 00:57:04
The answer is skills.

514
00:57:04 --> 00:57:12
- Yeah, you should, again, third time of me kind of plugging this, but in Xcode 27, check out the skills.

515
00:57:12 --> 00:57:22
We really put all the best practices into them and the models should be able to kind of pick them up and use the best practices we recommend without those, like, bad patterns.

516
00:57:22 --> 00:57:23
So yeah, check it out.

517
00:57:23 --> 00:57:31
- Also, if you can allow the model to check its work by compiling, right, the speedy of Swift as it moves so many errors to compile time.

518
00:57:31 --> 00:57:34
And so the model can find those and iterate on its approach.

519
00:57:34 --> 00:57:38
- And I would also say, go piece by piece when you're starting out.

520
00:57:38 --> 00:57:45
AI models have this tendency that they really wanna, like, one shot, implement everything at the same time, and that will often lead to errors.

521
00:57:45 --> 00:57:49
So piecemeal it, take as much of it apart as you can.

522
00:57:49 --> 00:57:53
And I think that's going to produce better code because it's going to be more encapsulated.

523
00:57:53 --> 00:57:54
Yep, for sure.

524
00:57:55 --> 00:57:58
Gabriel, I'm going to throw this one to you from Florentine F.

525
00:57:58 --> 00:58:04
What's one thing announced yesterday that you think beginners will overlook or will matter very much as they grow?

526
00:58:04 --> 00:58:07
Okay, so layout flexibility.

527
00:58:07 --> 00:58:10
We had a lot of stuff in this area.

528
00:58:10 --> 00:58:13
And generally with this year, we've done a lot of layout improvements.

529
00:58:13 --> 00:58:24
But layout flexibility is a really big thing, especially because your iPad apps and macOS apps and Vision Pro apps all kind of fall under this umbrella of resizability.

530
00:58:24 --> 00:58:27
And now with iPhone mirroring, your iPhone apps are also resizable.

531
00:58:27 --> 00:58:32
So it's really important to see how much of your app can you make flexible with SwiftUI.

532
00:58:33 --> 00:58:34
We have a lot of constructs.

533
00:58:34 --> 00:58:42
And as you get more advanced, you can even dip down into some of the more complicated things like making your own custom layout when things start to get complicated.

534
00:58:42 --> 00:58:50
And also, Device Hub is a great new application that lets you test all sorts of things, all sorts of aspect ratios.

535
00:58:50 --> 00:58:52
It's really a great resource.

536
00:58:52 --> 00:58:58
I know when I was starting out with iOS development, I only built apps for the size phone that I had.

537
00:58:58 --> 00:59:00
Because that's what I was testing.

538
00:59:00 --> 00:59:05
So Device Hub is going to be great and resizable previews for testing on different sizes.

539
00:59:06 --> 00:59:07
All right.

540
00:59:07 --> 00:59:10
I think we can get one more in if you're fast, Jeff.

541
00:59:10 --> 00:59:12
This is from Matasayali.

542
00:59:13 --> 00:59:17
"How to best convert a design of an app "from a designer to SwiftUI?

543
00:59:17 --> 00:59:19
"Do you recommend the Xcode Coding Assistant?

544
00:59:19 --> 00:59:20
"And if so, which one?"

545
00:59:20 --> 00:59:21
- Oh wow.

546
00:59:22 --> 00:59:25
I can't say this is ever something I've had to do.

547
00:59:25 --> 00:59:38
I would say probably I would go about it by looking at what they've built and considering the various layout primitives that we have in SwiftUI, and kind of using your own translation engine to do that.

548
00:59:38 --> 00:59:40
I don't know if we have a skill that will handle this automatically.

549
00:59:40 --> 00:59:41
- We do not.

550
00:59:41 --> 00:59:52
We also have connections to-- - Yeah, I was about to say about the Figma connections, and we have Sketch and Figma connections that produce SwiftUI code.

551
00:59:52 --> 00:59:52
- Oh, nice.

552
00:59:52 --> 00:59:55
- Given the design you're making in those tools.

553
00:59:55 --> 00:59:57
So definitely go check those out.

554
00:59:58 --> 01:00:00
And yeah, I don't know, anyone else?

555
01:00:00 --> 01:00:02
- Well, I think-- - Oh, dang it.

556
01:00:05 --> 01:00:08
- So we're gonna leave it on a cliffhanger.

557
01:00:08 --> 01:00:16
Come back and ask the question again at our 9:00 a.m. Pacific group lab tomorrow, and we can pick up the conversation.

558
01:00:16 --> 01:00:18
But that's the end of this group lab.

559
01:00:18 --> 01:00:21
We're so thankful that you all joined us today and hope you found this really helpful.

560
01:00:21 --> 01:00:28
A big thanks to our panelists, as well as all of the folks working hard behind the scenes to make this happen today.

561
01:00:28 --> 01:00:37
As I mentioned earlier, if we didn't get to your questions, please visit the developer forums at developer.apple.com/forums, where we can continue the discussion or go deeper into code.

562
01:00:37 --> 01:00:42
and visit feedbackassistant.apple.com to file any bugs or feature requests.

563
01:00:42 --> 01:00:45
We really do appreciate that feedback.

564
01:00:45 --> 01:00:53
Speaking of feedback, you should receive an email with a survey link to let us know about your experience with WWDC.

565
01:00:53 --> 01:00:57
We would love to incorporate your feedback into future events.

566
01:00:57 --> 01:01:00
Thanks again for joining us, and have a great WWDC.
