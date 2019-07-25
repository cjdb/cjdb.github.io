# SG20 Education and Recommended Videos for Teaching C++

## Abstract

In today's blog, we look at both the newly minted Study Group for education in the C++ Standard
Committee. We also look at a small number of conference videos that I recommend teachers consider
while they're waiting for this Study Group to produce usable materials.

## Introducing _SG20 Education_

WG21, the C++ Standards Committee, has just approved a new Study Group for education. This study
group is called SG20 Education, demonstrates that they indeed care about ensuring that C++ is both
beginner friendly and expert friendly.

## History

I've been teaching C++ since 2013, and ever since the C++ Core Guidelines came out, I've wanted to
see a set of guidelines emerge for teaching C++. I didn't know how to go about this, so I kept to
myself. I've been publicly trying to innovate education in the C++ world since 2016. This was
motivated, in part, thanks to Kate Gregory and [Sergey Zubkov][cubbi]. I eventually amassed enough
experience and feedback that I felt it was time to deliver a talk at the C++ Edinburgh user-group.
When I was asked to review a draft of the [Direction Group proposal][P0939], I made an almost
off-hand suggestion to Michael Wong that a Study Group for education would be a nice addition to the
Standards Committee. I certainly didn't expect the idea to fly.

Apparently, the Direction Group liked my suggestion so much that they commissioned JC van Winkel and
me to write '[P1231 Proposal for Study Group: C++ Education][P1231]', which asks the committee to
officially form a Study Group for education. At the same time as this, I was working on my CppCon
2018 talk about teaching programming using C++, and that helped JC and me work out that we have
compatible ideas when it comes to teaching C++. [P1231] was so well-received at the C++ Standards
meeting in San Diego that the Committee Convener, Herb Sutter, established SG20 as the Study Group
for Education, with JC van Winkel as its first chair. Congratulations, JC!

### Introducing JC van Winkel

JC has been teaching C++ since 1992, working for AT Computing, a small courseware spin-off of the
University of Nijmegen, the Netherlands. He's been a part of the Netherlands programming languages
committee that represents the Netherlands at the WG21. JC joined Google's Site Reliability
Engineering team in 2010, and is a both a founding member and lead educator of the SRE education
team, SRE EDU.

[P1231] was a joint effort between JC and me. Neither of us are able to individually attend all
three WG21 meetings each year. Given JC's long term experience in WG21, JC was appointed as the
chair for SG20. I'll be acting as a lieutenant, and will step in on organisational matters when
he's unable to attend, but all statements with a sense of finality will come from JC.

## What is a Study Group?

A Study Group is an official part of the Standards Committee that focuses on a single particular
area of interest. These Study Groups review proposals for additions to C++ that are related to that
area of interest. The most famous two are _SG1 Concurrency_ and _SG14 Game Development &
Low-latency_. It is my hope that SG20 will also become a household Study Group.

## What does SG20 aim to achieve?

### Curriculum guidelines

As articulated in [P1231], the goal of SG20 is not to provide [normative] curricula for teaching
C++, but rather to provide teaching and curriculum _guidelines_. Although this was once a dream of
mine, full credit goes to JC for suggesting guidelines in favour of normative curricula. The benefit
of this approach is that it lets teachers pick-and-choose which recommendations are fit for them so
that they can build _their own_ curriculum.

If you're thinking that there should be prescribed materials, I'd ask if there's any style guide
that you've followed, but have disagreed with a particular rule (and possibly disregard it).
Teaching is no different, and we fear that prescriptive curricula may be too rigid, which will cause
disgruntled teachers to build their own curricula anyway, but without the recommendations of
teaching experts.

### Tutorials for new C++ features

The C++ Standards Committee releases a new International Standard for C++ every three years, and
also releases Technical Specifications when necessary. The problem is that the teaching material for
these new features is often lagging and that makes it difficult to adopt new features when a new
release comes out right off the bat. This is extremely problematic for Technical Specifications,
which are released specifically to get feedback _from_ the community!

Another goal of SG20 is to get teaching material out the door when features are added to either
the C++ International Standard or any C++ Technical Specifications. The exact specifics of how this
process works remain to be discussed.

### Rationale to be added to the C++ Standard

During the discussion that birthed SG20, it became apparent that some members of the C++ Standards
Committee would like to see rationale introduced into the Standard. At present, the rationale on new
features is lost between the feature's design and its inclusion into the Standard.

Bjarne has noted that this has been a problem in C++ for a long time: even before C++98. There were
attempts to incorporate rationale, but it seems that a lack of time and motivation whittled these
attempts to naught. In his opinion, [_The Design and Evolution of C++_][dne] (D&E) and the ACM
[_History of Programming Languages_][hopl] (HOPL) papers address some of the lacking rationale. You
can find more in the the C++ papaers for [HOPL II][hopl2cpp] and [HOPL III][hopl3cpp].

JC has praised the rationale found in C89, and agrees that both D&E and HOPL are good movements to
preserve some of the rationale for design decisions in C++. I'm also in agreement on D&amp;E; I
haven't read the HOPL papers yet (but I trust their opinions).

Finally, [Shafik Yaghmour][shafik] has praised the [C99 rationale][c99], saying that it is "an
invaluable tool for understanding C99 choice". Shafik frequently uses this document when he answers
questions on StackOverflow.

### How often will SG20 meet?

Monthly.

### I want to learn or help! How do I join? Do I need to be a member of the C++ Standards Committee?

You don't need to be a member of the C++ Standards Committee, nor do you ever need to be. In fact,
it's best that we have some input from outside of the committee altogether to keep us true to our
mission. If you're interested in joining, please email either [JC van Winkel](mailto:jcvw@google.com)
or [me](mailto:cjdb.ns@gmail.com), and we'll add you to the mailing list.

I expect this process to become automatic at some point, but this is the state of the union at
present.

## SG20 is still in its infancy; I need stuff now!

Yes, SG20 is still starting out. So much so, that at the time of writing, we haven't even had our
first meeting yet. Below are a list of conference videos that I've compiled for teachers to watch
(and will update if recommendations come in). There's well over a day's worth of videos below, but
these aren't a random assortment of my favourite conference videos. Rather, they are sessions that
communicate values about:

* teaching people how to write programs using C++, or
* writing C++ programs using approaches the community agrees produce better code.

Most of the videos listed are not videos on how to facilitate teaching C++, but rather content that
every teacher should know, or a demonstration of how a particular topic has been taught well. I have
grouped the 'teaching C++' videos at the top of the list, and everything else is sorted into various
groups.

Some videos should be watched in a particular order: those videos are grouped together and have an
ordering to their left. Most videos also have an intended audience for that video. You should still
watch all the videos, even if your main student demographic doesn't fall into the specified level.
The intended audience column is designed to give you a bit of a heads up on what level of rigour you
ought to expect while watching that video. It'll then be up to you to adapt that material so that
it's appropriate for your audience.

Please remember that this is a list of videos that I recommend _teachers_ watch, not an exhaustive
list of C++ conference and usergroup videos. If you think your talk or your favourite talk should be
considered by teachers, please send me an email to discuss.

### Strictly on teaching

<font size="-1">
   <table>
      <tr>
         <th></th>
         <th>Event</th>
         <th>Speaker</th>
         <th>Title</th>
         <th>Audience</th>
         <th>Length</th>
      </tr>
      <tr>
         <td align="center">1</td>
         <td align="center">CppCon 2015</td>
         <td align="center">Kate Gregory</td>
         <td align="center"><a href="https://youtu.be/YnWhqhNdYyk">Stop Teaching C</a></td>
         <td align="center">Teachers</td>
         <td align="center">60 min</td>
      </tr>
      <tr>
         <td align="center">2</td>
         <td align="center">CppCon 2016</td>
         <td align="center">Dan Saks</td>
         <td align="center"><a href="https://youtu.be/D7Sd8A6_fYU">extern c: Talking to C Programmers about C++</a></td>
         <td align="center">Keynote</td>
         <td align="center">90 min</td>
      </tr>
      <tr>
         <td align="center">3</td>
         <td align="center">C++ Edinburgh</td>
         <td align="center">Christopher Di Bella</td>
         <td align="center"><a href="https://youtu.be/SUqKlMcOblM">Learning C++ isn't Difficult, Teaching is the Trick</a></td>
         <td align="center">Teachers</td>
         <td align="center">90 min</td>
      </tr>
      <tr>
         <td align="center">4</td>
         <td align="center">CppCon 2018</td>
         <td align="center">Christopher Di Bella</td>
         <td align="center"><a href="https://youtu.be/3AkPd9Nt2Aw">How to Teach C++ and Influence a Generation</a></td>
         <td align="center">Teachers</td>
         <td align="center">60 min</td>
      </tr>
      <tr><td colspan="6"></td></tr>
      <tr>
         <td align="center">-</td>
         <td align="center">CppCon 2017</td>
         <td align="center">Sara Chipps</td>
         <td align="center"><a href="https://youtu.be/zX0YoCDWGxc">Building for the Best of Us: Design and Development with Kids in Mind</a></td>
         <td align="center">Everyone</td>
         <td align="center">60 mins</td>
      </tr>
      <tr>
         <td align="center">-</td>
         <td align="center">CppCon 2017</td>
         <td align="center">Bjarne Stroustrup</td>
         <td align="center"><a href="https://youtu.be/fX2W3nNjJIo">Learning and Teaching Modern C++</a></td>
         <td align="center">Keynote</td>
         <td align="center">90 min</td>
      </tr>
   </table>
</font>

### Diversity and inclusion

<font size="-1">
   <table>
      <tr>
         <th></th>
         <th>Event</th>
         <th>Speaker</th>
         <th>Title</th>
         <th>Audience</th>
         <th>Length</th>
      </tr>
      <tr>
         <td align="center">-</td>
         <td align="center">Meeting C++ 2017</td>
         <td align="center">Guy Davidson</td>
         <td align="center"><a href="https://youtu.be/7GIZN03-_6w">Diversity and Inclusion</a></td>
         <td align="center">Lightning Talk</td>
         <td align="center">15 min</td>
      </tr>
      <tr>
         <td align="center">-</td>
         <td align="center">NDC Oslo 2018</td>
         <td align="center">Patricia Aas</td>
         <td align="center"><a href="https://youtu.be/02gpZuK5gF8">Deconstructing Privilege</a></td>
         <td align="center">Everyone</td>
         <td align="center">60 min</td>
      </tr>
   </table>
</font>

### Critical talks

The following talks are talks that all programmers should watch as soon as possible. As programming
teachers are a subset of programmers, this includes you.

<font size="-1">
   <table>
      <tr>
         <th></th>
         <th>Event</th>
         <th>Speaker</th>
         <th>Title</th>
         <th>Audience</th>
         <th>Length</th>
      </tr>
      <tr>
         <td align="center">1</td>
         <td align="center">CppCon 2018</td>
         <td align="center">Kate Gregory</td>
         <td align="center"><a href="https://youtu.be/n0Ak6xtVXno">Simplicity: Not Just For Beginners</a></td>
         <td align="center">Keynote</td>
         <td align="center">90 min</td>
      </tr>
      <tr>
         <td align="center">2</td>
         <td align="center">CppCon 2017</td>
         <td align="center">Matt Godbolt</td>
         <td align="center"><a href="https://youtu.be/bSkpMdDe4g4">What Has My Compiler Done for Me Lately? Unbolting the Compiler's Lid</a></td>
         <td align="center">Keynote</td>
         <td align="center">90 min</td>
      </tr>
      <tr><td colspan="6"></td></tr>
      <tr>
         <td align="center">-</td>
         <td align="center">CppCon 2018</td>
         <td align="center">Bryce Adelstein Lelbach</td>
         <td align="center"><a href="https://youtu.be/FJIn1YhPJJc">The C++ Execution Model</a></td>
         <td align="center">Intermediate</td>
         <td align="center">60 min</td>
      </tr>
      <tr>
         <td align="center">-</td>
         <td align="center">GoingNative 2013</td>
         <td align="center">Sean Parent</td>
         <td align="center"><a href="https://channel9.msdn.com/Events/GoingNative/2013/Cpp-Seasoning">C++ Seasoning</a></td>
         <td align="center">-</td>
         <td align="center">75 min</td>
      </tr>
      <tr>
         <td align="center">-</td>
         <td align="center">Pacific++ 2018</td>
         <td align="center">Titus Winters</td>
         <td align="center"><a href="https://youtu.be/IY8tHh2LSX4">C++ Past vs. Future</a></td>
         <td align="center">Keynote</td>
         <td align="center">90 min</td>
      </tr>
      <tr>
         <td align="center">-</td>
         <td align="center">CppCon 2018</td>
         <td align="center">Jonathan Boccara</td>
         <td align="center"><a href="https://youtu.be/2olsGf6JIkU">105 STL Algorithms in Less Than an Hour</a></td>
         <td align="center">Everyone</td>
         <td align="center">60 min</td>
      </tr>
      <tr>
         <td align="center">-</td>
         <td align="center">CppCon 2018</td>
         <td align="center">Ben Deane</td>
         <td align="center"><a href="https://youtu.be/I52uPJSoAT4">Easy to Use, Hard to Misuse: Declarative Style in C++</a></td>
         <td align="center">Everyone</td>
         <td align="center">60 min</td>
      </tr>
      <tr>
         <td align="center">-</td>
         <td align="center">GoingNative 2013</td>
         <td align="center">Stephan T. Lavaej</td>
         <td align="center"><a href="https://channel9.msdn.com/Events/GoingNative/2013/Don-t-Help-the-Compiler">Don't Help the Compiler</a></td>
         <td align="center">-</td>
         <td align="center">75 min</td>
      </tr>
      <tr>
         <td align="center">-</td>
         <td align="center">CppCon 2018</td>
         <td align="center">Barbara Geller and Ansel Sermersheim</td>
         <td align="center"><a href="https://youtu.be/XEXpwis_deQ">Undefined Behavior is Not an Error</a></td>
         <td align="center">Everyone</td>
         <td align="center">60 min</td>
      </tr>
      <tr>
         <td align="center">-</td>
         <td align="center">CppCon 2016</td>
         <td align="center">Chandler Carruth</td>
         <td align="center"><a href="https://youtu.be/vElZc6zSIXM">High Performance Code 201: Hybrid Data Structures</a></td>
         <td align="center">Intermediate</td>
         <td align="center">60 min</td>
      </tr>
      <tr>
         <td align="center">-</td>
         <td align="center">CppCon 2016</td>
         <td align="center">Jason Turner</td>
         <td align="center"><a href="https://youtu.be/zBkNBP00wJE">Rich Code for Tiny Computers: A Simple Commodore 64 Game in C++17</a></td>
         <td align="center">Keynote</td>
         <td align="center">90 min</td>
      </tr>
      <tr>
         <td align="center">-</td>
         <td align="center">Pacific++ 2017</td>
         <td align="center">Chandler Carruth</td>
         <td align="center"><a href="https://youtu.be/uZI_Qla4pNA">LLVM: A Modern, Open C++ Toolchain</a></td>
         <td align="center">Keynote</td>
         <td align="center">90 min</td>
      </tr>
   </table>
</font>

### History

You might think that videos covering the history of C++ or the history of programming in general
aren't suited for a list on teaching. History is something that _teachers_ need to understand in
order to teach effectively: it helps to answer questions or opinions that you might have. Why are
things the way they are today? What does this term _actually_ mean? What problems are that feature
meant to help solve? What can we learn from the past that will help us write better software in the
future? Not all of the knowledge that is imparted by these videos is suitable for students to learn
from, but it will help to answer some of the tougher questions that students will eventually ask.

<font size="-1">
   <table>
      <tr>
         <th></th>
         <th>Event</th>
         <th>Speaker</th>
         <th>Title</th>
         <th>Audience</th>
         <th>Length</th>
      </tr>
      <tr>
         <td align="center">-</td>
         <td align="center">Pacific++ 2018</td>
         <td align="center">Sean Parent</td>
         <td align="center"><a href="https://youtu.be/iwJpxWHuZQY">Generic Programming</a></td>
         <td align="center">Keynote</td>
         <td align="center">90 min</td>
      </tr>
   </table>
</font>

### Good programming habits

<font size="-1">
   <table>
      <tr>
         <th></th>
         <th>Event</th>
         <th>Speaker</th>
         <th>Title</th>
         <th>Audience</th>
         <th>Length</th>
      </tr>
      <tr>
         <td align="center">1</td>
         <td align="center">CppCon 2014</td>
         <td align="center">Bjarne Stroustrup</td>
         <td align="center"><a href="https://youtu.be/nesCaocNjtQ">Make Simple Tasks Simple!</a></td>
         <td align="center">Keynote</td>
         <td align="center">90 min</td>
      </tr>
      <tr>
         <td align="center">2</td>
         <td align="center">CppCon 2014</td>
         <td align="center">Herb Sutter</td>
         <td align="center"><a href="https://youtu.be/xnqTKD8uD64">Back to the Basics! Essentials of Modern C++ Style</a></td>
         <td align="center">Keynote</td>
         <td align="center">90 min</td>
      </tr>
      <tr><td colspan="6"></td></tr>
      <tr>
         <td align="center">1</td>
         <td align="center">CppCon 2015</td>
         <td align="center">Bjarne Stroustrup</td>
         <td align="center"><a href="https://youtu.be/1OEu9C51K2A">Writing Good C++14</a></td>
         <td align="center">Keynote</td>
         <td align="center">90 min</td>
      </tr>
      <tr>
         <td align="center">2</td>
         <td align="center">CppCon 2015</td>
         <td align="center">Herb Sutter</td>
         <td align="center"><a href="https://youtu.be/hEx5DNLWGgA">Writing Good C++14... By Default</a></td>
         <td align="center">Keynote</td>
         <td align="center">90 min</td>
      </tr>
      <tr>
         <td align="center">3</td>
         <td align="center">CppCon 2017</td>
         <td align="center">Kate Gregory</td>
         <td align="center"><a href="https://youtu.be/XkDEzfpdcSg">10 Core Guidelines You Need to Start Using Now</a></td>
         <td align="center">Everyone</td>
         <td align="center">60 min</td>
      </tr>
      <tr><td colspan="6"></td></tr>
      <tr>
         <td align="center">-</td>
         <td align="center">CppCon 2015</td>
         <td align="center">Eric Niebler</td>
         <td align="center"><a href="https://youtu.be/mFUXNMfaciE">Ranges for the Standard Library</a></td>
         <td align="center">Keynote</td>
         <td align="center">90 min</td>
      </tr>
      <tr>
         <td align="center">-</td>
         <td align="center">CppCon 2018</td>
         <td align="center">Jason Turner</td>
         <td align="center"><a href="https://youtu.be/DHOlsEd0eDE">Applied Best Practices</a></td>
         <td align="center">Everyone</td>
         <td align="center">60 min</td>
      </tr>
      <tr>
         <td align="center">-</td>
         <td align="center">CppCon 2017</td>
         <td align="center">Titus Winters</td>
         <td align="center"><a href="https://youtu.be/tISy7EJQPzI">C++ as a "Live at Head" Language</a></td>
         <td align="center">Keynote</td>
         <td align="center">90 min</td>
      </tr>
      <tr>
         <td align="center">-</td>
         <td align="center">CppCon 2017</td>
         <td align="center">Titus Winters and Hyrum Wright</td>
         <td align="center"><a href="https://youtu.be/u5senBJUkPc">All Your Tests are Terrible: Tales from the Trenches</a></td>
         <td align="center">Everyone</td>
         <td align="center">60 min</td>
      </tr>
      <tr><td colspan="6" align="center"><font size="+0"><b>Types, objects, values, and Regular types</b></font></td></tr>
      <tr><td colspan="6"></td></tr>
      <tr>
         <th></th>
         <th>Event</th>
         <th>Speaker</th>
         <th>Title</th>
         <th>Audience</th>
         <th>Length</th>
      </tr>
      <tr>
         <td align="center">1</td>
         <td align="center">CppCon 2018</td>
         <td align="center">Titus Winters</td>
         <td align="center"><a href="https://youtu.be/xTdeZ4MxbKo">Modern C++ Design (part 1 of 2)</a></td>
         <td align="center">Intermediate</td>
         <td align="center">60 min</td>
      </tr>
      <tr>
         <td align="center">2</td>
         <td align="center">CppCon 2018</td>
         <td align="center">Titus Winters</td>
         <td align="center"><a href="https://youtu.be/tn7oVNrPM8I">Modern C++ Design (part 2 of 2)</a></td>
         <td align="center">Intermediate</td>
         <td align="center">60 min</td>
      </tr>
      <tr>
         <td align="center">3</td>
         <td align="center">CppCon 2018</td>
         <td align="center">Victor Ciura</td>
         <td align="center"><a href="https://youtu.be/h60zqdzIelE">Regular Types and Why Do I Care?</a></td>
         <td align="center">Everyone</td>
         <td align="center">60 min</td>
      </tr>
      <tr><td colspan="6"></td></tr>
      <tr>
         <td align="center">-</td>
         <td align="center">CppCon 2018</td>
         <td align="center">Nicole Mazzuca</td>
         <td align="center"><a href="https://youtu.be/PkyD1iv3ATU">Value Semantics: Fast, Safe, and Correct by Default</a></td>
         <td align="center">Everyone</td>
         <td align="center">20 min</td>
      </tr>
      <tr>
         <td align="center">-</td>
         <td align="center">CppCon 2018</td>
         <td align="center">Simon Brand</td>
         <td align="center"><a href="https://youtu.be/J4A2B9eexiw">How to Write Well-Behaved Value Wrappers</a></td>
         <td align="center">Intermediate</td>
         <td align="center">60 min</td>
      </tr>
      <tr><td colspan="6" align="center"><font size="+0"><b>C++17</b></font></td></tr>
      <tr><td colspan="6"></td></tr>
      <tr>
         <th></th>
         <th>Event</th>
         <th>Speaker</th>
         <th>Title</th>
         <th>Audience</th>
         <th>Length</th>
      </tr>
      <tr>
         <td align="center">1</td>
         <td align="center">CppCon 2017</td>
         <td align="center">Bryce Adelstein Lelbach</td>
         <td align="center"><a href="https://youtu.be/fI2xiUqqH3Q">C++17 Features (part 1 of 2)</a></td>
         <td align="center">Everyone</td>
         <td align="center">60 min</td>
      </tr>
      <tr>
         <td align="center">2</td>
         <td align="center">CppCon 2017</td>
         <td align="center">Bryce Adelstein Lelbach</td>
         <td align="center"><a href="https://youtu.be/qjxBKINAWk0">C++17 Features (part 2 of 2)</a></td>
         <td align="center">Everyone</td>
         <td align="center">60 min</td>
      </tr>
      <tr><td colspan="6"></td></tr>
      <tr>
         <td align="center">-</td>
         <td align="center">CppCon 2017</td>
         <td align="center">Jason Turner</td>
         <td align="center"><a href="https://youtu.be/nnY4e4faNp0">Practical C++17</a></td>
         <td align="center">Everyone</td>
         <td align="center">60 min</td>
      </tr>
      <tr><td colspan="6" align="center"><font size="+0"><b>Parallel and Heterogeneous Programming</b></font></td></tr>
      <tr><td colspan="6"></td></tr>
      <tr>
         <th></th>
         <th>Event</th>
         <th>Speaker</th>
         <th>Title</th>
         <th>Audience</th>
         <th>Length</th>
      </tr>
      <tr>
         <td align="center">1</td>
         <td align="center">CppCon 2016</td>
         <td align="center">Bryce Adelstein Lelbach</td>
         <td align="center"><a href="https://youtu.be/Vck6kzWjY88">The C++17 Parallel Algorithms Library and Beyond</a></td>
         <td align="center">Everyone</td>
         <td align="center">60 min</td>
      </tr>
      <tr>
         <td align="center">2</td>
         <td align="center">CppCon 2017</td>
         <td align="center">Michael Wong and Gordon Brown</td>
         <td align="center"><a href="https://youtu.be/RoUYiHTsEFE">Parallel STL for CPU and GPU -- The Future of Heterogeneous/Distributed C++</a></td>
         <td align="center">Everyone</td>
         <td align="center">60 min</td>
      </tr>
      <tr><td colspan="6"></td></tr>
      <tr>
         <td align="center">1</td>
         <td align="center">CppCon 2016</td>
         <td align="center">Gordon Brown, Ruyman Reyes, and Michael Wong</td>
         <td align="center"><a href="https://youtu.be/0Thv72yhxxw">Towards Heterogeneous Programming in C++</a></td>
         <td align="center">Everyone</td>
         <td align="center">60 min</td>
      </tr>
      <tr>
         <td align="center">2</td>
         <td align="center">CppCon 2018</td>
         <td align="center">Gordon Brown</td>
         <td align="center"><a href="https://youtu.be/miqZS6aS9K0">A Modern C++ Programming Model for GPUs using Khronos SYCL</a></td>
         <td align="center">Everyone</td>
         <td align="center">60 min</td>
      </tr>
      <tr><td colspan="6" align="center"><font size="+0"><b>C++20</b></font></td></tr>
      <tr><td colspan="6"></td></tr>
      <tr>
         <th></th>
         <th>Event</th>
         <th>Speaker</th>
         <th>Title</th>
         <th>Audience</th>
         <th>Length</th>
      </tr>
      <tr>
         <td align="center">-</td>
         <td align="center">CppCon 2018</td>
         <td align="center">Andrew Sutton</td>
         <td align="center"><a href="https://youtu.be/ZeU6OPaGxwM">Concepts in 60: Everything you need to know and nothing you don't</a></td>
         <td align="center">Everyone</td>
         <td align="center">60 min</td>
      </tr>
   </table>
</font>

### Tools

<font size="-1">
   <table>
      <tr>
         <th></th>
         <th>Event</th>
         <th>Speaker</th>
         <th>Title</th>
         <th>Audience</th>
         <th>Length</th>
      </tr>
      <tr>
         <td align="center">-</td>
         <td align="center">CppCon 2018</td>
         <td align="center">Anastasia Kazakova</td>
         <td align="center"><a href="https://youtu.be/eGWM_dI5egQ">Debug C++ Without Running</a></td>
         <td align="center">Intermediate</td>
         <td align="center">60 min</td>
      </tr>
      <tr>
         <td align="center">-</td>
         <td align="center">CppCon 2018</td>
         <td align="center">Phil Nash</td>
         <td align="center"><a href="https://youtu.be/Ob5_XZrFQH0">Modern C++ Testing with Catch2</a></td>
         <td align="center">Everyone</td>
         <td align="center">60 min</td>
      </tr>
      <tr>
         <td align="center">-</td>
         <td align="center">CppCon 2018</td>
         <td align="center">Anny Gakhokidze</td>
         <td align="center"><a href="https://youtu.be/K4XxeB1Duyo">Workflow hacks for developers</a></td>
         <td align="center">Lightning Talk</td>
         <td align="center">5 min</td>
      </tr>
      <tr><td colspan="6" align="center"><font size="+0"><b>Microsoft Visual Studio and VS Code</b></font></td></tr>
      <tr><td colspan="6"></td></tr>
      <tr>
         <td align="center">1</td>
         <td align="center">CppCon 2017</td>
         <td align="center">Rong Lu</td>
         <td align="center"><a href="https://youtu.be/rFdJ68WbkdQ">C++ Development with Visual Studio Code</a></td>
         <td align="center">Everyone</td>
         <td align="center">60 min</td>
      </tr>
      <tr>
         <td align="center">2</td>
         <td align="center">CppCon 2018</td>
         <td align="center">Rong Lu</td>
         <td align="center"><a href="https://youtu.be/JME1i3vCRR8">What's new in Visual Studio Code for C++ development</a></td>
         <td align="center">Everyone</td>
         <td align="center">30 min</td>
      </tr>
      <tr><td colspan="6"></td></tr>
      <tr>
         <td align="center">-</td>
         <td align="center">CppCon 2017</td>
         <td align="center">Steve Carroll and Daniel Moth</td>
         <td align="center"><a href="https://youtu.be/jsdn3kXFVdA">Latest and Greatest in Visual Studio for C++ developers</a></td>
         <td align="center">-</td>
         <td align="center">60 min</td>
      </tr>
      <tr><td colspan="6" align="center"><font size="+0"><b>Debugging</b></font></td></tr>
      <tr><td colspan="6"></td></tr>
      <tr>
         <td align="center">-</td>
         <td align="center">CppCon 2015</td>
         <td align="center">Greg Law</td>
         <td align="center"><a href="https://youtu.be/PorfLSr3DDI">Give me 15 minutes and I'll change your view of GDB</a></td>
         <td align="center">Lightning Talk</td>
         <td align="center">15 min</td>
      </tr>
      <tr>
         <td align="center">-</td>
         <td align="center">CppCon 2016</td>
         <td align="center">Greg Law</td>
         <td align="center"><a href="https://youtu.be/-n9Fkq1e6sg">GDB - A Lot More Than You Knew</a></td>
         <td align="center">Everyone</td>
         <td align="center">60 min</td>
      </tr>
      <tr>
         <td align="center">-</td>
         <td align="center">CppCon 2018</td>
         <td align="center">Simon Brand</td>
         <td align="center"><a href="https://youtu.be/0DDrseUomfU">How C++ Debuggers Work</a></td>
         <td align="center">Everyone</td>
         <td align="center">60 min</td>
      </tr>
      <tr>
         <td align="center">-</td>
         <td align="center">Pacific++ 2018</td>
         <td align="center">James McNellis</td>
         <td align="center"><a href="https://youtu.be/BVslyei0804">Time Travel Debugging</a></td>
         <td align="center">Everyone</td>
         <td align="center">90 min</td>
      </tr>
      <tr><td colspan="6" align="center"><font size="+0"><b>Build systems</b></font></td></tr>
      <tr><td colspan="6"></td></tr>
      <tr>
         <td align="center">1</td>
         <td align="center">C++ Edinburgh</td>
         <td align="center">Morris Hafner</td>
         <td align="center">Building C++</td>
         <td align="center">Everyone</td>
         <td align="center">60 min</td>
      </tr>
      <tr>
         <td align="center">2</td>
         <td align="center">C++Now 2017</td>
         <td align="center">Daniel Pfeifer</td>
         <td align="center"><a href="https://youtu.be/bsXLMQ6WgIk">Effective CMake</a></td>
         <td align="center">-</td>
         <td align="center">90 min</td>
      </tr>
      <tr>
         <td align="center">3</td>
         <td align="center">Meeting C++ 2017</td>
         <td align="center">Mathieu Ropert</td>
         <td align="center"><a href="https://youtu.be/ztrnb-bVVPo">Modern CMake for modular design</a></td>
         <td align="center">-</td>
         <td align="center">60 min</td>
      </tr>
   </table>
</font>

### Benchmarking

I've often seen people claiming that C++ is this programming language that you only use if you're
after high performance, or are wanting to "squeeze every last cycle out of the CPU" as one person
put it. This isn't true: a lot of programmers use C++ for its lightweight abstraction model and its
ability to let us expressively write code.

The only time a C++ programmer cares about performance or efficiency is when we benchmark the code
that we want to claim is 'efficient'.

<font size="-1">
   <table>
      <tr>
         <th></th>
         <th>Event</th>
         <th>Speaker</th>
         <th>Title</th>
         <th>Audience</th>
         <th>Length</th>
      </tr>
      <tr>
         <td align="center">1</td>
         <td align="center">CppCon 2015</td>
         <td align="center">Bryce Adelstein Lelbach</td>
         <td align="center"><a href="https://youtu.be/zWxSZcpeS8Q">Benchmarking C++ Code</a></td>
         <td align="center">-</td>
         <td align="center">60 min</td>
      </tr>
      <tr>
         <td align="center">2</td>
         <td align="center">CppCon 2015</td>
         <td align="center">Chandler Carruth</td>
         <td align="center"><a href="https://youtu.be/nXaxk27zwlk">Tuning C++: Benchmarks, and CPUs, and Compilers! Oh My!</a></td>
         <td align="center">Keynote</td>
         <td align="center">90 min</td>
      </tr>
      <tr><td colspan="6"></td></tr>
      <tr>
         <td align="center">-</td>
         <td align="center">CppCon 2017</td>
         <td align="center">Frederic Tingaud</td>
         <td align="center"><a href="https://youtu.be/mDkuJvxlF4I">quick-bench.com</a></td>
         <td align="center">Lightning Talk</td>
         <td align="center">5 min</td>
      </tr>
      <tr>
         <td align="center">-</td>
         <td align="center">CppCon 2018</td>
         <td align="center">Frederic Tingaud</td>
         <td align="center"><a href="https://youtu.be/-0tO3Eni2uo">A Little Order: Delving into the STL sorting algorithms</a></td>
         <td align="center">Everyone</td>
         <td align="center">30 min</td>
      </tr>
   </table>
</font>

## What's next?

SG20 meets, writes papers, and advises. In the meantime, I plan to blog about some books (maybe
book reviews) at some point. Stay tuned.

## Comments and feedback

I haven’t yet set up Disqus for this site. If you’d like to discuss what you’ve read here, please
head to this [GitHub issue][comments]. I’m always open to feedback!

If you'd like to find out when I blog next, please use my [RSS feed][rss].

## Acknowledgements

Thanks to JC van Winkel, [Simon Brand][simon], [Kate Gregory][gregcons], [Nicole Mazzuca][nicole],
[Matt Stark][stark], [Bjarne Stroustrup][bjarne], [Titus Winters][titus], [Michael Wong][michael],
and [Shafik Yaghmour][shafik] for reviewing.

[bjarne]: https://www.stroustrup.com
[c99]: http://www.open-std.org/jtc1/sc22/wg14/www/C99RationaleV5.10.pdf
[comments]: https://github.com/cjdb/cjdb.github.io/issues/7
[cubbi]: https://www.quora.com/profile/Sergey-Zubkov-1
[dne]: http://stroustrup.com/dne.html
[gregcons]: http://www.gregcons.com/Kateblog/
[hopl]: http://hopl.info/
[hopl2cpp]: http://stroustrup.com/hopl2.pdf
[hopl3cpp]: http://stroustrup.com/hopl-almost-final.pdf
[nicole]: https://www.github.com/ubsan
[normative]: https://www.google.com/search?q=define+normative&ie=utf-8&oe=utf-8&client=firefox-b-ab
[michael]: https://wongmichael.com/about
[P0939]: https://wg21.link/p0939
[P1231]: https://wg21.link/p1231
[rss]: https://www.cjdb.com.au/feed.xml
[shafik]: https://t.co/LI3H5fOQUI
[simon]: https://tartanllama.xyz
[stark]: https://github.com/matts1
[titus]: https://twitter.com/tituswinters
