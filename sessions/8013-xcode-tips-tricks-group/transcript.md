---
title: Xcode Tips and Tricks Group Lab
source: https://developer.apple.com/videos/play/wwdc2026/8013/
session: 8013
collection: wwdc2026
duration: 01:01:35
fetched: 2026-06-12
via: whisper.cpp ggml-large-v3 (session SD video audio)
transcription: machine-generated, no speaker diarization
---

> Machine transcription of the **Xcode Tips and Tricks Group Lab** SD video via whisper.cpp (`ggml-large-v3`).
> Group labs ship no Apple transcript; this is generated from the audio. Cues are timestamped segments, **not** speaker-attributed turns.

## Transcript

1
00:00:03 --> 00:00:05
Hello, and good afternoon from Cupertino.

2
00:00:05 --> 00:00:08
Welcome to today's Xcode Tips and Tricks Group Lab.

3
00:00:08 --> 00:00:16
I'm Angelica, and I have the privilege of being the host today with some incredible panelists from the Xcode team to answer all of your questions.

4
00:00:16 --> 00:00:20
Now, we are so glad so many of you are able to join us today, so let's jump right in.

5
00:00:21 --> 00:00:25
I'd like to do a round of introductions, starting with Chris from across the table.

6
00:00:25 --> 00:00:26
Thanks. I'm Chris Miles.

7
00:00:27 --> 00:00:30
I work on live coding experiences in Xcode.

8
00:00:30 --> 00:00:50
That's things like previews and playgrounds and integrating those technologies into our agentic workflows hey i'm jake i'm a designer on the xcode team most recently involved in themes and some of the toolbar customization so very exciting times my name is john i manage several of the front-end xcode teams like the window chrome

9
00:00:50 --> 00:01:07
source editor search code completion and design tools hi my name is katsper and i work on the instruments team here at apple my job is creating profiling tools so you can all optimize and make make your apps better.

10
00:01:07 --> 00:01:08
Hello, I'm Chris.

11
00:01:08 --> 00:01:14
I work on the organizer window for crash reporting and uploading to the App Store.

12
00:01:14 --> 00:01:18
And I work on some of the signing UI in Xcode.

13
00:01:18 --> 00:01:19
Great.

14
00:01:19 --> 00:01:24
So our first question from developers was actually also going to be my question for you all.

15
00:01:24 --> 00:01:25
Thanks for the question, Pichaya.

16
00:01:25 --> 00:01:28
Nice to have you again at a group lab.

17
00:01:28 --> 00:01:56
But from Chris to Chris, I would like ask each of you, what are the most useful Xcode tips and tricks that most people may miss out on or maybe are lesser known, in your opinion? I know I wanted to call this out especially because a little known fact, Xcode is built with Xcode. You all not only built features for Xcode, but use it on a daily basis. So what would you say is your

18
00:01:56 --> 00:01:59
number one lesser-known feature in Xcode?

19
00:02:00 --> 00:02:10
The tip I'll pick is a feature we added to last year's release, which is the ability to put a playground into any Swift file in your project.

20
00:02:10 --> 00:02:17
We added a playground macro that lets you just run code snippets in any of your Swift files.

21
00:02:17 --> 00:02:23
So you can simply import playgrounds and then use the hash playground macro to define a snippet of code.

22
00:02:23 --> 00:02:34
And then you can just run that code, and like any playground, You'll get the expression results shown for you on the side, and then you can just use that to explore Swift API and that kind of thing, which is pretty handy.

23
00:02:34 --> 00:02:43
- Yeah, and it's super easy to make those now where you can use the new project command and you can get a new Swift file with a Playground macro right in it.

24
00:02:43 --> 00:02:44
It's awesome.

25
00:02:44 --> 00:02:45
- Yeah, that's right.

26
00:02:45 --> 00:02:54
Yeah, basically file new Playground will give you that modern form of just a single Swift file with a Playground in it, and you can just use that to experiment and explore Swift code.

27
00:02:54 --> 00:02:56
- The dream.

28
00:02:56 --> 00:03:01
Not just that, but also, like, models that you want to test, because you could actually use Playgrounds to test out models.

29
00:03:01 --> 00:03:11
Yeah, you can play with foundation models in those Swift files, and you can also put UI code and preview that all in just a standalone Swift file, so that's made that really easy.

30
00:03:12 --> 00:03:13
That's awesome.

31
00:03:13 --> 00:03:24
My tip is kind of cheating because it's new this year, so I don't know how well-known it is, but with the new themes in Xcode, you can have different themes chosen for different workspaces.

32
00:03:24 --> 00:03:41
So if you have two projects, you can have one, you know, bright and red and one you know blue and now it's super easy to tell them apart or you know based on your mood maybe you want to swap them out uh it can be a lot of fun uh one of my favorites is when i'm writing some new code i'll typically like i'll write several types all in one file

33
00:03:41 --> 00:03:58
maybe some protocols some enum some structs all together and then i gotta split them up and i could go through the new file dialogue making new files and putting them in over and over again but uh kind of hidden feature in xcode is you just cut the text that you want to move to a new file and put your keyboard focus in the file navigator and hit paste it'll make a new file with the

34
00:03:58 --> 00:04:16
content of your pasteboard and it'll guess the file name based on what you're pasting so if you're pasting instruct person you're going to get person.swift and it's a real time saver and lets you kind of organize when you're done sort of making everything work together use it all the time that's a great one i've been doing a lot of refactoring recently with xcode and rewriting out

35
00:04:16 --> 00:04:31
of our objective c code to swift so for me find call hierarchy was really a helper so being able to quickly understand where my function is being called from, what are the dependencies, not only at one level, but going even deeper.

36
00:04:31 --> 00:04:34
So that's really handy, and it was super helpful.

37
00:04:35 --> 00:04:36
I have two, if that's all right.

38
00:04:37 --> 00:04:49
When I'm looking at a file in Xcode, I'm constantly using a Command-Shift-J to navigate, to open the navigator and see where that file is in the source code, in the folder.

39
00:04:50 --> 00:05:00
And the other one that I use, developing Xcode on the Mac, What we're constantly doing is we want to differentiate the debug Xcode from the Xcode that we're using.

40
00:05:00 --> 00:05:06
So I'll use the debug bar, and you can change the appearance of the debug application.

41
00:05:07 --> 00:05:10
So I'll change it to dark mode so I can see the difference between them.

42
00:05:11 --> 00:05:12
That's really cool.

43
00:05:12 --> 00:05:14
I wanted to add to Command-Shift-J.

44
00:05:14 --> 00:05:22
If you use open quickly, search for a function instead of a file, and then use Command-Shift-J, you could find it exactly where it is in your project.

45
00:05:22 --> 00:05:23
It's like my favorite combo.

46
00:05:23 --> 00:05:25
All right.

47
00:05:25 --> 00:05:35
Another question from a developer, Phanteks, is asking which recent Xcode feature do you think deserves more attention?

48
00:05:35 --> 00:05:37
- Oh, I've got an answer for this one.

49
00:05:37 --> 00:05:43
When you're, a challenge with using Xcode on a large team is project file conflicts.

50
00:05:43 --> 00:05:45
You've probably all experienced this.

51
00:05:45 --> 00:05:50
Like, I make a change, my colleague makes a change, we conflict, and it doesn't even feel like we're working on the same thing.

52
00:05:50 --> 00:05:58
We added a feature a couple years ago, maybe in Xcode 16, might have been 15, I'm not sure, where we have folders instead of groups.

53
00:05:58 --> 00:06:00
You can see the difference in them in the navigator.

54
00:06:00 --> 00:06:04
The gray ones are the old groups, and the blue ones are the new folders.

55
00:06:04 --> 00:06:20
When you add a folder instead of a group to represent all your source code, Xcode doesn't record the list of files, which means you'll find that if you convert your whole project to use these new folders, and you start adding new files to your project, you generally get zero diffs in the project file, and it can just drastically cut down

56
00:06:20 --> 00:06:22
on the conflicts that you have for your teams.

57
00:06:22 --> 00:06:28
I can't recall one conflict that I've had working on Xcode, since we've adopted this feature in the project format.

58
00:06:28 --> 00:06:32
And there's tons of us working on the project with tons of projects.

59
00:06:32 --> 00:06:34
So it's a real time saver.

60
00:06:34 --> 00:06:36
Yeah, we've adopted that on the instruments team as well.

61
00:06:36 --> 00:06:42
And I feel like it also was an opportunity for us to rethink about the grouping and the folders over the years.

62
00:06:42 --> 00:06:47
We've had some mess in there, so it allowed us to just clean that stuff up as well.

63
00:06:47 --> 00:06:57
I have an oldie but a goodie, is that in the schemes up at the top of Xcode, you can edit the scheme, and you can set different user defaults.

64
00:06:57 --> 00:07:02
And it's very common that we're trying out new features under a user default.

65
00:07:02 --> 00:07:07
And you can click and unclick them there, and I'm constantly doing that.

66
00:07:07 --> 00:07:09
- That's awesome.

67
00:07:09 --> 00:07:25
One of my favorite things, and this might not be like a hidden feature or something, but I just love being able to filter in the console down to different components or categories, and it makes it so much cleaner to debug something if you actually take the time to organize, which you can then just use an agent to organize it for you.

68
00:07:25 --> 00:07:28
But yeah, it's wonderful to get your logs sorted nicely.

69
00:07:28 --> 00:07:42
- A variant of that with the filtering that's less discoverable, it's super useful, is in many of the menus in Xcode, especially the ones along the jump bar at the top of the editor, it just looks like a normal menu, but if you start typing, it'll filter the menu with the code completion, open quickly, style text matching,

70
00:07:42 --> 00:07:46
so you can get to a function definition really fast that way.

71
00:07:46 --> 00:07:58
- One that's useful to know when you're previewing your UI is you can show variants, like show me all the light mode, dark mode, or different dynamic type sizes.

72
00:07:58 --> 00:08:03
But new this year, you can pass in arguments to your previews.

73
00:08:03 --> 00:08:07
So maybe you've got an enum with various types.

74
00:08:07 --> 00:08:16
You could pass in enum.allValues, and then your single preview can then take that argument and just give you a grid of the preview rendered with all the ones, which is really good.

75
00:08:16 --> 00:08:19
FRANCESC CAMPOY: I have one more that is performance related.

76
00:08:19 --> 00:08:28
So quite often, you have memory of your application is exceeding your expectations, then you will open Memory Graph Debugger to search for leaks.

77
00:08:28 --> 00:08:32
But sometimes Memory Graph Debugger is not going to give you information about the footprint of your app.

78
00:08:32 --> 00:08:35
So you can actually open this Memgraph right in Instruments.

79
00:08:35 --> 00:08:42
There's a little Share button that maybe is a bit hidden, but developers can open that up and see the timeline of their allocations.

80
00:08:42 --> 00:09:01
Yeah, there's a great session on memory analysis that actually uses that feature, where you're actually able to export the mem graph to instruments. I wanted to call out, I know everyone loves print debugging, but conditions and breakpoints is one of my favorite things that I like to call out.

81
00:09:01 --> 00:09:23
Breakpoints are already pretty incredible, but if you just secondary click on it, there's a whole modal that actually pops up where you could set conditions, number of retries, or number of repetitions, things like that. Really cool. It makes them so you can just leave a breakpoint there and it'll trigger when you actually need it to trigger all right moving on to a highly

82
00:09:23 --> 00:09:48
upvoted uh comment not a question um let's look at this one from franklin biaru not a question just a thank you to the xcode team for adding the delete derived data menu item in xcode 27 many of us have spent years manually deleting folders running terminal commands or maintaining custom scripts just to do this thank you for making every day xcode workflows a little better

83
00:09:48 --> 00:10:09
i wanted to give that to you john because it's for you uh but definitely uh your feedback has been incredible and instrumental in all of this so please continue sending us feedback if you need to use this but any any comments or behind the scenes thought process behind delete drive data other than feedback we've gotten i mean there's lots of reasons why you

84
00:10:09 --> 00:10:30
might do it like you could be running out of disk space sometimes it's faster than cleaning you know you just want to be sure and start start over other times it's masking problems in your project you know like it's easy to have a sort of hidden dependency between two targets where depending on the way the parallel build runs the build succeeds or fails and so sometimes you should reach for

85
00:10:30 --> 00:10:52
that to just sort of like start over but maybe if you see the same pattern of failures try analyzing your project and see if there's a missing dependency and then you won't have to do it so much. Great. All right. So next question we have is from Formidable Studio. The question is, I want to try out the Xcode 27 beta on my MacBook,

86
00:10:53 --> 00:11:14
but I also want to be able to use Xcode 26 to push out my apps. Can I do this or will it break my setup? So you can definitely do this. The main thing you'll want to be concerned with is just making sure you stick to the features from the public SDK that we already shipped so that your app, you know, still builds with that old version of Xcode. Sometimes we add new features to Xcode

87
00:11:14 --> 00:11:34
itself that require, you know, the new version of Xcode. Sometimes they're pervasive, like the blue folder I mentioned earlier. This year, we only added one, and you'd really have to go out of your way to use it. There's a checkbox in the inspector to, like, opt into some stricter project validation and loading. You're not going to check this by accident, so feel confident using the new

88
00:11:34 --> 00:11:47
Xcode to explore it while you, you know, maintain compatibility with the shipping version. Yeah, and I I think specifically, you could use both Xcode 26 and 27 if you're using macOS Tahoe.

89
00:11:49 --> 00:11:53
Xcode 26, I believe, does not run on macOS GoldenGate.

90
00:11:53 --> 00:11:53
That is true.

91
00:11:54 --> 00:11:56
Yeah, so you could use both if you're using Tahoe.

92
00:11:58 --> 00:12:07
And I believe you do have to worry a little bit about, especially if you're building or shipping to the App Store, making sure Xcode Select is pointing in the right place and things like that.

93
00:12:08 --> 00:12:10
Also a great opportunity to use Xcode Cloud.

94
00:12:10 --> 00:12:10
Yes.

95
00:12:10 --> 00:12:12
Because then you're not the one building it.

96
00:12:12 --> 00:12:12
Exactly.

97
00:12:13 --> 00:12:21
If you want to test the compatibility or at least what the build status would be for your app on Xcode 27, try out Xcode Cloud.

98
00:12:22 --> 00:12:23
25 free hours.

99
00:12:23 --> 00:12:25
Plenty for you to be able to do that.

100
00:12:26 --> 00:12:27
All right.

101
00:12:28 --> 00:12:31
Let's take a look at the next question from Phanteks.

102
00:12:32 --> 00:12:38
What's the most underrated Xcode feature that even experienced developers don't use enough?

103
00:12:39 --> 00:12:40
Could be for anyone on the panel.

104
00:12:40 --> 00:12:46
I mean, I'm going to plug the blue folders thing again.

105
00:12:47 --> 00:12:50
I can't stress enough how much this will reduce your project conflicts.

106
00:12:50 --> 00:12:55
And it's true that we have a lot of projects that don't use it because you have to do like a conversion step to do it.

107
00:12:55 --> 00:12:58
But it is like really worth the payoff at the end.

108
00:12:58 --> 00:13:01
So I'd encourage you to go through that and convert your groups to folders.

109
00:13:03 --> 00:13:06
I wouldn't say it's like the most underrated one ever.

110
00:13:06 --> 00:13:10
But in the canvas, let's say you have two different Swift files in your project.

111
00:13:10 --> 00:13:13
You have one for your data and one for your Swift UI views.

112
00:13:14 --> 00:13:26
You may notice that when you go to a file that doesn't have a SwiftUI preview at the bottom of it, your canvas may go away if there's no content to display, but you can always pin the tabs inside of the canvas.

113
00:13:27 --> 00:13:31
And new this year, you pin individual tabs as opposed to the entire file.

114
00:13:31 --> 00:13:43
So you could, for example, open up your SwiftUI view file, pin the tab for your content view or whatever your preview is, and you can just start navigating around knowing that your preview will be maintained, and it's a great way to work.

115
00:13:43 --> 00:13:46
And you can start playing around with data and other files, and it'll keep updating.

116
00:13:48 --> 00:13:53
I mean, I think there are all kinds of tricks that I'm always finding out, even if I'm experienced.

117
00:13:53 --> 00:13:56
And I think, you know, Chris has given a great presentation.

118
00:13:56 --> 00:14:00
There's so much that you can do while the program is still paused.

119
00:14:00 --> 00:14:02
You're mentioning conditional breakpoints.

120
00:14:02 --> 00:14:07
And, you know, if you want to maintain that state, there's so much you can do.

121
00:14:07 --> 00:14:15
I mean, I've used this forever, but I've talked about breakpoints with you can have them signal a sound and not actually pause.

122
00:14:15 --> 00:14:16
That's always a good one.

123
00:14:17 --> 00:14:24
But, you know, I think like Xcode Cloud is an example of people miss that that's really something that's very easy to set up.

124
00:14:24 --> 00:14:26
And, you know, you don't even need to learn it very much.

125
00:14:26 --> 00:14:28
It just will work on its own.

126
00:14:28 --> 00:14:34
Yeah, there's a lot of updates actually in Xcode Cloud this year, especially that people, it's like a really great time to try it out.

127
00:14:35 --> 00:14:40
The onboarding was improved as well as some build performance, things like that.

128
00:14:40 --> 00:14:42
Yeah, it's even easier to set it up right inside of Xcode.

129
00:14:42 --> 00:14:44
You don't even have to go to App Store Connect.

130
00:14:45 --> 00:14:50
- Yeah, it includes things like webhook configuration is also now built into Xcode and everything too.

131
00:14:50 --> 00:14:54
So it can make your workflows pretty powerful.

132
00:14:54 --> 00:14:57
All right, any other underrated features people can think of?

133
00:14:57 --> 00:15:00
- I wanna call out, Xcode has really powerful search too.

134
00:15:00 --> 00:15:10
When you're really looking for something across a large project, you can open the search navigator and by default you're just searching for keywords and things, partial word searches.

135
00:15:10 --> 00:15:13
But you can search for regular expressions, you can search I think by symbol.

136
00:15:13 --> 00:15:15
John knows this better than I do.

137
00:15:15 --> 00:15:18
- Yeah, you can do the multi-word search for like words that are in proximity.

138
00:15:18 --> 00:15:29
And then, actually, so for underused, I would say one of the coolest things you can do with this is to combine the search field with the filter field at the bottom so you can get kind of like a Boolean search.

139
00:15:29 --> 00:15:36
And then, little known fact, the results in the Find Navigator, when they're highlighted, you can press delete and they're removed from the search set.

140
00:15:36 --> 00:15:44
So you can sort of like narrow your scope to the things you have to work on, or like if you're gonna rename or change something, you can work your way through the list, deleting them one at a time until they're all gone.

141
00:15:45 --> 00:15:49
And last tip related to that, sometimes we'll do a query, and there's a bunch of matches.

142
00:15:50 --> 00:15:54
And I want to kind of get an overview of how much, like, where are these?

143
00:15:54 --> 00:15:55
And you want to see all the file names.

144
00:15:56 --> 00:16:06
In all of Xcode's navigators, if you hold the command key and then click on the disclosure triangle, it'll collapse all the siblings so that you can see sort of all the file names at once.

145
00:16:06 --> 00:16:08
And then I'm going to add one more fine feature.

146
00:16:08 --> 00:16:09
There's so many of them.

147
00:16:10 --> 00:16:13
You mentioned find call hierarchy earlier.

148
00:16:13 --> 00:16:24
We also added Queries for showing a type hierarchy both up to the root and down to the leaves So we are considering some favorite factor and you want to know how many things conform to this protocol or how many subtypes are there?

149
00:16:24 --> 00:16:48
You can bring them all up in a tree and then work your way through it Yeah, I'm probably a bit biased here, but I would say all of the profiling tools that we So from from the organizer that allows you to see how your data is behaving with how your app is behaving with your users To instruments and profiling at desk. I think I'd like I I really love profiling and I think that there's something great about making your up better

150
00:16:48 --> 00:17:11
It's a pretty addictive process actually. So yeah, I recommend people to try it out and improve their apps I was gonna say especially in instruments flame graph combination with top functions Makes things so much easier to find when you're profiling something. So if you haven't tried it yet This is a really great time to start. Yeah, and there's a great session this year profile fixer if I that focuses on explaining these concepts

151
00:17:11 --> 00:17:13
So I recommend everyone to check it out.

152
00:17:13 --> 00:17:15
And we have a SwiftUI instrument, don't we?

153
00:17:15 --> 00:17:15
Yeah.

154
00:17:16 --> 00:17:18
It's great for your hierarchy performance.

155
00:17:18 --> 00:17:19
That's correct.

156
00:17:19 --> 00:17:24
So, yeah, if you're experiencing problems with your SwiftUI app specifically, there's a dedicated template.

157
00:17:26 --> 00:17:27
Same thing with concurrency.

158
00:17:27 --> 00:17:31
Same thing with agentic foundation models.

159
00:17:31 --> 00:17:32
There's templates for everything.

160
00:17:33 --> 00:17:35
I wanted to call out the SwiftUI instrument.

161
00:17:35 --> 00:17:37
There was a session actually last year for it.

162
00:17:37 --> 00:17:39
Definitely take a watch of that one.

163
00:17:40 --> 00:17:45
But yeah, I like to call that out, too, because instruments in general use it.

164
00:17:45 --> 00:17:52
But you could also-- you start with a template, and then if you want to include other instruments in there, you can.

165
00:17:52 --> 00:17:59
And you could actually save those templates for your own use case if that is the way you want to work for every time you want to profile your app.

166
00:17:59 --> 00:18:00
All right.

167
00:18:00 --> 00:18:03
Really great underrated Xcode features.

168
00:18:03 --> 00:18:08
Let's take a look at the next question from mblackmon.

169
00:18:08 --> 00:18:10
Great username, also.

170
00:18:10 --> 00:18:17
What caveats should we be aware of when running Xcode 27 beta on the same machine with Xcode 26?

171
00:18:17 --> 00:18:22
We talked a little bit about this, but is there any caveats or things to be aware of?

172
00:18:23 --> 00:18:25
I think you're set.

173
00:18:25 --> 00:18:26
Go for it.

174
00:18:26 --> 00:18:31
We use lots of versions of Xcode every day, lots of versions of macOS.

175
00:18:31 --> 00:18:32
So we're prepared for this.

176
00:18:32 --> 00:18:35
Go ahead and use two versions of Xcode at the same time.

177
00:18:35 --> 00:18:36
Totally supported.

178
00:18:36 --> 00:18:40
One might look really colorful and one might look really normal or plain.

179
00:18:41 --> 00:18:44
So that might be the only major cause for concern.

180
00:18:45 --> 00:18:50
I believe, I think there's a couple of notes on like project file incompatibility.

181
00:18:50 --> 00:18:54
Is there anything around there between Xcode 26 and 27?

182
00:18:55 --> 00:18:57
Some years, but not really this year.

183
00:18:57 --> 00:18:57
Okay, great.

184
00:18:58 --> 00:18:59
Awesome.

185
00:18:59 --> 00:19:01
And let's see.

186
00:19:01 --> 00:19:05
Let's move on to the next question then to Scott G.

187
00:19:05 --> 00:19:12
There can sometimes be painful merge conflicts with .xcode proj files.

188
00:19:13 --> 00:19:19
which some developers address by generating their own project files with Tuist or Xcode Gen.

189
00:19:20 --> 00:19:24
Are there any tips and tricks to minimize these merge conflicts without the use of such third-party tools?

190
00:19:25 --> 00:19:25
John.

191
00:19:27 --> 00:19:31
Well, I'll expand on using the blue folders instead of the gray groups.

192
00:19:32 --> 00:19:35
That's going to cut your project size probably in a third if you adopt it.

193
00:19:35 --> 00:19:37
Some tips on using it most successfully.

194
00:19:38 --> 00:19:41
The blue folders are members of targets.

195
00:19:41 --> 00:19:42
That wasn't the case with groups.

196
00:19:42 --> 00:19:50
and you want the targets of the blue folders to really align with the source code inside of it because any deviation from that is going to get recorded in the project.

197
00:19:50 --> 00:19:58
So if you've got a framework with 150 source files and they're all together in the same target, none of it will be recorded.

198
00:19:58 --> 00:20:01
If there's a couple of test files in there for a test target, those will be listed in the project file.

199
00:20:02 --> 00:20:04
So get them in alignment as much as you can.

200
00:20:04 --> 00:20:12
The first time you adopt this, you're going to get a huge diff because two-thirds of your project is going to go away, which is why you're not getting conflicts anymore.

201
00:20:12 --> 00:20:17
Getting that first integration step, if you're on a big team, can be hard because your colleagues are going to keep changing the project file as you work.

202
00:20:18 --> 00:20:26
The workflow for moving to the folders is to click on one of the groups in the navigator, right-click, and choose Convert to Folder.

203
00:20:27 --> 00:20:30
If you hold Option, it'll just pre-flight the operation.

204
00:20:31 --> 00:20:35
It'll tell you that in the menu, and it'll tell you which things need to be resolved before you can complete the migration.

205
00:20:36 --> 00:20:40
I'd encourage you to resolve all the errors without migrating anything.

206
00:20:41 --> 00:20:41
Commit that.

207
00:20:42 --> 00:20:49
Then it's really easy to finish off that migration at any time where you're not going to conflict with all your peers who are still working on your project.

208
00:20:51 --> 00:20:52
One other.

209
00:20:53 --> 00:21:05
In the project inspector, if you happen to work on many Xcode projects together, which some of us do, Xcode is composed of many Xcode projects, there's a checkbox in there which is set for all projects created in the last several years.

210
00:21:06 --> 00:21:07
But for older ones, it won't be.

211
00:21:07 --> 00:21:10
It says minimize maybe cross-project references or something.

212
00:21:11 --> 00:21:11
Check that box.

213
00:21:11 --> 00:21:12
It's basically free.

214
00:21:12 --> 00:21:18
And it's also, if you're in this scenario of using many projects, it's going to delete thousands of lines from your project files.

215
00:21:18 --> 00:21:21
So another easy way to avoid some conflicts.

216
00:21:21 --> 00:21:24
- I will need to make sure that we have that checked.

217
00:21:24 --> 00:21:27
- I think we did it for you.

218
00:21:27 --> 00:21:29
- What about build settings and things like that?

219
00:21:29 --> 00:21:32
- You could move-- - Yeah, that's a useful one.

220
00:21:32 --> 00:21:41
You can put your build settings in files called xe-config-files if you'd like to just manage them outside in their own text file.

221
00:21:41 --> 00:21:48
You can put some build settings in there and then still use build settings in the editor to inherit those settings and override them.

222
00:21:49 --> 00:21:51
Or you can move all your build settings into XDConfig files.

223
00:21:52 --> 00:21:55
And if you're not sure what the build setting name is, like, well, what do I call it?

224
00:21:56 --> 00:22:08
A tip is to, when you're looking at the build settings editor, open the inspector, select the help inspector, and just select one of the build setting lines, and you'll see the name of the build setting in the help inspector.

225
00:22:09 --> 00:22:09
I'll use that all the time.

226
00:22:10 --> 00:22:15
The source editing experience for working with XC config files has been tuned up a bit this year as well.

227
00:22:15 --> 00:22:16
So it can be a nicer experience.

228
00:22:16 --> 00:22:17
Yeah, there's some syntax covering there.

229
00:22:18 --> 00:22:21
And then you can just copy the build setting row as well to get the key name and paste it into the config file.

230
00:22:21 --> 00:22:22
Yeah, that's right.

231
00:22:22 --> 00:22:25
And those config files have a great sort of audit trail, and they can have comments.

232
00:22:26 --> 00:22:28
Really easy to review and appear as well.

233
00:22:28 --> 00:22:30
Adding comments for them is one of the big bonuses.

234
00:22:32 --> 00:22:32
Awesome.

235
00:22:33 --> 00:22:33
Great.

236
00:22:33 --> 00:22:36
Hopefully that covers your question, Scott.

237
00:22:36 --> 00:22:40
We're going to move on to a new question from another developer, Newbie.

238
00:22:41 --> 00:22:44
On Xcode 27, Markdown is fantastic.

239
00:22:44 --> 00:22:47
I didn't see the WWDC sessions mentioning them.

240
00:22:47 --> 00:22:49
Are there any cool tricks with Markdown?

241
00:22:49 --> 00:22:56
And are there any improvements-- this is the second question-- are there any improvements on source control side of things in Xcode?

242
00:22:56 --> 00:23:02
I wish multi-select was possible on stashes to delete at once.

243
00:23:02 --> 00:23:07
So first question first, let's start with any cool tricks with Markdown.

244
00:23:07 --> 00:23:10
Well, you can view Markdown files.

245
00:23:10 --> 00:23:12
I mean, we have a good renderer now.

246
00:23:12 --> 00:23:15
And you can view them as rendered, nicely formatted.

247
00:23:15 --> 00:23:16
or you can view them in their original form.

248
00:23:16 --> 00:23:26
If you use open as and just select source code instead of rendered, then you'll see Markdown in its textual form, and that can be useful to move back and forth.

249
00:23:26 --> 00:23:30
But in both forms, you can edit that Markdown just straight in the editor, which is really cool.

250
00:23:30 --> 00:23:44
- Yeah, and not just as a standalone file, but when you're working with an agent and you're creating a plan or some other Markdown file inside of the transcript area or the editor, you can just work on the Markdown as if you were working on it as a standalone file.

251
00:23:44 --> 00:23:48
it's just the same experience in both places, which is really amazing.

252
00:23:48 --> 00:23:56
- Yeah, and you could do it side to side, side by side as well as like the rendered markdown as on the canvas on the right side as well.

253
00:23:56 --> 00:23:57
- Yeah, that's right.

254
00:23:57 --> 00:24:03
Just use the canvas preview button to open that and yeah, you can see it side by side.

255
00:24:03 --> 00:24:10
- All right, so following up on the second question that Newby had, which is are there any improvements on the source control side of things in Xcode?

256
00:24:10 --> 00:24:14
- We'll definitely take a comment for the multi-select on the stash.

257
00:24:14 --> 00:24:38
be bringing that back to the team when we leave um uh we've done a bunch of infrastructure work this year i mean the the we've actually completely redid the navigator that's there you can't tell because it's exactly the same it just works better um so plug for the engineer that did that thank you um uh i'm not recalling a specific you know version control enhancement other than uh us

258
00:24:38 --> 00:24:56
continuing to sort of make the the workflow that manages uh cloning packages and cloning exo projects all be consistent with all their authentication and everything else so we're improving that every year yeah there's there's good performance improvements yeah oh yeah especially if you have lots of tags in your uh your repository we have millions of tags

259
00:24:56 --> 00:25:15
and the performance of millions of tags is a lot better this year um there's there's one more uh little kind of like bonus improvement which is uh we're finally using red and green as our diff colors so that's important to you um but kind of like a i guess this is even more secret uh tip is if you want to customize your theme colors,

260
00:25:15 --> 00:25:18
we actually allow you to customize those red and green colors.

261
00:25:18 --> 00:25:22
So if you want to go pick something slightly different or you love the purple and orange, feel free.

262
00:25:24 --> 00:25:24
Awesome.

263
00:25:25 --> 00:25:33
And of course, if you have feedback, particularly around source control updates, like John mentioned, definitely file those.

264
00:25:33 --> 00:25:39
We do read them and it actually helps us keep track of suggestions, use cases that developers have for these features.

265
00:25:39 --> 00:25:42
So please file it in feedbackassistant.apple.com.

266
00:25:42 --> 00:26:10
All right. Moving on to the next question from Tammy Santana. When a Swift package manager package like MLX Swift LM updates its macros, Xcode requires manual re-approval every time, breaking CI builds and blocking compilation. Can macro trust be pinned to a package identity instead of requiring re-approval on every update? This is interesting because this is a particular

267
00:26:10 --> 00:26:33
use case. And I think I've heard a few reports of this, but do you all have any insights on how this can be done better? I think we'd want to take that back to the package. Yeah, I was going to say, we don't have experts on package managers specifically here. So this might be good for us to be able to send over to our colleagues. But the things that I do know is that I'm surprised

268
00:26:33 --> 00:27:02
that it's invalidating trust on every update. So make sure you submit something in feedback. Let us know exactly what what's happening in your use case or like what package you're using that'll help us kind of track this down figure out what's going on all right moving on to a highly upvoted question from c gontijo i hope i pronounced that correctly is there a way to open the terminal

269
00:27:02 --> 00:27:29
on the project root folder in xcode it's a great idea not directly right yeah like yeah you could open terminal and then maybe like drag one of the file references from the navigator into terminal to change that directory in a more convenient way a related feature not exactly what you're asking for but useful once you get to the terminal is the file inspector now lists the we call it the

270
00:27:29 --> 00:27:40
working copy relative path so that you can just double click on the navigator or in the inspector get all the text for the file path and then do your get operations or whatever it is that you wanted to do in there.

271
00:27:40 --> 00:27:41
- Yeah, that's really useful.

272
00:27:41 --> 00:27:53
- Yeah, I do think this is a great feedback to file, especially 'cause we have things like right-click, Navigator, Show in Finder, if you wanted that functionality in Terminal, definitely possible.

273
00:27:53 --> 00:27:55
Great, awesome.

274
00:27:55 --> 00:28:00
All right, moving on to, ooh, this is a great question, from Chrisya.

275
00:28:00 --> 00:28:05
What are the most useful Xcode keyboard shortcuts you use daily?

276
00:28:05 --> 00:28:07
This is gonna, how much time do we have?

277
00:28:07 --> 00:28:09
I feel like we could go on and on with this one.

278
00:28:09 --> 00:28:10
- I'm sure we've all got some.

279
00:28:10 --> 00:28:12
We already talked about Shift + Command + J, right?

280
00:28:12 --> 00:28:19
to show in the project navigator the location of the current file, the reference to the current file that you're working on.

281
00:28:19 --> 00:28:24
We've got shift command Y is show and hide the debug area, which I use all the time as I'm jumping in and out.

282
00:28:25 --> 00:28:31
Another one that I use a lot is run this test over again.

283
00:28:31 --> 00:28:39
And I remember that because it's all three modifiers in the bottom row, control, option, command, and G.

284
00:28:40 --> 00:28:43
Don't try and remember which ones, just all three of those and G.

285
00:28:43 --> 00:28:48
I was like, I feel like my hands have remembered keyboard shortcuts and I don't know which modifiers they actually are.

286
00:28:48 --> 00:28:49
Yeah, absolutely.

287
00:28:49 --> 00:28:53
One of my favorites is simply to refresh previews.

288
00:28:53 --> 00:28:58
So previews can pause when you're making larger changes and there's a button to hit or get them to continue.

289
00:28:58 --> 00:29:04
But if you do command option P, everyone has to do this with their hands to remember exactly how this is done.

290
00:29:04 --> 00:29:06
But command option P should refresh the preview automatically.

291
00:29:06 --> 00:29:09
And it's really nice to quickly and easily get it back up and running.

292
00:29:10 --> 00:29:12
I'm going to go with control six.

293
00:29:12 --> 00:29:23
I don't know the name of this menu command, but when I'd want to learn the name of a menu command or something dead, I'll often press it repeatedly if I'm feeling lucky and look at the menu to see which thing highlights and then go find in there which thing has a key equivalent.

294
00:29:24 --> 00:29:25
Now let me tell you what Control 6 does.

295
00:29:25 --> 00:29:37
Control 6 opens the rightmost element of the path control above the editor, which lists all the types and functions in the file that you're in, and then you can type into that menu to filter it.

296
00:29:37 --> 00:29:44
So it's kind of like gives you a really scoped open quickly on the file I'm already in, which is how I like to use it.

297
00:29:45 --> 00:29:53
For me, it's probably going to be command option up and down to be able to switch between implementation and header in Objective-C.

298
00:29:53 --> 00:29:59
That's just I use it all the time when I think a new header and I want to just fill in the implementation.

299
00:29:59 --> 00:30:01
So really useful.

300
00:30:02 --> 00:30:04
You always need command shift O.

301
00:30:04 --> 00:30:11
Command-Shift-O is open quickly, so you can navigate to any symbol or do a lot of things with open quickly.

302
00:30:12 --> 00:30:18
And I'm always using Command-Control-R, which is launch without building.

303
00:30:18 --> 00:30:29
So if you want to tear down your application and you want to create some state on disk or something and just relaunch without building, that's what you do all the time.

304
00:30:29 --> 00:30:30
Super useful.

305
00:30:30 --> 00:30:37
when your agent has reported that it's done building and everything looks great, you can just use that command to launch it without having to build again, which is awesome.

306
00:30:37 --> 00:30:44
And as a reminder for folks, if you're not aware, in Xcode settings, you can find all of these keybinds and make your own in a really easy-to-browse table.

307
00:30:45 --> 00:30:50
So every action that can exist in Xcode is in there, it's searchable, and you can make your own keyboard shortcuts as well.

308
00:30:51 --> 00:30:57
Yeah, I was going to say, most useful, Command-Shift-O, Command-R, and U.

309
00:30:57 --> 00:30:59
Yes, the basics for sure, absolutely.

310
00:30:59 --> 00:31:01
The basics, the things that you find in the product menu, basically.

311
00:31:02 --> 00:31:03
- Profile.

312
00:31:03 --> 00:31:03
- Command I.

313
00:31:03 --> 00:31:05
- Command I, yeah, there's mine.

314
00:31:05 --> 00:31:07
- Control backslash I use all the time.

315
00:31:07 --> 00:31:11
It jumps to the next diff that is in this file so you can quickly jump between all the changed lines.

316
00:31:11 --> 00:31:12
- That's a great thing to do.

317
00:31:12 --> 00:31:17
- And command backslash is toggle a comment, which I find very useful, especially in SwiftUI.

318
00:31:17 --> 00:31:20
If you're like, turn this modifier off, just comment it out, command slash.

319
00:31:20 --> 00:31:22
- Yep.

320
00:31:22 --> 00:31:26
- All right, I mean, we could probably keep going about-- - Yeah, forever.

321
00:31:28 --> 00:31:33
- But let's take a look at another highly upvoted question from Claire KC.

322
00:31:33 --> 00:31:36
What are the most common beginner mistakes in Xcode?

323
00:31:36 --> 00:31:36
Ooh, this is a great one.

324
00:31:37 --> 00:31:38
And how do I avoid them?

325
00:31:39 --> 00:31:41
Most common mistakes in Xcode.

326
00:31:43 --> 00:31:47
I mean, mine is trying to learn all of the features all at once.

327
00:31:47 --> 00:31:48
Xcode does a lot.

328
00:31:49 --> 00:31:51
It's a workhorse of an app.

329
00:31:52 --> 00:31:57
So I think you don't have to necessarily look at every single feature right away.

330
00:31:57 --> 00:32:02
Get to know the things that you're going to use most, like your source editor, your inspectors, your navigators, things like that.

331
00:32:03 --> 00:32:14
I'll highlight two things that, you know, if you're using code signing, if you're developing for the phone, you do want to use automatic signing.

332
00:32:14 --> 00:32:21
I mean, I think over the years, maybe you switched to manual signing because there was advice online that you wanted to take.

333
00:32:21 --> 00:32:25
But I think for most cases now, automatic signing is really there for you.

334
00:32:25 --> 00:32:31
And, you know, you can easily get stuck if you're a newbie on these kinds of manual signing workflows.

335
00:32:31 --> 00:32:31
They're too tough.

336
00:32:31 --> 00:32:33
So use automatic signing.

337
00:32:33 --> 00:32:33
That's what I'd say.

338
00:32:33 --> 00:32:56
Yeah, well, this kind of question was was deep in my heart when I was thinking about how to simplify some of the experience for Xcode 27. So the toolbar being full of the controls that were previously in other areas and moving them up to the top level, you can, you know, right click on them, you can customize them, you can swap out things for your workflow.

339
00:32:57 --> 00:33:16
it should make it a little bit easier to get started with a more simple setup so you know new project you get an app you don't have to ask answer any questions you can just start to code and see results on the right hand side um same with the playground if you want to see other results um so you don't necessarily need to go into the template picker try to find the most complicated

340
00:33:16 --> 00:33:39
looking project diving in trying to add a bunch of targets um but you're also at a very fortunate time where uh when you do want to start doing some of these things you can reach for agents to help you um so it can be a great uh safety net when you're navigating around and also um please use source control so any mistakes you do make you can oh my god revert back yeah that that was something

341
00:33:39 --> 00:33:57
in a different group lab that i was hosting it's just like please please use git yeah yeah if you don't know how to use it your agent will and it's not too hard that's probably the most one of the most like really challenging things to get started with if you've never used it before but always use I was going to say, when you create a new project, that's on by default, correct?

342
00:33:57 --> 00:33:57
So, yeah.

343
00:33:59 --> 00:34:03
Starting a Git repository for each of your projects is invaluable.

344
00:34:03 --> 00:34:06
Even if you're working, I always hear like, oh, but it's just me.

345
00:34:06 --> 00:34:07
I don't have a team.

346
00:34:07 --> 00:34:09
I don't have PRs to file that other people will review.

347
00:34:10 --> 00:34:13
It's just for fixing work that you've done that you want to revert.

348
00:34:13 --> 00:34:15
It's so invaluable to be able to do that.

349
00:34:15 --> 00:34:16
It's the best undo system ever.

350
00:34:17 --> 00:34:18
You want this.

351
00:34:19 --> 00:34:19
It's awesome.

352
00:34:19 --> 00:34:22
And if you're building different features, you could keep them separate.

353
00:34:22 --> 00:34:25
You don't have to necessarily conflate the two sets of, you know, work.

354
00:34:25 --> 00:34:27
So please use source control.

355
00:34:28 --> 00:34:30
I have two actually on the profiling side.

356
00:34:30 --> 00:34:33
So I think the first one is profiling on the simulator.

357
00:34:33 --> 00:34:36
This doesn't represent your actual device.

358
00:34:36 --> 00:34:40
So we always profile on an actual real device.

359
00:34:40 --> 00:34:43
And second thing, sometimes I see people profiling a debug build.

360
00:34:44 --> 00:34:46
And that's not advisable.

361
00:34:46 --> 00:34:51
You want compiler to apply all the optimizations and then check it out.

362
00:34:51 --> 00:34:55
sometimes you're gonna start chasing something that is not really a problem in the real world.

363
00:34:55 --> 00:34:57
- Yeah, definitely.

364
00:34:57 --> 00:35:03
Yeah, running, profiling like an iPhone simulator is definitely, you're not gonna get the right data.

365
00:35:05 --> 00:35:07
Any other?

366
00:35:07 --> 00:35:12
- I think if you're new, you should adopt the greatest new framework ideas.

367
00:35:12 --> 00:35:15
You should use Swift Concurrency, you know?

368
00:35:15 --> 00:35:18
But, use SwiftUI.

369
00:35:18 --> 00:35:19
- Yeah, SwiftUI, Swift Concurrency.

370
00:35:19 --> 00:35:36
- There's some coding, code-along sessions as well that we've done over the years those maybe might be a great place to have a like expert guiding you that you can then you know play around in your own space or go off the the beaten path when i was first getting started programming in general which is like not completely related to xcode but

371
00:35:36 --> 00:35:56
sometimes i would take an open source project like a little game or something was maybe something i was made like you know 48 hours so it's relatively small and just try to rewrite it one-to-one and at some point you get something that actually works on your side. So not only can you learn Xcode by trying to just kind of play around with what's inside of it, but just, just getting, you know,

372
00:35:56 --> 00:36:14
your focus on your, your code and leaving Xcode as a, a tool that can power you up after you get your initial stuff running. That's probably the most important bit. Yeah, we actually, I was going to mention there's code along sessions and while they're all usually on a particular topic, like I know there's one on Swift concurrency from last year that SEMA did. What's really great about

373
00:36:14 --> 00:36:33
watching them watching those sessions is just seeing it in the context of xcode and seeing how the tools are integrated in that workflow while you're learning about a new concept as well so all the little icons that are clicking on and you're like that's what okay got it that's what that one's for yeah all right any other i'd just say if you're starting a new project start

374
00:36:33 --> 00:36:47
organizing it almost straight away like from the start even though you don't know if it's going to turn into you know a big project yet like john says with with these folder types just you know you have UI code and maybe you have, you know, different screens and you just start organizing it.

375
00:36:47 --> 00:36:53
The folders are cheap and easy and you'll thank yourself later once it grows into, you know, dozens and dozens of files.

376
00:36:53 --> 00:36:59
I often actually find myself reaching for agents for that too. Like, okay, we did one giant file that's now 600 lines long.

377
00:37:00 --> 00:37:03
Let's make this organized, please do that more often and more consistently.

378
00:37:03 --> 00:37:04
You'll have a great time.

379
00:37:05 --> 00:37:06
Yeah, definitely.

380
00:37:07 --> 00:37:27
Kind of related to that too is something new in Xcode 27 is the way files and projects are created you no longer have to go through that whole modal you could actually just start a new project and it'll be a blank project so if you start there explore the tools you know prototype iterate things like that and then once you get to a place where you're just like oh i

381
00:37:27 --> 00:37:54
want to save this start organizing it we also made that so easy so that you can go experiment you know in a small context like in a little sandbox playground etc yeah so i would say uh there are no beginner mistakes it's just things that you have to try out a little bit more and use the tools that we have to be able to to iterate on it all right great um let's see let's

382
00:37:54 --> 00:38:26
go to a question from protonster protonster uh what are the most efficient workflows for generating and maintaining doc c documentation alongside active development anyone have any insights for this this is an area i wish i was better at in general documentation my code uh yeah it just seems to be right documentation i was gonna say better development practices than these guys

383
00:38:26 --> 00:38:50
write your documentation um i know that there's the documentation preview assistant so anything that you have written uh in your source files will automatically be visible in the documentation preview assistant and you could host a docs a doxy archive as a static website for pr review so you could also use that if you want it to be part of your project and use it in your diffs and you

384
00:38:50 --> 00:39:09
you might have the inverse of this request which is like how do i generate and you know do a great job having like a lot of documentation you might also want to reign an agent back uh they love to add documentation comments that go way too much into detail or reveal implementation details sometimes um so when you are requesting for things to be you know like now is the time where this is

385
00:39:09 --> 00:39:31
now api i really want to make sure that it's documented uh taking the time to review it is is really important i also would recommend writing articles so instruments team writes a lot of articles before we release the features so like internal developers can learn how to use them before we release them externally and for us like i recently learned that i can actually put images

386
00:39:31 --> 00:39:50
in both light and dark modes and that that is really helpful it actually swaps them automatically yes i will say doxy is open source so if you want to take a look at how to use it or implement or any details about it, you can check out the repo for it.

387
00:39:50 --> 00:39:54
But also there are some great sessions on Doxy from a few years ago.

388
00:39:54 --> 00:40:01
Unfortunately, I don't remember all the titles, but please use the, actually developer.apple.com has a generative AI search feature.

389
00:40:02 --> 00:40:05
So if you put in Doxy there, you'll find all the resources about Doxy.

390
00:40:06 --> 00:40:10
All right, let's take a look at the next question from Theo K.

391
00:40:11 --> 00:40:16
Which settings or practices can improve build time significantly?

392
00:40:16 --> 00:40:18
It's a great one.

393
00:40:19 --> 00:40:21
Any tips on build performance or build time performance?

394
00:40:22 --> 00:40:23
I'd be happy to take this.

395
00:40:23 --> 00:40:23
So one of you guys want to do it?

396
00:40:23 --> 00:40:29
Okay, so one of the main things that you're probably thinking about with build performance is your incremental build performance.

397
00:40:29 --> 00:40:31
You know, like Chris mentioned, well, how about Chris's tip again?

398
00:40:31 --> 00:40:36
Command-Ctrl-R, sometimes you've made some code changes and then you realize, I don't actually need those to debug this another time.

399
00:40:37 --> 00:40:39
Command-Ctrl-R is the fastest way to run again.

400
00:40:39 --> 00:40:40
That's run without building.

401
00:40:40 --> 00:40:46
So next would be like, as you work, how much of your build from last time is getting invalidated by the changes you're making this time?

402
00:40:47 --> 00:40:50
And so that has to do with sort of how connected all your source code is to each other.

403
00:40:51 --> 00:41:05
If you split your code into different modules, either packages or frameworks or whatever technique fits best with your workflow, then whole sections of your project can be unreachable from other sections, meaning they won't have to be rebuilt after many changes to them.

404
00:41:05 --> 00:41:07
So that's a great way to save time.

405
00:41:08 --> 00:41:12
There's an assistant editor on the build log that will show you the build timeline.

406
00:41:12 --> 00:41:33
So you could try to understand why more stuff is building than you expect or that things are taking much longer than you expect to build, there are some build settings that you can opt into, maybe in a config file, that will give you warnings if the certain Swift expressions take longer than expected to type check.

407
00:41:33 --> 00:41:38
And so you can ratchet down a threshold and get everything building quickly by just maybe sometimes breaking expressions into multiple lines.

408
00:41:39 --> 00:41:44
And then another gotcha is script phases that people have that run unconditionally.

409
00:41:44 --> 00:42:03
And sometimes those script phases, just they're slow for whatever they do or they invalidate previous build results so they have to build over and over again so just be careful at the script phases make sure that they only run when necessary by declaring their inputs in the project editor is there a warning cream emitted when you create a

410
00:42:03 --> 00:42:15
script face without there might be yes I'm like wondering also build with timing summary yeah that's good that's that um that assistant that you can to bring out, I think.

411
00:42:15 --> 00:42:23
- And yeah, the build timeline, there's actually a session from a few years ago now, the build timeline, about using the build timeline specifically.

412
00:42:23 --> 00:42:29
I was thinking, too, I don't know if this is necessarily related, but explicit modules?

413
00:42:29 --> 00:42:35
Potentially, especially if you're using dependencies and modularizing your project, you want to optimize for those dependencies?

414
00:42:35 --> 00:42:46
- Yeah, I'm not personally an expert on these features, but Swift now also has private and internal import, and I imagine those can sort of reign in the relationship across modules and help you reuse build products from one build to the next as well.

415
00:42:46 --> 00:42:47
All right.

416
00:42:48 --> 00:43:02
Well, there are probably better experts than us, so go to the forums as well if you have any specific use cases or problems that you're trying to solve with your build performance, and we'll have people there to answer your questions.

417
00:43:03 --> 00:43:04
Great.

418
00:43:05 --> 00:43:09
Let's go to the next question from Pirca.

419
00:43:10 --> 00:43:18
What tricks should I employ to get Xcode previews to load faster, not having to wait for an off-screen simulator instance to launch again.

420
00:43:19 --> 00:43:23
Yeah, I think these would be similar sorts of tips to the previous question.

421
00:43:24 --> 00:43:28
It's all about getting the build performance to be what you expect it to be.

422
00:43:29 --> 00:43:40
And then once a preview is up and running, then the machinery just does the minimum amount of work just to reload that current source file and update the preview.

423
00:43:40 --> 00:43:42
Same thing for one of those inline playground macros.

424
00:43:43 --> 00:43:50
So I think if the initial build is slow, I mean, they share the same build artifacts.

425
00:43:50 --> 00:43:52
So you can do an initial build and run.

426
00:43:52 --> 00:43:58
And then if you preview, then it should be relatively quick because it's using the products that have already been built.

427
00:43:58 --> 00:44:06
And then it's just principles that we already talked about, modularizing your project and everything.

428
00:44:06 --> 00:44:08
All right.

429
00:44:08 --> 00:44:11
Let's move on to another question from Newbie.

430
00:44:11 --> 00:44:20
For automatic code formatting, I understand Swift format exists, but I'm not sure how to integrate it into Xcode.

431
00:44:20 --> 00:44:24
I have added them via build phases so that when I compile, it gets run.

432
00:44:24 --> 00:44:32
It works mostly, but at times when I start typing something and it starts to run, it keeps throwing alerts about loading from disk.

433
00:44:32 --> 00:44:42
So I guess this is specific to specifically adding Swift format into their projects.

434
00:44:42 --> 00:44:50
I do think that this is a great one for a feedback report if you're encountering workflow issues because this is a very specific use case.

435
00:44:51 --> 00:44:54
So I do highly recommend you file a feedback for us as well.

436
00:44:54 --> 00:44:58
But any other tips for newbie here on how to make this better?

437
00:44:58 --> 00:45:00
Well, you could consider when you run it, right?

438
00:45:00 --> 00:45:09
Like you could integrate it in a way that runs on build or runs on save, or you could integrate it into your Git workflow so that it runs as like a preparatory step to committing.

439
00:45:10 --> 00:45:13
And that is probably going to be one of the less disruptive.

440
00:45:14 --> 00:45:14
I don't know if that's the right word.

441
00:45:15 --> 00:45:17
But, you know, it's not going to get in your way, you know, if you do it then.

442
00:45:17 --> 00:45:22
And you'll be able to get the right format into your repository as it goes in, which is what really matters.

443
00:45:22 --> 00:45:23
That's true.

444
00:45:23 --> 00:45:30
And especially because that's also when your pull request is complete, you're ready for a review, your feature development is complete.

445
00:45:30 --> 00:45:34
Probably the best time to do that rather than continually doing it on code that you might refactor anyway.

446
00:45:36 --> 00:45:38
All right. Great.

447
00:45:39 --> 00:45:41
Another question from Protonster.

448
00:45:42 --> 00:45:49
What are the most underutilized LLDB commands or breakpoint actions for isolating state changes without stopping execution?

449
00:45:49 --> 00:45:52
I think this is because, Chris, you mentioned play sound.

450
00:45:53 --> 00:45:58
But what breakpoint actions could they use without stopping execution?

451
00:46:00 --> 00:46:02
Yeah, play sound is a good one when you just need that feedback.

452
00:46:02 --> 00:46:08
It might be hit over and over, or you just want to learn, like, is this part of the code actually being hit?

453
00:46:08 --> 00:46:10
And you can get that audio feedback.

454
00:46:10 --> 00:46:18
Or you could, if it's something you want to hit more often or have more diagnostics, you can log a message straight from that action, which is really handy.

455
00:46:18 --> 00:46:20
It saves you putting in print somewhere.

456
00:46:20 --> 00:46:21
You can just have the breakpoint do that for you.

457
00:46:21 --> 00:46:41
Or, like, print a backtrace, you know, trying to log like when these things are occurring right yeah you can call any um any ldb command from there this isn't exactly answering the question it's more about when to stop at just the right time um i'm often debugging like drag and drop or like window layout issues that are

458
00:46:41 --> 00:46:58
happening and there's like lots of layout calls and it's there's not a condition to stop on exactly the right one and so i'll make a conditional break point that has to do with like the shift key being held on my mac and so then i'll get the program to the exact right state where layout has happened thousands of times since then then i'll press shift and trigger it and stop at just the right

459
00:46:58 --> 00:47:28
time to debug that's cool we can skip a couple of invocations like a couple of hits of the breakpoint as well right like sometimes that's useful like when you know the third one is what i want so yeah yeah any other underutilized lldb commands i mean underutilized sometimes i i reach for po like i use the command po when i could use the variables view sometimes i think a command is

460
00:47:28 --> 00:47:50
not always the answer yeah sure the variables view will tell you you know the the values of everything that you have um in scope so that's usually what i'm asking po for you can also like go the other way sometimes i'll use expression and then swift's print so that i'm sure i'm getting like exactly like the escaping that i'm expecting on the output you know text um it's just another

461
00:47:50 --> 00:48:14
version of using that stuff for instruments development we do a lot of work with like hex numbers so i've been using p slash x just to p slash x to print it as a x number so that that's really useful for my line of work sometimes i'm not sure if it's a good habit but sometimes i'll i'll wonder what the program is doing and i'll use the pause button i mean like i don't know

462
00:48:14 --> 00:48:39
you know this this question is about how not to pause but sometimes i intentionally pause just to see where is the program uh lost i haven't set a break point and i already don't have logging or something. So that's useful. Yeah. I do think that LLDB commands in breakpoints, it's a really good combination of both because I mentioned conditional breakpoints or at least being able

463
00:48:39 --> 00:48:57
to set things for the breakpoint. And if you were to do, you know, if you're used to print debugging and you want to still see that in the console, you can have that breakpoint print and then not have it committed into your project where you have random print statements. So that's one of favorite things to do if you're just used to seeing print debugging or using print debugging

464
00:48:57 --> 00:49:28
all right great let's move to uh another highly upvoted question from thbi can i see unused vars properties functions etc in xcode if not why not i think this comes down to what what the compiler can reliably determine you know through static analysis and compilation you you will see warnings for unused variables because it can reliably know yes you you know you defined a variable in scope

465
00:49:28 --> 00:49:48
you haven't used it you might you know you might want to know about that things like public properties can't reasonably be reasoned about because they're public who knows who's going to use them so be interesting to you know hear feedback on use cases where you think it's it's something that's quite clearly what you expect as a developer but the compiler or the

466
00:49:48 --> 00:50:06
build system is not giving you that information yeah i would plug in find call hierarchies again if you're not sure just like run it and you will see what what is calling into that function so but obviously that's not going to list everything in your project yeah something that you always suspect yeah and i think the public properties one especially is just you can't you can't make

467
00:50:06 --> 00:50:24
any of those assumptions because it might be called by something else um so uh that's really an interesting um question especially uh from thbi if you are um needing this in xcode again I don't want to sound like a broken record, but feedback assistant, please.

468
00:50:24 --> 00:50:35
There are, I believe, third-party tools that do this, but if the use case is needed for Xcode to surface that, it would be great to get a feedback report and the use case specifically.

469
00:50:37 --> 00:50:37
Great.

470
00:50:38 --> 00:50:42
Let's take a look at, oh, I love this username.

471
00:50:43 --> 00:50:44
BrewInstallPoppy has a question.

472
00:50:45 --> 00:50:51
Is deleting derived data an actual engineering practice or a superstition we've all collectively agreed to perform?

473
00:50:52 --> 00:50:58
Also, besides derived data and Xcode 27, what should we really reach for at first when a build gets haunted?

474
00:50:59 --> 00:51:02
I love, Gru and Sol Poppy, what a great question.

475
00:51:03 --> 00:51:04
Do you all have any insights on?

476
00:51:05 --> 00:51:10
Derived data is not a superstition, as you mentioned, John, that there's actual reasons for.

477
00:51:10 --> 00:51:11
I mean, it's both, right?

478
00:51:11 --> 00:51:16
It's like it is both a superstition and it works sometimes.

479
00:51:16 --> 00:51:19
So what happened, right?

480
00:51:19 --> 00:51:23
So earlier I was saying that, like, you know, a common example is, like, two targets.

481
00:51:24 --> 00:51:28
You know, you have a target that depends on the output of another target somehow, but in a non-obvious way.

482
00:51:28 --> 00:51:35
And the build typically gets ordered so that the first target's product is produced before the second target consumes it.

483
00:51:35 --> 00:51:38
You know, and so you build and you get this error and you're like, what is happening?

484
00:51:38 --> 00:51:39
You know, why can't you see this definition?

485
00:51:39 --> 00:51:47
It's clearly right here in this file, you know, and your go-to reaction might be to just destroy this directory and try again.

486
00:51:47 --> 00:51:48
And then it works.

487
00:51:48 --> 00:51:50
And you're like, what is going on?

488
00:51:50 --> 00:51:50
Right?

489
00:51:50 --> 00:51:51
So these problems are hard.

490
00:51:51 --> 00:51:56
It's like debugging a race condition in your code, except you're debugging a race condition in the Xcode build system, and you don't get to see the source code.

491
00:51:56 --> 00:51:58
Well, I guess you do get to see the source code of that now.

492
00:51:58 --> 00:51:59
So check it out and debug it.

493
00:52:00 --> 00:52:00
You know?

494
00:52:01 --> 00:52:01
Like, please.

495
00:52:04 --> 00:52:14
But those are some of the biggest causes, or that you're piling up stuff from your previous builds that is taking too much disk space, and you want to get rid of it, you know?

496
00:52:14 --> 00:52:19
Or there is a race condition in the actual build system, and you're working around one of our bugs.

497
00:52:19 --> 00:52:24
Like, those are the reasons why you're deleting that directory, and we want to fix all of those.

498
00:52:26 --> 00:52:33
And, you know, so if you think that we are building your source code incorrectly, because, you know, we actually don't think you should ever have to delete the derived data.

499
00:52:33 --> 00:52:35
It's like it's a workaround for our bug or your bug.

500
00:52:36 --> 00:52:47
So please send us the bug report, especially if you can reproduce it and know what sequence of edits leads to you taking this response because, you know, it's a superstition that works.

501
00:52:47 --> 00:52:51
Based on my experience, I actually think that Xcode 27 got a lot better.

502
00:52:51 --> 00:52:56
Sometimes in previous versions, I would switch between branches and it would fail the build.

503
00:52:56 --> 00:52:58
Now it's working way, way, way better.

504
00:52:59 --> 00:53:04
And yeah, on the size of derived data, I'm sure many developers have been utilizing more work trees.

505
00:53:04 --> 00:53:10
So that creates a new build folder for your project.

506
00:53:10 --> 00:53:15
So sometimes you might want to just clean it after too many work trees.

507
00:53:17 --> 00:53:17
All right.

508
00:53:18 --> 00:53:26
But yes, I believe in Xcode 27 especially, there should be a reduction in the need of deleting derived data.

509
00:53:26 --> 00:53:33
I've taken a lot of effort to make sure that those problems that you typically encounter are resolved.

510
00:53:33 --> 00:53:50
We've absolutely focused and fixed many instances of this, but I don't want to say that it is fixed, but it is something we take very seriously. So let us know if you have any problems. I wanted to acknowledge something that you said as an aside that maybe some folks aren't aware of. You said, oh, you can look at the source

511
00:53:50 --> 00:54:09
code. Yeah, Swift build is open source, which is the build system for Xcode. So if you do want to take a look at how it's working, check out the repo. All right, great. Any last minute, what what other tools should they reach for if their build gets haunted other than deleting derived data?

512
00:54:14 --> 00:54:15
We do have clean, right?

513
00:54:15 --> 00:54:15
Clean.

514
00:54:16 --> 00:54:20
I don't know if these features are still supported.

515
00:54:20 --> 00:54:29
You used to be able to turn off parallelized builds, you know, and that was like an opportunity for maybe more predictably finding one of these missing dependencies.

516
00:54:30 --> 00:54:33
But I'm not sure of other features that we would have that would help you with this.

517
00:54:35 --> 00:54:40
Yeah, I think if you're still reaching for derived data, please reach out to us.

518
00:54:41 --> 00:54:46
so that we could try to figure out in what other cases we could still improve this experience.

519
00:54:47 --> 00:54:50
All right, we have time for a couple more questions.

520
00:54:50 --> 00:54:53
So I'm going to move on to a question from Tammy Santana.

521
00:54:54 --> 00:54:58
Love using Cloud Code in Xcode, building a 50-file SwiftUI app with it.

522
00:54:58 --> 00:55:09
One issue, when context compresses mid-session, the agent silently loses project understanding, and I had to write a 230-line Cloud MD file as a workaround.

523
00:55:09 --> 00:55:14
Would love a compression indicator and a pause before editing mode to protect project.

524
00:55:14 --> 00:55:16
This is more of a feature request.

525
00:55:16 --> 00:55:25
But specifically around agent workflows, trying to get a compression indicator, is that a possibility?

526
00:55:25 --> 00:55:27
Anything's possible with feedback.

527
00:55:28 --> 00:55:29
No, that is a good feedback.

528
00:55:30 --> 00:55:49
I mean, there's good opportunities there to perhaps looking at how you're using that session, perhaps looking at starting, if you haven't already, plan mode and getting something planned out and written to disk and then the the context isn't in your you know chat window chat session it's it's on disk and the model can reach for that whenever

529
00:55:49 --> 00:56:10
it wants to yeah i would say generally creating artifacts that you could reference yeah because also if you're switching between models or uh going between sessions or you have to wait till the next day um you don't lose that context you don't rely on the window being your only source of truth yeah and you want to be a little careful the cloud md is is loaded into the context by

530
00:56:10 --> 00:56:40
default so if it's 230 lines or more you know you you are growing the context by default so but this is an area where we love feedback here so please if you have any workflow impediments or you want to see things improved uh always file feedback especially this time here all right i think we have time for one more question i'm just scanning through to see if there's one that would be great

531
00:56:40 --> 00:57:04
to wrap up on i think i'm gonna go with signing and provisioning profiles for chris i think this is a question for you from klk99 klkj99 signing and provisioning profiles always trip me and my team up we often see mismatches and code signing issues while building what can we do to mitigate that in xcode and each time we need to build and test in instruments we have to manually switch a

532
00:57:04 --> 00:57:26
bunch of provisioning profiles in many targets i'm unfamiliar with an instruments issue i think that that might be sometimes what we see developers do i'm not sure that if that's that's what you're doing is using distribution certificate for their like release configuration and that has all sorts of problems because that that prevents developers from using tools like allocations that actually

533
00:57:26 --> 00:57:49
require your build to be like debuggable and have the right entitlement so if that's what you're doing sometimes developers disable this code sign inject base entitlements uh setting to work around some issues that also is not the right thing to do so that that's on instruments i mean i'd say another thing is i'd plug automatic signing again and and you know one thing that people would use

534
00:57:49 --> 00:58:11
manual signing for is because they ultimately want to set up some ci and if you're you know interested in in xcode cloud then the the signing is handled for you so you can easily use automatic signing locally and the other thing is along the lines of what casper is saying is that i have seen setups where you have a debug configuration and you have a release configuration

535
00:58:11 --> 00:58:32
and that feels like you should use distribution signing for the release configuration and that's not exactly true you you want to use you want to use regular signing locally and and then when you go to upload to the app store that is when you distribution sign it's unnecessary to kind of do distribution signing locally and it can mess things up i think it's quite common like large

536
00:58:32 --> 00:58:57
projects many developers working on something work arounding it at some point in time and then you have these artifacts baked in yeah well hopefully that answers your question klkj99 and if you do have specific issues with your like your setup or particularly around code signing issues again can't say it enough developer.apple.com forums where we could also see what other experiences

537
00:58:57 --> 00:59:17
other developers are having and they can join in the conversation and potentially help debug this with you. All right. Along with our engineers, of course, it's not just all of you on the forums. All right. I think we have time for one more question. And this might be an interesting one to end on. Protonster is asking, how can I configure Xcode's code folding

538
00:59:17 --> 00:59:42
and structural navigation to visually suppress noise and keep only the high value logic on the screen. Seems like a really great feature request. Yes, definitely. It's definitely file feedback around how you can, you picture yourself working. So you can imagine a world where you just kind of want to see basically what the jump bar is giving you, but like in code form, like this kind of

539
00:59:42 --> 01:00:00
stuff is really, really useful feedback to file. So if you can give your workflow or your specific use case while you're giving feedback, it can help us take a look at how to tackle problems like You might also choose to organize your source code so that the noise is contained in one spot.

540
01:00:01 --> 01:00:05
Like you can split definitions for a type across many extensions or many files.

541
01:00:05 --> 01:00:10
So you could choose to sort of contain and put the junk in the junk drawer if you want.

542
01:00:10 --> 01:00:17
I will say you could check out the Xcode's editor menu also to look at code folding actions.

543
01:00:17 --> 01:00:19
If there's something there that actually helps with your workflow.

544
01:00:19 --> 01:00:20
But I agree.

545
01:00:20 --> 01:00:24
I think what's interesting about this is what do you consider visual noise?

546
01:00:24 --> 01:00:26
And so I think it depends on the use case.

547
01:00:27 --> 01:00:30
So feedback is going to help us try to chase that one down for you.

548
01:00:31 --> 01:00:31
All right.

549
01:00:32 --> 01:00:34
And that is all the time we have.

550
01:00:34 --> 01:00:35
We got through lots of questions.

551
01:00:36 --> 01:00:40
Thank you to our panelists, Chris, Jake, John, Casper, other Chris.

552
01:00:41 --> 01:00:44
Thank you for your answers and your insights today.

553
01:00:44 --> 01:00:49
Thank you for everyone behind the scenes who helped triage questions as well as helped keep the show running.

554
01:00:50 --> 01:00:55
And, of course, thank you all who joined, who asked the question or just tuned in.

555
01:00:55 --> 01:01:15
it was such a privilege for us to be able to host this for you today. Don't forget, like I've mentioned already multiple times, if we didn't get to your question today, please go to developer.apple.com forums where we could continue the conversation. And I mentioned briefly, there is a generative AI powered search in developer.apple.com where you could search any of the topics we talked about

556
01:01:15 --> 01:01:35
today and it'll provide you sessions, documentation, articles, anything we got for you to help you in your journey. And last but not least, please submit your code level questions, bug reports, and enhancement requests in feedbackassistant.apple.com. Thank you again. Have a great WWDC.
