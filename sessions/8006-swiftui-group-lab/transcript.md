---
title: SwiftUI Group Lab
source: https://developer.apple.com/videos/play/wwdc2026/8006/
session: 8006
collection: wwdc2026
duration: 01:00:49
fetched: 2026-06-12
via: whisper.cpp ggml-large-v3 (session SD video audio)
transcription: machine-generated, no speaker diarization
---

> Machine transcription of the **SwiftUI Group Lab** SD video via whisper.cpp (`ggml-large-v3`).
> Group labs ship no Apple transcript; this is generated from the audio. Cues are timestamped segments, **not** speaker-attributed turns.

## Transcript

1
00:00:03 --> 00:00:06
Hi, and welcome to another SwiftUI and UI Frameworks Group Lab.

2
00:00:07 --> 00:00:11
I'm Kurt, and I'm part of the Worldwide Developer Relations team here at Apple.

3
00:00:11 --> 00:00:19
I'm joined by engineers and leaders from the UI Frameworks team, Aditya, Jason, Taylor, David, and Seema.

4
00:00:20 --> 00:00:26
In addition to those on screen, there's a team behind the scenes helping with the triage of all your inbound questions.

5
00:00:26 --> 00:00:31
We're excited to answer your questions today about all things SwiftUI.

6
00:00:31 --> 00:00:45
If you have code-specific questions or we somehow can't get to your question today, please bring your questions to the developer forums at developer.apple.com slash forums, where we'll continue the discussion.

7
00:00:46 --> 00:00:56
If you have a bug or a feature request, go to feedbackassistant.apple.com, as we'll focus on questions today that will help the broadest audience.

8
00:00:57 --> 00:01:00
So without further ado, let's go to your questions.

9
00:01:00 --> 00:01:19
So our first question today is from UJ. And UJ says, I have an architecture question. Is there an architecture like MVC or MVVM or other that the SwiftUI team expects us to adopt? Sima, do you want to start us off on that one?

10
00:01:19 --> 00:01:37
Yeah. So there's really no architecture that we expect you to adopt. SwiftUI is really designed to be architecture agnostic and really like any question, any architecture that works for your specific app is would work, should work with SwiftUI.

11
00:01:37 --> 00:01:56
and if for some reason the architecture of your choice integrates poorly with SwiftUI or you see some ergonomic issues with trying to pass data around or other issues, feel free to let us know and we will see what we can do to make sure your experience is the best possible.

12
00:01:57 --> 00:02:07
So David, in your WW talk this year, you talked about using Observable with AppKit and UIKit and then how that makes it easy to transfer to SwiftUI.

13
00:02:07 --> 00:02:35
does that have implications for choosing an architecture you can use absorbable to store your model and make it easier to transition to the new framework but it's not another case is also it it's just Swift code essentially it's not integrated with any like it's integrated with UI frameworks but it's not dependent on them right and so you can use that and just write tests for it and

14
00:02:35 --> 00:02:39
use it in other components and listen to changes yourself.

15
00:02:39 --> 00:02:44
- One of the kind of core messages in this session is like how you can adopt SwiftUI incrementally.

16
00:02:44 --> 00:03:00
So one of the important things with this architecture question is you maybe have an existing app with an existing architecture and SwiftUI needs to be able to handle whatever architecture that is, including as you start to mix and match with AppGate UI kit or as you start to just grow more and more SwiftUI unique code.

17
00:03:00 --> 00:03:04
- Yeah, the other reason is that, you know, your apps can be very different.

18
00:03:04 --> 00:03:16
you can have a very small app for which you probably don't need to go for some elaborate architecture that requires you to refactor most of your code, and you just want to get something started really quickly.

19
00:03:16 --> 00:03:24
And you can also be working on a very large app where you have maybe a team committing daily to the large code base.

20
00:03:24 --> 00:03:34
And maybe in those cases, you want to reach for some of the other solutions that require your code to be more standardized across the entire codebase.

21
00:03:34 --> 00:03:37
So it really just depends on the kind of app that you have.

22
00:03:37 --> 00:03:40
- Yeah, I think it's really underrated, the ability to stay nimble.

23
00:03:40 --> 00:03:45
So you don't want to build your app just because you have a certain architecture.

24
00:03:45 --> 00:03:49
You want to be able to ship features to your users.

25
00:03:49 --> 00:03:52
And that's what should drive all decisions, I think.

26
00:03:52 --> 00:03:54
- Yeah, and I think that's a great point.

27
00:03:54 --> 00:04:04
The other thing that I think's interesting is structuring your data model just around the UI framework is maybe not the leading way to approach it.

28
00:04:04 --> 00:04:08
You have to think about persistence.

29
00:04:08 --> 00:04:09
How are you syncing?

30
00:04:09 --> 00:04:13
Do you need a CRDT to keep simultaneous edits?

31
00:04:13 --> 00:04:17
So all these things drive your data model also.

32
00:04:17 --> 00:04:31
And so if you can keep your SwiftUI views so that just a projection of your data model to pixels and then design the data to be as robust and testable as you can, I think that's the best move.

33
00:04:31 --> 00:04:36
So another question, and I think I'll take this to you first, Jason.

34
00:04:37 --> 00:04:39
So apologies if this is basic.

35
00:04:40 --> 00:04:41
No apologies needed.

36
00:04:41 --> 00:04:43
We welcome all questions.

37
00:04:43 --> 00:04:54
But what's the difference between at view builder closure or computed property and a separate view structure, the performance pros and cons between these approaches?

38
00:04:55 --> 00:05:03
Yeah, so it's quite common that if you have like a very large view body, you'll quickly want to break it up into smaller pieces.

39
00:05:04 --> 00:05:07
It's just it's easier to reason about your view.

40
00:05:07 --> 00:05:16
And often people will create maybe a computed property that is at view builder or now at content builder to do this.

41
00:05:17 --> 00:05:18
And yeah, there are differences.

42
00:05:19 --> 00:05:25
So one of the major ones is that a view itself has its own identity.

43
00:05:25 --> 00:05:29
And so it can actually track dependencies.

44
00:05:29 --> 00:05:41
So you pass in the data that is specific to that view, pass that into this new view, and it can independently track dependencies.

45
00:05:41 --> 00:05:49
Whereas if you're just using the computer property, it really is, in effect, just as if that was still in your view body.

46
00:05:49 --> 00:05:56
You've just moved it out into a separate method.

47
00:05:56 --> 00:06:01
But a new view is entirely itself a new view.

48
00:06:01 --> 00:06:12
And so yeah, maybe David, you have-- or Seema have more to-- Yeah, like from SwiftUI's perspective, we're just executing a function.

49
00:06:12 --> 00:06:15
And we do not have more granular control.

50
00:06:15 --> 00:06:19
If the function is calling other functions, we cannot intervene there.

51
00:06:19 --> 00:06:26
But if it's a separate view that is created, we are the ones that call the body computed property.

52
00:06:26 --> 00:06:33
And so there we can scope the dependency tracking and invalidation to each view independently.

53
00:06:33 --> 00:06:37
- Yeah, so it's really for more performance wins.

54
00:06:37 --> 00:06:49
You want to move some parts of your view out to a separate custom view that has its own body, and that would let us more granularly update only the views that have changed.

55
00:06:49 --> 00:06:51
so that can really help you with performance.

56
00:06:51 --> 00:06:53
- Could I ask, what's the trade-off there?

57
00:06:53 --> 00:07:00
At what, you don't want, probably, every single component made into a separate view, right?

58
00:07:00 --> 00:07:06
So what level should someone reach for creating a sub-view, essentially?

59
00:07:06 --> 00:07:12
- This is one of the surprising things when people approach SwiftUI for the first time, is they think, okay, I can't make too many views, right?

60
00:07:12 --> 00:07:18
There's an overhead to each individual view, and SwiftUI, it's different, where there is this performance benefit.

61
00:07:18 --> 00:07:20
There's also preview benefit.

62
00:07:20 --> 00:07:22
you can now preview things at a finer-grained level.

63
00:07:22 --> 00:07:30
So I don't think that we have a recommendation of, like, what is too small of a view to break apart if there's reasonable dependency differences.

64
00:07:30 --> 00:07:43
Yeah, I mean, if we tunnel into the detail level, it's like each view, because it's a struct, is allocated on the stack, and so it's very inexpensive to allocate a view, render it, and then discard it.

65
00:07:43 --> 00:07:51
And so the fewer pieces of state that a particular view depends on, the less likely it is to have to be redrawn.

66
00:07:51 --> 00:07:53
So that's where the performance win comes from.

67
00:07:53 --> 00:07:59
It's useful to think of your view body like a description of your views.

68
00:08:00 --> 00:08:01
They're very short-lived.

69
00:08:01 --> 00:08:05
You could even think about it as just a data model for your views.

70
00:08:06 --> 00:08:10
They're not going to stick around very long, so it's very lightweight.

71
00:08:10 --> 00:08:20
I think we often talk about how great Observable is for invalidation, and if it's only a small little piece of your big view reads that property, it invalidates the whole view.

72
00:08:20 --> 00:08:24
So that's like another great example of a place where you can extract out and minimize the dependencies.

73
00:08:25 --> 00:08:26
Right, yeah, that makes sense.

74
00:08:26 --> 00:08:37
And it also extends beyond views to modifiers where we have the same concept where you can extract a couple of modifiers into your own modifier and then get the same benefits as splitting it up into multiple views.

75
00:08:37 --> 00:08:38
That's true.

76
00:08:38 --> 00:08:58
Yeah, and if you're looking for advice on like how and when to start like breaking up your views, the kind of guiding principle that I would recommend is also like if you're seeing maybe like a repeated pattern of like the same set of modifiers that you apply like across your view hierarchy maybe you have like some some sort of like a style system like

77
00:08:58 --> 00:09:21
in your app or like there's a repeated kind of pattern that you have then that's a great place to say okay maybe this can be a custom modifier that I can just apply in various parts of my view hierarchy and that same goes for custom views. It's also sometimes important to have a view so that you can do things like read read from the environment right and so um one example where i

78
00:09:21 --> 00:09:49
this this maybe uh trip some people up is like um we have an environment value called background prominence that is increased if um when a uh like a a table or a list row is selected and so you can you can use that in your view to adapt to that selection state and maybe uh you know change the color of um of something in the selection so that so that it appears better against like the darker

79
00:09:49 --> 00:10:17
background um and if you don't create a new view uh if you just have the stuff that's within that within uh the cell um you you can't necessarily read from the environment um so that's that's another i think good use of for doing that um so i think i'll start with you on this next question taylor um and especially as we um we see more internal adoption all the time with swift ui

80
00:10:17 --> 00:10:40
um what are some common anti-patterns we see people um you know fall into with with swift ui i'll open this up to everyone but we'll start with taylor that's a good question and like i think like there's anti-patterns and then there's also like what's the right way to do things yeah um a number of things come to mind um i think we have things like uh geometry reader which we often talk about

81
00:10:40 --> 00:10:47
how it's great to analyze where you're using Geometry Reader in your app and whether or not you can use a layout instead.

82
00:10:47 --> 00:10:59
Layout was introduced several years ago and is a great alternative so you're not, similar to our previous discussion, invalidating an entire view hierarchy when the layout changes and instead really customizing the layout at that point or similar tools.

83
00:11:00 --> 00:11:02
OnChange is another similar one to watch out for.

84
00:11:02 --> 00:11:08
If you're using OnChange to try to trampoline data back and forth, it's a good sign.

85
00:11:08 --> 00:11:09
Sometimes there's a correct use.

86
00:11:09 --> 00:11:15
So it's not like, you know, never use onChange, but you want to analyze are you using it in a way where there's not a better substitute.

87
00:11:15 --> 00:11:30
Yeah, I see that in workshops we do is that a lot of times people will use onChange a lot as they're moving from an imperative UI framework to a declarative one because it's sort of comfortable imperative territory to jump to an onChange.

88
00:11:30 --> 00:11:39
And so you need to think about whether you can make the view more declarative so it just responds to data instead of you writing the code that reacts to the data.

89
00:11:39 --> 00:11:43
Exactly, and like we both said, they're not wrong to use.

90
00:11:43 --> 00:11:44
There are appropriate uses.

91
00:11:44 --> 00:11:46
It's really just analyzing and making sure you're using them.

92
00:11:48 --> 00:11:50
Anybody else have a favorite anti-pattern?

93
00:11:51 --> 00:12:06
I think, so environment, every time you kind of refer or declare an out-environment property in your view, every time that an environment changes, we would re-evaluate all views that kind of read that environment.

94
00:12:07 --> 00:12:15
So I think something to watch out for is some value that's frequently changing in the environment that you're reading.

95
00:12:15 --> 00:12:18
And maybe some views don't even need to read that environment.

96
00:12:18 --> 00:12:24
And they are now suddenly getting invalidated because the environment was declared.

97
00:12:24 --> 00:12:29
But even if the body of that view doesn't read the environment, we still have to invalidate.

98
00:12:29 --> 00:12:40
So analyze usage of environment properties and see if you can remove some of them or reduce the frequency.

99
00:12:40 --> 00:12:47
That's another place where observable works really well because you can pass an observable object down through the environment.

100
00:12:48 --> 00:12:57
The object pointer itself is not changing, and so the environment itself is stable, and your view is just reading properties from it, and so it's a great use there.

101
00:12:57 --> 00:12:57
I have one.

102
00:12:57 --> 00:13:17
It's not so much an anti-pattern, I guess, but I would say going down to the level of controls or leaf views or something, I've often seen people create wrapper views around things like buttons because they have, like, customizations they want to make to a button that they want to use throughout their app.

103
00:13:17 --> 00:13:28
And so they'll make, like, you know, my custom button view that has effectively a button with a bunch of modifiers applied to it or styling the label within the button.

104
00:13:29 --> 00:13:36
And I think that's a great opportunity to explore button styles and all the other various style protocols that we have.

105
00:13:37 --> 00:13:42
If you're doing that, you can often just create a custom button style.

106
00:13:43 --> 00:13:52
And within the button style, you can actually pass the configuration right through to another button if you want to just apply some modifiers to a button.

107
00:13:52 --> 00:13:59
Or you can just use the button's configuration.label and apply whatever modifiers you want there.

108
00:13:59 --> 00:14:08
So that's a good way of creating reusable custom buttons or any other control, really, and share that across your app.

109
00:14:08 --> 00:14:16
And that way, you can use all of the button initializers that exist, which include all the convenience ones for localization and using system images.

110
00:14:17 --> 00:14:20
And you don't have to recreate those initializers.

111
00:14:20 --> 00:14:24
You don't have to have your custom view let you pass through a title.

112
00:14:24 --> 00:14:30
And then you're doing a lot of stuff that the framework can give you for free.

113
00:14:30 --> 00:14:34
And that's especially important, too, when you're creating something custom.

114
00:14:35 --> 00:14:40
Because, you know, if you apply a modifier to a button, that may be different than applying it in the label.

115
00:14:41 --> 00:14:47
If you want to add padding to a button, you want to really do that in the label or ideally do that with a custom button style.

116
00:14:48 --> 00:14:53
Cementia has something that Adi and I were talking about the other day, which is, like, how much list gives you out of the box.

117
00:14:53 --> 00:15:00
I think another similar thing is, especially with AI tooling today, it's easier than ever to build just, like, a completely custom view from scratch.

118
00:15:00 --> 00:15:06
When sometimes, like, using a list or using some other standard component is the right thing to do for how much it actually gives you.

119
00:15:06 --> 00:15:17
And, you know, as the new design has evolved this year, if you're using the built-in components, you're evolving right along with it without doing any additional work.

120
00:15:17 --> 00:15:27
Yeah, and often, like, if this has been, like, a wrapper that you wrote maybe five or six years ago, sometimes you might not even know.

121
00:15:27 --> 00:15:28
You might update your app.

122
00:15:28 --> 00:15:39
And we've found this in some workshops where even the developer, neither us from Apple nor the developer who's trying to update their app can seem to figure out why something isn't changing.

123
00:15:40 --> 00:15:46
We have to spend half an hour debugging to figure out, well, oh, is this wrapper that everyone's forgotten about?

124
00:15:46 --> 00:15:51
So if you can stick to stock components, it's definitely easier to update.

125
00:15:51 --> 00:15:57
Yeah, I think a similar thing is when it's like, oh, well, SwiftUI maybe today does not have the standard component that I really want to use.

126
00:15:58 --> 00:16:04
oh, I should just rewrite it from scratch in SwiftUI, versus, like we were talking earlier, the interoperability.

127
00:16:04 --> 00:16:09
It is so easy to bring in a UIKit or AppKit view that does what you want that it's totally recommended.

128
00:16:09 --> 00:16:10
That is not an anti-pattern.

129
00:16:10 --> 00:16:13
That is what we would recommend doing.

130
00:16:13 --> 00:16:17
One more anti-pattern is potentially using conditional modifiers.

131
00:16:17 --> 00:16:21
If you write your own modifiers and then you have an if condition, you apply one modifier.

132
00:16:21 --> 00:16:25
And otherwise, you're true and, for example, just the view as is.

133
00:16:25 --> 00:16:33
And this is, I think, acceptable if the condition never changes once the view has been initialized.

134
00:16:33 --> 00:16:40
But when the condition changes while the view is on screen, it completely recreates the view, which has all sorts of issues.

135
00:16:40 --> 00:16:47
The animations can break, state gets re-initialized, but also from a performance perspective is a concern.

136
00:16:47 --> 00:17:10
- And that's a good excuse for me to call out Renz's talk on lazy stacks that was in this year's WWDC videos, where he goes into lots of things that you can do will accidentally cause a lazy stack to update more slowly than it should and so that's one of the ones he talks about um there's some other ones in there and i'd encourage folks to take a look at

137
00:17:10 --> 00:17:32
that um thanks for that question ozzy clearly we have opinions on that one um so let's uh let's move on to um the next one i'm i think i think we'll do this just down the line um and let everyone take a crack at it so we'll start with you seema um what's one concept in swift ui that takes many Developers the longest to understand and how would you explain that?

138
00:17:34 --> 00:17:55
Yeah, I think it's a good question So the kind of the hidden part of software is the way how views update is how we kind of have like an internal representation Which is like a graph and it effectively has like edges and nodes that represent individual views and the edges are like the inputs To the view and so we use that data structure internally to you know

139
00:17:55 --> 00:18:18
Understand which parts of your hierarchy updated as a result of like a state change and I think it's important to on some level kind of be aware of that and be aware of the fact that There is there is some underlying mechanism that like dips or like effectively figures out if your view has indeed changes and if so like updates its body and

140
00:18:18 --> 00:18:38
I think as you as your app grows and as you kind of look into more like performance tooling and instruments It's it's very helpful to know about that like underlying update mechanism to then reason about what is wrong with my apps performance Why is my view updating more than I would expect it to updates are all about?

141
00:18:39 --> 00:18:53
state change not Rerendering and comparing the views like we're not diffing the Dom of an HTML hierarchy. We're actually looking at the state Yeah, so David do you have a concept that you think people trip over?

142
00:18:53 --> 00:19:03
Yeah, sometimes with foreach when you want to like filter the data, there are a lot of people write like an if in the body of the foreach.

143
00:19:04 --> 00:19:16
And that like gives you the result visually, but has a problem of then SwiftUI often to figure out the total number of elements that we often need for certain operations internally.

144
00:19:16 --> 00:19:19
We need to iterate through the whole foreach.

145
00:19:19 --> 00:19:46
right um and then this is ifs are one uh one reason why that happens but any views as well right um and in our docs we have some examples of that and also a flag you can pass to debug this issue um and the one of the simple solution is either filtering it up front and having returning constant number of views or for any view putting it in an h stack or z stack right so that makes

146
00:19:46 --> 00:19:49
that constant number of views from the SwiftUI.

147
00:19:49 --> 00:19:52
Yeah, so the for each can count without doing more interpretation.

148
00:19:52 --> 00:19:53
Exactly.

149
00:19:54 --> 00:19:58
I'm going to riff off of David's anti-pattern about conditional modifiers.

150
00:19:58 --> 00:20:06
I think one of the things that's interesting, like mental model shift, is your view's body is a description of that view in all the states it can go across.

151
00:20:06 --> 00:20:13
And so, you know, coming to SwiftUI for the first time, you're often like, well, I want to add in, I want to make the opacity be zero, but normally it's normal, right?

152
00:20:13 --> 00:20:16
Normally it's just like the default value.

153
00:20:16 --> 00:20:20
But most of our modifiers have what we call inert variance.

154
00:20:21 --> 00:20:30
So instead of thinking of it as like adding and removing an opacity modifier, it's, well, the opacity is either one, it's fully opaque, or you've changed it to zero or some other value.

155
00:20:30 --> 00:20:33
And so you can use ternaries, and opacity is just like the simplest example.

156
00:20:33 --> 00:20:35
You can kind of apply this to almost any modifier.

157
00:20:36 --> 00:20:45
You think of it as, here's my description of all the states it can be in, and the values I pass to those modifiers is what's kind of capturing that, which even helps with things like animation.

158
00:20:45 --> 00:21:11
it makes it easier to like animate between all these different states as well yeah definitely um i also say like uh if you come across anything that that doesn't have this like inert version lets you do this definitely file feedback for that feedbackassistant.apple.com i think uh so going also riffing off the like the idea that views are like a description um

159
00:21:11 --> 00:21:21
And something I often see is people referring to, like, the view body being re-evaluated as, like, re-rendering or re-drawing.

160
00:21:21 --> 00:21:23
But that's not always the case.

161
00:21:23 --> 00:21:32
It's really just, like, your body can be re-evaluated, but it doesn't necessarily mean that something needs to be re-drawn or rendered again.

162
00:21:32 --> 00:21:35
Like, SwiftUI will also handle that.

163
00:21:35 --> 00:21:41
So that's something I've just seen often.

164
00:21:41 --> 00:21:53
I think that, especially if you come from another framework, it's easy to think about it, that the body is going to all, like re-evaluating the body is always going to cause this big task to happen.

165
00:21:54 --> 00:21:58
Aditya, I know you come with a deep UI kit understanding and background.

166
00:21:59 --> 00:22:03
Is there anything about SwiftUI where the impedance difference there is?

167
00:22:03 --> 00:22:13
Yeah, I mean, for me, as someone who has been writing UI kit for more than a decade, which sounds pretty wild to say.

168
00:22:13 --> 00:22:25
The biggest change for me-- it's something I understood or know, but it's hard to internalize-- is that UIKit lays out and arranges and renders views top down.

169
00:22:25 --> 00:22:33
So we'll start at the outside, the window, and go down to the smallest child and have some loops as needed.

170
00:22:33 --> 00:22:36
Whereas SwiftUI tends to do it the other way around.

171
00:22:36 --> 00:22:41
They'll start at the innermost node and build outwards.

172
00:22:42 --> 00:22:47
And so sometimes, especially if you're working in a sandwich or a cake.

173
00:22:48 --> 00:22:49
A sandwich or a cake.

174
00:22:49 --> 00:22:53
Aditya means that you're using interop and you have layers alternating.

175
00:22:54 --> 00:23:02
It's something to try to keep in mind that there's a slight difference in approach in both frameworks.

176
00:23:03 --> 00:23:13
And so you might want to consider how your data flows and how you decide to lay out and invalidate state to take this difference into account.

177
00:23:13 --> 00:23:17
Yeah, that gets one of the things that was challenging for me when I approached it, too.

178
00:23:17 --> 00:23:22
It's like the layout system is kind of like all the subviews are asked, well, how big are you?

179
00:23:22 --> 00:23:25
And the subviews ask, well, how big do you want to be?

180
00:23:25 --> 00:23:28
Until we get down to the leaf nodes, like, oh, I want to be this big.

181
00:23:28 --> 00:23:30
And then that sort of flows back up.

182
00:23:30 --> 00:23:32
There's a really great video from Paul a few years ago.

183
00:23:33 --> 00:23:38
He discusses this, and I found that to be very helpful in understanding the layout system.

184
00:23:39 --> 00:23:43
Yeah, it's a video on custom layout, I believe, from WW23.

185
00:23:45 --> 00:23:46
Probably, you would know.

186
00:23:46 --> 00:23:53
I think I was kind of busy that year, but yeah, I think that was the year I did what's doing SwiftUI.

187
00:23:55 --> 00:23:59
So I think we'll go back to you on this one, Adi.

188
00:24:00 --> 00:24:01
This is from Tyler.

189
00:24:02 --> 00:24:09
And the question, in UIKit, the concept of cell reuse was fundamental to optimizing scrolling content.

190
00:24:10 --> 00:24:14
In SwiftUI, are lazy stacks and grids sufficient for infinitely scrolling grid layout?

191
00:24:14 --> 00:24:19
Is there guidance to drop down to hosted SwiftUI and UI collection view in these cases?

192
00:24:21 --> 00:24:24
What's the right way to think about that sort of issue?

193
00:24:24 --> 00:24:46
Yeah, I mean, I think the primary reason UIKit is using reuse was because views are allocated and stored on the heap so there's like this fixed cost that you have to pay up front no matter what you're doing with your views so like cell reuse was primarily to get around the fact that well you have to instantiate and allocate memory for each one of

194
00:24:46 --> 00:25:15
your rows or cells and then all of them come with their overhead adding them and removing from the hierarchy excuse me have their own overhead um swift ui doesn't have the same issue necessarily while he takes a drink does anyone else want to jump in on that yeah like swift ui uh has also some benefits it has some implicit prefetching so while you're scrolling in one direction we look in

195
00:25:15 --> 00:25:34
what what cells come on screen next and we start evaluating those few bodies and we have because our graph is made up of individual nodes we have the concept of partially evaluating that and so So while we have rendered the current frame, there's sometimes some time left.

196
00:25:34 --> 00:25:41
And we reuse that to continue to evaluate the graph for the next cell, and then stop right before the next frame begins.

197
00:25:43 --> 00:25:43
Nerding out.

198
00:25:43 --> 00:25:50
That is one of the coolest things about this graph model is that you can do this partial evaluation and then sort of say, hey, we're out of time for this frame.

199
00:25:50 --> 00:25:52
We can keep it up for the next one.

200
00:25:52 --> 00:25:53
It's really cool.

201
00:25:53 --> 00:26:00
And again, Renzo's talk has this great animation of how this process works.

202
00:26:00 --> 00:26:03
And this actually gets at a perf thing that I thought was really interesting.

203
00:26:03 --> 00:26:24
I learned while working with Renz on this session where if you've got an on-appear that reconfigures the state of a cell that causes it to need to be laid out again because it changes the size, you actually throw away all that pre-work that was done in prior frames.

204
00:26:24 --> 00:26:33
And so it's really important to try to get that work done in init, not in body or in onAppear so you get it done earlier.

205
00:26:34 --> 00:26:36
It's kind of similar to our discussion about onChange.

206
00:26:36 --> 00:26:42
Like, there's sometimes a good use case for onAppear, but as much as you can avoid it and have your data model handle it or otherwise, it's great.

207
00:26:42 --> 00:26:51
The reason for both of, like, onChange and onAppear is that both of them have to go into the view body, and so we have to recompute the entire view body in order to execute those closures.

208
00:26:51 --> 00:26:53
All right.

209
00:26:53 --> 00:26:54
Excellent.

210
00:26:55 --> 00:26:58
So the next question I think is super interesting.

211
00:26:58 --> 00:27:00
It's very pertinent to this year.

212
00:27:00 --> 00:27:07
The change from ViewBuilder and other builders to ContentBuilder-- looks great, thank you.

213
00:27:07 --> 00:27:08
I mean, I didn't do it.

214
00:27:08 --> 00:27:10
Thank you.

215
00:27:10 --> 00:27:15
I'm fascinated how it works now and why this improved type checking performance.

216
00:27:15 --> 00:27:18
And could you say a little bit more about how that change was thought about and created?

217
00:27:18 --> 00:27:20
Maybe Sima, you want to jump on that?

218
00:27:20 --> 00:27:22
SIMA SHAH: I can get started on that one.

219
00:27:22 --> 00:27:24
First, I wanted to call out what's new with UI.

220
00:27:24 --> 00:27:42
It has a great visualization of the type checking work that needed to be done before this change and now how it changed with Content Builder where essentially the compiler needs to consider less overloads when it's trying to type check your view body code.

221
00:27:42 --> 00:27:53
And that was causing, that type checking work was causing the notorious expression is too complex to type check error that probably most of you have encountered.

222
00:27:53 --> 00:28:17
And this year we did so much work to kind of improve the type checking story there and that involved us kind of moving away to this new content builder type where essentially it let us collapse the amount of overloads we had for each kind of custom type, like for each group and section which used to have like each, like all of their own view builder overloads.

223
00:28:17 --> 00:28:30
Now we only have one and I think it's a great improvement and hopefully a long-awaited one, 'cause I feel like even all of us here have dealt with the type checking problem, so.

224
00:28:30 --> 00:28:33
- We've even limited our API in some cases because of this.

225
00:28:33 --> 00:28:39
- Yeah, we had a ceiling and we were like, oh, we can't introduce more because it's gonna worsen up.

226
00:28:39 --> 00:28:43
- 4-H only worked in some types because we couldn't add it to any more places.

227
00:28:44 --> 00:28:54
Stephen showed a great analogy when we were working on the What's New session In the past, you could think of a view with lots of groups and sections and four each.

228
00:28:55 --> 00:29:01
It was sort of like a cave, and the type checker had to explore every path through it, and then it could find the path out.

229
00:29:02 --> 00:29:03
And we've made it a hallway.

230
00:29:04 --> 00:29:08
It's content builder all the levels down until you know that it's a view.

231
00:29:09 --> 00:29:10
I think that's a great analogy.

232
00:29:10 --> 00:29:22
And it backports all the way to the beginning of SwiftUI because it's effectively a type alias to ViewBuilder, and then ViewBuilder got a lot smarter.

233
00:29:24 --> 00:29:24
Excellent.

234
00:29:25 --> 00:29:27
That's one of my favorite things, so thanks for that question.

235
00:29:29 --> 00:29:30
Oh, this one's great.

236
00:29:31 --> 00:29:34
This is a throwback to the SwiftUI group lab last year, I think.

237
00:29:34 --> 00:29:42
SwiftUI's most loved debugging tool is self._printchanges, underscore included.

238
00:29:43 --> 00:29:50
When your teams hit a mystery re-render storm, maybe I'll start with you on this one, Jason.

239
00:29:51 --> 00:29:54
What's the real workflow?

240
00:29:54 --> 00:29:58
How do you debug why re-rendering is happening?

241
00:29:59 --> 00:30:00
Is it instrument?

242
00:30:01 --> 00:30:02
Is it underbar print changes?

243
00:30:02 --> 00:30:04
Is it some mystery?

244
00:30:05 --> 00:30:29
technique that uh that we don't share with the world yeah so i'm assuming they're asking like what what do we do internally and um it's really yeah all of all of those above two um another thing i've i've often seen happen or people do is like do um have a uh color that just is like random um so you can very easily see if your like body is reevaluating maybe on reese you try this

245
00:30:29 --> 00:30:55
on resize when something's evaluating you're not expecting that uh to happen it's also a good way visualizing i think the the differences um uh that we talked about in the first question um or the second question uh i can't remember now but um of you know having the computer property view builder versus the the separate view that's a good way to to visualize that um because anybody no because

246
00:30:55 --> 00:31:10
that means you can sort of like add these print changes or there's also underbar log changes um which uses os logging so a good tip in case you ever stumble across that um but yeah you can can sort of add it at these different layers and see, like, OK, well, it's this small little view changing.

247
00:31:10 --> 00:31:13
Sometimes you'll see, like, self changed.

248
00:31:13 --> 00:31:15
And sometimes that can feel like a mystery of what that means.

249
00:31:15 --> 00:31:19
It's really just like the value of that view has changed.

250
00:31:19 --> 00:31:24
And that often means you just need to crawl up to the next level and see why that parent view changed.

251
00:31:24 --> 00:31:28
I think this is a great reason to use, actually, the SwiftUI instrument.

252
00:31:28 --> 00:31:33
The instrument, it might seem as though it's mostly for optimizing performance.

253
00:31:33 --> 00:31:35
But it's also a really good debugging tool.

254
00:31:35 --> 00:31:42
If you can reproduce your issue and capture a trace, you can figure out exactly what inputs cause something to re-evaluate.

255
00:31:42 --> 00:31:59
So if you have a hunch that, oh, I think something here changed, but I don't know what, it's actually great because you get this giant graph of exactly what caused your body to re-evaluate, which can be sometimes a little simpler than trying to use print changes.

256
00:32:00 --> 00:32:09
And especially given the strength of agents now, you can throw a trace or a crash report also at an agent.

257
00:32:10 --> 00:32:17
And sometimes the problem is one line in 1,000 or 10,000 lines, and it's hard for a human to see that.

258
00:32:17 --> 00:32:19
We get bored reading that, right?

259
00:32:19 --> 00:32:20
But the agent doesn't get bored.

260
00:32:21 --> 00:32:23
Sometimes it even notices the one thing you overlooked.

261
00:32:24 --> 00:32:26
I've had this happen a few times when we've been investigating things.

262
00:32:26 --> 00:32:28
It's like, oh, my gosh, how did it find that one little thing?

263
00:32:28 --> 00:32:30
I often use the SwiftUI instrument.

264
00:32:30 --> 00:32:34
It also has a way to represent the view hierarchy as a tree.

265
00:32:35 --> 00:32:40
And then it can draw a flame graph that is weighted by how much time we spend in each view.

266
00:32:40 --> 00:32:44
But I also just like to look at it if I want to understand what an app does.

267
00:32:44 --> 00:32:49
I often look into our first-party apps and see where there are performance problems.

268
00:32:49 --> 00:32:54
And recently, I was looking into the TV app, and I noticed that we update sliders.

269
00:32:54 --> 00:32:57
But if I looked at the screen, there are no sliders.

270
00:32:58 --> 00:33:01
And so this was something interesting to look at.

271
00:33:01 --> 00:33:18
This is often like if you have an intuition of what you would expect from an app or look at the app and see there are things updating that are not even on screen or shouldn't update at that point in time, this is a really good indication that there's something that is worth investigating.

272
00:33:18 --> 00:33:23
Right, and either there's a bug in the app or in your understanding of it, and either things are good things to fix.

273
00:33:23 --> 00:33:23
Exactly.

274
00:33:25 --> 00:33:27
So there's a follow-up to this one.

275
00:33:27 --> 00:33:29
Why the underscore still?

276
00:33:30 --> 00:33:34
I think that's just really a signal to not ship this.

277
00:33:36 --> 00:33:38
Like, don't remove this when you're done with it.

278
00:33:38 --> 00:33:41
Don't submit to the App Store with this.

279
00:33:41 --> 00:33:45
You don't want your user's console to be filled up with all these print changes.

280
00:33:46 --> 00:33:49
And there's a little bit of performance overhead of printing these changes, right?

281
00:33:49 --> 00:33:51
Like, it's not an optimized path.

282
00:33:51 --> 00:33:52
Right.

283
00:33:52 --> 00:33:54
The string has to be generated.

284
00:33:54 --> 00:33:55
It has to be written out.

285
00:33:55 --> 00:33:56
Yeah, exactly.

286
00:33:56 --> 00:33:57
Excellent.

287
00:33:57 --> 00:33:57
Thanks.

288
00:33:58 --> 00:34:06
So I think we've talked about this next question already.

289
00:34:06 --> 00:34:18
But it's-- actually, I think I'm going to go past this one, because we opened with it, about architecture, and move on to the next one.

290
00:34:18 --> 00:34:19
Oh, this is a great question.

291
00:34:19 --> 00:34:22
And maybe I'll start with you on this one, Seema.

292
00:34:22 --> 00:34:26
So nice to see the compilation improvements with @ContentBuilder.

293
00:34:26 --> 00:34:30
Is there any reason to continue using @ViewBuilder?

294
00:34:30 --> 00:34:32
Is there a reason to choose between those?

295
00:34:32 --> 00:34:33
That's a great question.

296
00:34:33 --> 00:34:37
So there's probably some confusion around those two names.

297
00:34:37 --> 00:34:41
So ContentBuilder and ViewBuilder are actually the same.

298
00:34:41 --> 00:34:42
It's just the type alias.

299
00:34:42 --> 00:34:48
But something that ContentBuilder would allow you to is it's now possible to use it outside of your view.

300
00:34:48 --> 00:34:59
So you can have custom SwiftUI-like, if I can say, DSLs, where you can create your own building blocks that aren't necessarily SwiftUI views.

301
00:34:59 --> 00:35:25
So we're kind of opening up all of it to the outside which is interesting interesting and thought of that anybody have anything to add to that it's a good description that's a i learned something so thanks for that question i love it um all right so this uh this question um is is another architecture one but a different take on

302
00:35:25 --> 00:35:36
it so um person asked i've used swift and mvc for over a year but swift ui's property wrappers feel like opaque magic.

303
00:35:37 --> 00:35:47
So my worry is if I don't fully grasp how at-state manages life cycle under the hood, how will I safely adopt swift concurrency?

304
00:35:48 --> 00:35:51
So I think concurrency means I'm going to start with you, Seema.

305
00:35:51 --> 00:36:02
But what's the mental model for connecting at-state and state management and concurrency, and how should I think about those together?

306
00:36:02 --> 00:36:04
- That's a great question.

307
00:36:04 --> 00:36:10
So in SwiftUI, we really try to make it as simple as possible to adopt concurrency in your code.

308
00:36:10 --> 00:36:22
So views are main actors, so it means everything that's declared in your views, all of your state property wrappers will also be main actor isolated, and that makes it really easy to work with.

309
00:36:23 --> 00:36:37
The one problem that you can encounter, like an issue that also actually we did cover in last year's WT talks is some closures in SwiftUI are sendable.

310
00:36:37 --> 00:36:43
And that actually means that, as an optimization, we would execute them from off main thread.

311
00:36:43 --> 00:36:48
And we annotate those closures for that sendable to kind of signal that.

312
00:36:48 --> 00:36:56
And so you'll notice sometimes when you're trying to use your state values, you'll get some concurrency errors.

313
00:36:56 --> 00:37:10
And the reason to that is you basically want to use the copy of the value in the state because the value of the state property wrapper, or macro now, is actually conditionally sendable when the value is sendable.

314
00:37:10 --> 00:37:23
So there is a literal, not trick, but a way you can use those values is by basically declaring an explicit capture list in the closure and copying over the values.

315
00:37:23 --> 00:37:29
And that's just going to tell Swift compiler that, hey, just use the copy of the state value in this closure.

316
00:37:29 --> 00:37:32
But these closures are really rare in SwiftUI.

317
00:37:32 --> 00:37:38
Most of our closures are main actors, so you shouldn't encounter any of this normally.

318
00:37:38 --> 00:37:45
But yeah, that's the only thing I can think of, or you can run into maybe a concurrency issue with state.

319
00:37:45 --> 00:37:57
- Yeah, Daniel did a great, well, Daniel and Seema both did great talks on concurrency in SwiftUI last year, And Daniel's talk, in particular, talked about the sendable closures.

320
00:37:57 --> 00:38:05
And the one place where I thought it was interesting that those were used is for animations, where it's something that has to be done at frame rate.

321
00:38:05 --> 00:38:10
And so it takes it out of that main rendering loop, which is nice.

322
00:38:10 --> 00:38:17
All right, thanks.

323
00:38:17 --> 00:38:19
Oh, this is interesting.

324
00:38:19 --> 00:38:22
So Taylor, I'm going to start with you on this one.

325
00:38:22 --> 00:38:23
And then maybe you'll throw back to me.

326
00:38:23 --> 00:38:25
We'll see.

327
00:38:25 --> 00:38:34
But what's the recommended way for implementing navigation in an iOS app in iOS 27 if custom transitions are required?

328
00:38:34 --> 00:38:36
SwiftUI, UIKit plus SwiftUI?

329
00:38:37 --> 00:38:38
Yeah, that's a great question.

330
00:38:38 --> 00:38:43
And it really ultimately depends on the needs of your app and the user experience you're trying to deliver.

331
00:38:43 --> 00:38:54
There is, since you asked about iOS 27, there is a new API for customizing navigation transitions in SwiftUI this year, like with built-in support for crossfades and other custom ones.

332
00:38:54 --> 00:38:57
So if those fit your needs, I think iOS 27 has your back.

333
00:38:57 --> 00:39:03
If you're trying to back deploy, that's maybe where you have a different question if you need to support releases before this API was introduced.

334
00:39:04 --> 00:39:07
And we mentioned interop a few times.

335
00:39:07 --> 00:39:20
I think it is totally expected that if the experience you need is something that UIKit can deliver in the release you're trying to deliver for, you should feel free to pull that in, mix and match both ways as our layer cake example called out.

336
00:39:22 --> 00:39:24
So hopefully that answers your question, and it's kind of a case-by-case basis.

337
00:39:25 --> 00:39:26
Anybody have anything to add to that?

338
00:39:27 --> 00:39:36
This is a good question for the forms, and I believe tomorrow there's a SwiftUI Q&A happening on the forms.

339
00:39:38 --> 00:39:44
Providing recommendations for navigation, it's difficult because it's highly dependent on what your app is and what your goals are.

340
00:39:44 --> 00:39:55
So I think if you can bring a more specific question to the forms, you'll probably get an answer that's better tailored for your situation.

341
00:39:55 --> 00:40:08
I think another important thing to think about there, and this was emphasized in the State of the Union on Monday, and please watch it if you haven't, but resizability of apps is important.

342
00:40:08 --> 00:40:11
You know, iPhone apps can resize on iPad.

343
00:40:11 --> 00:40:14
In the, you know, iPhone mirroring, they can resize.

344
00:40:14 --> 00:40:30
And so as much as you can stick with one of the system components for your navigation, whether that's UIKit or AppKit or, you know, SwiftUI, try to do that because those are going to support that resizing, that adapting for you.

345
00:40:30 --> 00:40:35
You'll have a lot more work to do if you have to support it and you're trying to actually build your own custom thing.

346
00:40:35 --> 00:40:38
All right.

347
00:40:39 --> 00:40:41
The next question is about scroll views.

348
00:40:41 --> 00:40:44
So maybe we'll start with you on this one, David.

349
00:40:44 --> 00:40:49
So what's the recommended way to track a scroll view's scrolling offset?

350
00:40:49 --> 00:40:54
The goal is to show certain view elements based on how far the user has scrolled.

351
00:40:54 --> 00:40:56
DAVID EAST: Yeah.

352
00:40:56 --> 00:41:04
So we have different APIs, depending on what you exactly need, like on Geometry Change Modifier.

353
00:41:04 --> 00:41:31
We also have scroll effects that also take away a little bit of work that you need to do but also are more efficient in certain situations and generally we have like the scroll position modifier as well to see which item is currently on screen right and there's um there's a really interesting api that i can't remember the name

354
00:41:31 --> 00:41:58
of i'm afraid but renz talks about it in his talk where you can track what percent of a view is visible and this is this is super useful if you're doing analytics and you want to see okay this this ad or this impression was on screen or fully on screen or whatever, but you can apply that to like the first or last view in a scroll view. And now when that transitions to, you know,

355
00:41:58 --> 00:42:21
80% visible, use that to trigger the update instead of trying to track the content offset, because the content offset is actually estimated based on the estimated heights of the views that aren't in the viewport in the view visible range so i thought that was a really interesting application yes yeah i'd really like to emphasize like the content offset is especially when you

356
00:42:21 --> 00:42:38
combine with lazy stacks is essentially should be treated as an implementation detail and it doesn't really have any semantic meaning you should try to focus on something relative to to the views that are on screen.

357
00:42:38 --> 00:42:40
- Oh, go ahead, you were gonna jump in?

358
00:42:40 --> 00:42:41
Oh, I'm sorry.

359
00:42:42 --> 00:42:46
- The scroll position modifier, I think it takes also an ID and not an offset.

360
00:42:46 --> 00:42:46
- Right.

361
00:42:46 --> 00:42:51
- And so this allows you to also be more relative to what your data model is.

362
00:42:51 --> 00:42:53
- Yeah, and that actually reminds me of this.

363
00:42:53 --> 00:42:59
It's, I guess, sort of unrelated, but maybe tied up in the question.

364
00:42:59 --> 00:43:06
It often comes up when people are trying to create an experience where more views are paged in as you go farther.

365
00:43:06 --> 00:43:23
So instead of looking at scroll position or content offset, you can add a view at the bottom of the list that that view actually has an onAppear, which then tells your networking layer to go fetch more data and then updates the data model.

366
00:43:23 --> 00:43:31
And that's a way you can implement infinite scrolling lists by paging based on the last view becoming visible, which is kind of cool.

367
00:43:31 --> 00:43:38
All right, so this is a – I guess it's sort of an API design question.

368
00:43:39 --> 00:43:41
So maybe I'll start with you on this one, Taylor.

369
00:43:42 --> 00:43:49
The question, is there a way to build custom layouts that are lazy, similar to LazyVStack?

370
00:43:50 --> 00:43:53
That's a good question, and we were just talking about different types of lazy views.

371
00:43:54 --> 00:43:58
So today, no, there is no protocol for describing a custom lazy layout specifically.

372
00:43:59 --> 00:44:05
So please do file feedbacks like we were talking about earlier through Feedback Assistant that you'd like to see that and a little bit more detail about your use case.

373
00:44:06 --> 00:44:16
So today, this would be a great example where it may be appropriate, depending on your layout, to, you know, interoperate and bring in a UI collection view if your use case needs that.

374
00:44:16 --> 00:44:22
Or we have a suite of other just other really lazy layouts, lazy grids, lazy stacks, et cetera.

375
00:44:23 --> 00:44:27
But, you know, behind your question was also, you know, there is a protocol for non-lazy layouts.

376
00:44:28 --> 00:44:31
And so if you don't require that laziness, I do recommend checking that out.

377
00:44:31 --> 00:44:40
That's a great tool for, you know, building a custom layout that is much more optimized than trying to, say, like, arrange things with a geometry reader and offsets and things like that.

378
00:44:40 --> 00:44:43
I think the other thing to think about there is can you compose those?

379
00:44:43 --> 00:44:49
Can you use one of our lazy layouts and then a custom layout within that for a sub piece of it?

380
00:44:50 --> 00:44:56
And so you get the laziness from the built-in component and then the custom layout from your component.

381
00:44:56 --> 00:44:57
That's not always going to be possible.

382
00:44:57 --> 00:44:58
When appropriate, it's great.

383
00:44:58 --> 00:45:10
Like, I've seen some pretty cool sort of mosaic layouts built that way that look a lot more complex than a simple stack or grid, but are basically built using that technique.

384
00:45:10 --> 00:45:16
And I think that lets us do a nice transition here to another performance question.

385
00:45:16 --> 00:45:19
And we'll start with you, David.

386
00:45:19 --> 00:45:25
What's the most common mistake you see developers make in large SwiftUI apps that ends up hurting performance?

387
00:45:25 --> 00:45:35
Yeah, usually it's-- well, often it's too many invalidations that tickle down, maybe start from the top, and then calls and validations downstream.

388
00:45:35 --> 00:45:51
One is, if you have something that you put up in the environment at the very top that is read something downstream or by a lot of views, if that's updating too often, a very bad example would be to put in the time in milliseconds.

389
00:45:51 --> 00:45:53
Have we seen that?

390
00:45:53 --> 00:46:02
We have seen the scroll position, for example, being put in the environment, which also updates every frame or potentially every frame while you're scrolling.

391
00:46:02 --> 00:46:06
Not the time yet.

392
00:46:06 --> 00:46:18
Another one is if you use like a value type that you have high ARP in your view hierarchy and pass down through many views.

393
00:46:18 --> 00:46:22
And like some views maybe just really don't use it, but just forward it.

394
00:46:22 --> 00:46:26
We still need to evaluate those views to pass it to the next view, right?

395
00:46:26 --> 00:46:32
And so using observable macro helps here, because the reference stays the same.

396
00:46:32 --> 00:46:40
You just pass that down, and then only at the views that are actually accessing any properties, there we track the invalidation and update those.

397
00:46:40 --> 00:46:51
Right, yeah, and to put a little more color on that, a complex value type that is getting passed down level by level, you have to do a quality comparison on it at each level.

398
00:46:52 --> 00:46:56
And so the difference there is that the observable we just compared, that the pointer is unchanged.

399
00:46:56 --> 00:47:07
And so that's where, actually, maybe somewhat surprising to folks who have really gotten into Swift and value types is there are cases where a reference type is the right choice.

400
00:47:07 --> 00:47:09
And that's why observable is a reference type.

401
00:47:09 --> 00:47:11
FRANCESC CAMPOY: And they can also be put into environment.

402
00:47:11 --> 00:47:14
And then you can indirectly update something more frequently.

403
00:47:14 --> 00:47:19
It is technically in the environment, but the environment itself is not really changing.

404
00:47:19 --> 00:47:19
MARK MANDEL: Right, static.

405
00:47:19 --> 00:47:21
FRANCESC CAMPOY: Yeah, exactly.

406
00:47:21 --> 00:47:37
I mean, I think we talked about how much users love smooth scrolling and these tips and tricks for scroll and lists. Another thing is resizability. On Mac, iPad, and now even iPhone, you can resize your app, and that's another place where users really expect a really smooth experience.

407
00:47:38 --> 00:47:47
That's another place where we've talked about hey, try to avoid Geometry Reader because using Geometry Reader means on every single frame as you resize, we are updating that view's body.

408
00:47:48 --> 00:47:58
It's another great place where you can watch out for you want to have as few views updating as possible during resize only when it's actually really necessary and otherwise keep it completely scoped just to the layout.

409
00:47:58 --> 00:48:13
Yeah, and this is another place to bring in the thing Jason mentioned of putting a border or a background or a transparent overlay that's in color and then if you resize your scroll and it's suddenly a disco dance party, you've got a lot of invalidations going on.

410
00:48:15 --> 00:48:29
Also avoiding large viewbodies as our scope of invalidation is the viewbody and everything that the viewbody is calling. If you can split that up, especially if the viewbody or something you're accessing in the view body updates often that also really helps.

411
00:48:31 --> 00:48:36
So the next one eventually becomes a question about threads and isolation.

412
00:48:36 --> 00:48:37
So I'm going to start with you, Seema.

413
00:48:38 --> 00:49:02
So do add observable properties, and add observable is a type really, so maybe it's state properties holding add observables, automatically get allocated on the right thread, or is it recommended that we explicitly make these types at main actor if the UI is going to reference them are there some best practices on how to decide

414
00:49:02 --> 00:49:28
where the state should live yeah so as I mentioned before Swift UIs we use are at main actor and this means that currently at runtime we would call your bodies and your views on the main thread and so that means that anytime you put like an observable class in declared in state then it's going to automatically get allocated to the main thread.

415
00:49:28 --> 00:49:36
Now with approachable concurrency settings, and I think for new apps, we're now setting the isolation to main actor.

416
00:49:36 --> 00:49:42
So main actor is going to be inferred for everything, including your custom observable classes.

417
00:49:42 --> 00:49:54
And I know before that setting, it used to be the case where if you were to adopt Swift 6, you would need to manually annotate your observable classes with that main actor for them to be usable from your UI code.

418
00:49:54 --> 00:50:03
So now you don't need to do that if you set the approachable concurrency setting and also the main isolation set to main actor.

419
00:50:03 --> 00:50:11
Both of these settings make it really easier to integrate observable classes with SwiftUI views.

420
00:50:11 --> 00:50:13
- Anybody have anything to add to that?

421
00:50:13 --> 00:50:14
Seema's our concurrency expert.

422
00:50:14 --> 00:50:16
- Seema covered that great.

423
00:50:16 --> 00:50:20
- We're not gonna contradict Seema's expertise, that's for sure.

424
00:50:20 --> 00:50:43
- I would add that because your state can also be accessed on the main thread, you also usually want atomic updates and not coming down like you update part of your observable and it's already rendered while you're still running on the background thread is that i would factor out the background computation into like a separate uh function and then await the result

425
00:50:43 --> 00:51:08
and once it's done um jump back to the main thread and then update the observation that's a great point and that also goes back to like the updates to state uh in your view should be mostly synchronous, so that SwiftUI can produce better animations, and you don't experience any hitches or weirdness when your app is running.

426
00:51:08 --> 00:51:10
So yeah, I think it's a really good point.

427
00:51:10 --> 00:51:23
Yeah, because at the user experience level, it's instead of seeing like-- we've probably all seen different apps that have these glitchy things, where it's like things appear back to back to back really quickly, versus waiting a tiny bit longer and having this much more continuous, smooth experience.

428
00:51:23 --> 00:51:30
Yeah, it's amazing to me how much of those things is actually feel-based.

429
00:51:30 --> 00:51:43
And you need to test on-device with the release build at least some of the time to really feel what those things are like because that intuitive sense of quality that comes from something updating crisply is so important.

430
00:51:44 --> 00:51:49
Oh, I also wanted to mention the timeline view.

431
00:51:49 --> 00:52:05
If you need something that updates on every frame in your app, Timeline view can be a great API to adopt because it would update on every frame, and I think it's an optimized way to deliver that kind of experience.

432
00:52:05 --> 00:52:22
- And that is a great chance for me to direct folks towards Hao Qian's session this year at DubDub on advanced graphics and animation, which has the best explanation of shaders that I've personally ever seen, and the best explanation of alignment guides.

433
00:52:22 --> 00:52:29
but Haojian puts a shader inside a timeline view and gets this gorgeous flowing background on a sample podcast app.

434
00:52:29 --> 00:52:32
I could just watch that animation all day, it's so good.

435
00:52:32 --> 00:52:36
- My favorite SwiftUI feature is that you can integrate your own custom shaders.

436
00:52:36 --> 00:52:38
Like this is really rare in any other framework.

437
00:52:38 --> 00:52:40
- Yeah, yup.

438
00:52:40 --> 00:52:45
And then those shaders can, you hit one of my favorite things.

439
00:52:46 --> 00:53:01
Those shaders can be used as foreground or background styles which means you can use a shader to render the glyphs of text in your app, and then you can animate it and have the gradient flowing through the text, and it's so fun.

440
00:53:01 --> 00:53:03
- Then you throw in a text render, and then they can move around.

441
00:53:03 --> 00:53:04
- And the sizes can change.

442
00:53:04 --> 00:53:07
- You can make some pretty amazing stuff.

443
00:53:07 --> 00:53:08
So that's why I got a Fauti installation.

444
00:53:08 --> 00:53:11
- Now you get a sense of what we all do in our spare time.

445
00:53:14 --> 00:53:24
All right, so this is another kind of architecture question, but it's sort of a design system kind of architecture.

446
00:53:24 --> 00:53:28
And maybe, Jason, I'll let you take a first crack at this one.

447
00:53:28 --> 00:53:45
So the question is, in our multi-module app, so an app that's made up of distinct modules, we return any view from protocols to hide the concrete view types and avoid cross-module dependencies.

448
00:53:46 --> 00:53:51
Is there a more idiomatic way to do this without any view?

449
00:53:51 --> 00:53:58
For example, should we be using at view builder closures or any view existentials or what's the approach that we should be looking at here?

450
00:53:58 --> 00:54:07
Yeah, so definitely use some view if you can for your use case because that will handle that better.

451
00:54:08 --> 00:54:14
If that's not possible, any view is fine as long as the underlying type isn't changing.

452
00:54:14 --> 00:54:25
So just try to avoid dynamic changes to the type, which I think also falls in line with a lot of the advice that we've already heard about avoiding conditionals.

453
00:54:26 --> 00:54:31
I'm curious, have you seen any patterns of this being done well or poorly when you dug into different?

454
00:54:31 --> 00:54:37
Yeah, so one issue is that we do not know, don't have static information about this type, right?

455
00:54:37 --> 00:54:45
And so for each relies on that, for example, to get the static count of the view that you return from the body closure.

456
00:54:46 --> 00:54:54
And so for any view, because the type can actually change, we can never be sure about any static information we gather from that.

457
00:54:54 --> 00:55:05
And there, a good thing to do is put it in an HStack or a ZStack to then communicate to SwiftUI for each that it only ever returns a single view.

458
00:55:05 --> 00:55:08
It can't happen to a better performance there.

459
00:55:08 --> 00:55:14
And in our APIs, the APIs in SwiftUI, we're almost always returning some view.

460
00:55:15 --> 00:55:42
you'll see that referred to as an opaque type. But the type checker actually knows what type it is without having to expose that type to the people using that. And so that's a way to hide the type, but still give the type checker the information it needs and give our internal systems clear knowledge that it's a static type. So instead of swap, instead of... Jason said, and it's totally

461
00:55:42 --> 00:56:11
true like if you keep the value that's returned and the wrapped in the any view consistent well that that can be performant but um but if it's a sum view then we know that you're doing that like the compiler will catch you if you try to change it lots of lots of great performance questions today so i appreciate that um so taylor i think i'll start this one with with you um what are the

462
00:56:11 --> 00:56:17
performance trade-offs, speed and memory, of using a compositing group or a drawing group?

463
00:56:18 --> 00:56:20
This is some of the stuff that's been there since early on.

464
00:56:21 --> 00:56:39
Since the very beginning, yeah. And that's a good question because, yeah, there's lots of these something-something group. So compositing group is not strictly about performance. It's really about how you can apply visual effects. I think one of the things that we were talking earlier about what surprises people is if you create this kind of structure of views with like a Z stack and

465
00:56:39 --> 00:56:57
things and you apply a shadow to the overall thing. If you're used to other UI frameworks, you might think, okay, well, I'm just applying a single shadow. But actually what it's doing is it's applying a shadow to each individual visual element, which is sometimes what you want. But sometimes you do just want to say, hey, I want to take the overall visual result and apply a

466
00:56:57 --> 00:57:17
shadow to that. So compositing group is great for that. It's less about performance and more about really fine tuning the visual result you want. Versus drawing group, I remember this was, I think, one of the very first, this is the very first year SwiftUI is introduced. We had this great example where it was like this kind of pie chart-esque visual, and it was showing like how

467
00:57:17 --> 00:57:37
far you could push SwiftUI to build these custom graphics. And you could get pretty far, but at a certain point it was just like, there was just too many individual views being rendered. Not SwiftUI views, but on-screen like layer representations of the views. And so what Drawing Group does is it takes all of the like kind of rendered layers that would have been drawn and puts them onto a single

468
00:57:37 --> 00:57:59
one and so then you in that in that example you saw the performance suddenly shoot back up because even though there were the same exact number of SwiftUI views updating it was rendering to a single a single drawing layer so that's a great tool when you have that kind of setup and the cool thing is like all of the same other APIs you'd expect from SwiftUI work so gestures

469
00:57:59 --> 00:58:07
work the same way etc so it's like a great tool for these very rich interactive experiences that Otherwise, you'll have a lot of layers.

470
00:58:07 --> 00:58:12
I think it wasn't mentioned in the question, but Canvas is kind of similar in that way.

471
00:58:12 --> 00:58:14
Do you want to explain what Canvas is?

472
00:58:14 --> 00:58:16
Oh, yeah.

473
00:58:16 --> 00:58:21
Canvas is, if you have ever used UIKit or AppKit, there is a drawRect method.

474
00:58:21 --> 00:58:32
It's very similar to that, where you get more control over each individual drawing invocation and can compose them in very interesting ways.

475
00:58:32 --> 00:58:40
If you want, instead of this way that you describe the view, you want more direct control and want to invoke the functions.

476
00:58:40 --> 00:58:48
MATT SULLIVAN: And I'll take this opportunity to shout out David's DubDub talk this year, where he uses Canvas to build a very unique color picker.

477
00:58:48 --> 00:58:59
And I will call out that if you're watching that talk, look at the code snippets, because the code for drawing that is in the code snippets of the talk, even though we didn't share it in the slides.

478
00:58:59 --> 00:59:00
MATT SULLIVAN: That's cool.

479
00:59:00 --> 00:59:08
I mean, the one trade-off with Canvas is those are not SwiftUI views being drawn, and so you can't attach sub-gestures to each individual piece, for example.

480
00:59:08 --> 00:59:09
Right.

481
00:59:09 --> 00:59:16
You will also see in the code that I have one direct gesture that apply to the overall slider.

482
00:59:16 --> 00:59:23
And there are three sub-sliders, and so I need to do some math to recognize where I'm starting the drag from.

483
00:59:23 --> 00:59:27
Accessibility is kind of in a similar vein where you would need to use a proper thing.

484
00:59:27 --> 00:59:30
But that's a good plug for accessibility representation.

485
00:59:30 --> 00:59:35
One of my favorite APIs, because it's so cool, where you can say, hey, here's my overall canvas.

486
00:59:35 --> 00:59:37
But actually, it's represented by three sliders, right?

487
00:59:37 --> 00:59:42
Like, you don't need to think about how that, you know, fake accessibility hierarchy looks like.

488
00:59:42 --> 00:59:43
It is three sliders.

489
00:59:43 --> 00:59:52
And I'm afraid, despite having lots of questions that we didn't get to, and so thank you for those, that we've reached the end of this group lab.

490
00:59:52 --> 00:59:57
We do have another SwiftUI group lab at 7 p.m. Pacific time tomorrow evening.

491
00:59:58 --> 01:00:00
So we'd love to have you back for that.

492
01:00:01 --> 01:00:03
We're so thankful that you joined us today.

493
01:00:03 --> 01:00:09
A big thanks to our panelists and to all the folks working hard behind the scenes to make this happen today.

494
01:00:09 --> 01:00:16
If we didn't get to your questions, come back tomorrow evening or visit the developer forums at developer.apple.com slash forums.

495
01:00:17 --> 01:00:28
You can try out the new AI search experience at developer.apple.com to get answers about frameworks, designs, accounts, and everything related to development on our platform.

496
01:00:28 --> 01:00:34
And of course, as we mentioned, please go to feedbackassistant.apple.com to file any bugs or feature requests.

497
01:00:35 --> 01:00:41
Speaking of feedback, you'll receive an email with a survey link to let us know about your experience with WWDC.

498
01:00:41 --> 01:00:44
We'd love to incorporate your feedback into future events.

499
01:00:44 --> 01:00:46
Again, thanks for joining us.

500
01:00:46 --> 01:00:49
I hope you have a great WWDC.
