You need to start with the RepoMix and create this, and then followed by that, create a constitution. After that, for an existing feature, before you implement this one, make sure that you have all the regression suite, which includes your unit testing, integration testing, and are you even picking up a brownfield project? Before even doing this packet, your first question would be, do you have sufficient regression test available? If the answer is yes, then you move forward with RepoMix and other things. If you do not have that, then tell that GHCP this activity, ensure that you have this unit testing and other things, integration testing and end-to-end testing in place with a reasonable coverage, right? API testing and other things.

Call it out as the first box, and then followed by that, you have a constitution. Create the constitution from this RepoMix, add a new feature with a specification. And I think once a new specification is added, what happens to that when you're adding a second one? Do you need to explicitly refer to that? Does it automatically do? How does it work? Yeah, it will not automatically refer to this.

So the spec is designed in that way. So each feature is independent. So you'll have to explicitly say that? Yes.

Okay. Or you need to refer to that saying that this, you will have to find out the impact and then relate to those specification, right? Yes, we can use that. While creating a feature specification only, you can tell the copilot to check the impact.

So it will does all the work for us. But by default, it will not do. Okay.

So I think, okay. Okay, so even if you have to show that relationship, you will have to explicitly mention that. It will not automatically understand that.

Yes, yes. Yeah, so then it is much more easier. So convert this into a flowchart with only one thing.

One is you have started with what the STDs and other things, right? This is too much textual. And while the content is there, just put it as a flowchart saying that, and then here are the list of projects where we've implemented. Here are the list of pilots.

So I'll forward one another email which I received from Sudhir. And those are the project which you have nominated. And then you say that as an EA team, we are there to support for this quarter.

And until the end of January, we are going to work along with the project team and have them implemented the spec kit as part of their sprints. Right, one sprint or multiple sprint. And our availability, I mean, we will just say that on a weekly, we will support two.

On every day, we are going to support at least one or two projects. And the members are going to be Satish Mani and myself, yourself. That would be the last sliding.

So they may have challenges in terms of how to implement constitution. And you can call it out. While these are something which you have tried on this particular project, there are going to be challenges.

But we are there to help you. For the next three months, our focus is only going to be having this spec kit implemented. Spec kit and other frameworks implemented in the development framework.

And we are going to make ourself available for that. From EA team, three members who are dedicated to support the project team to have this implemented. So once they know that somebody is there to help us, right, and maybe we can work parallelly or maybe we can start, right? We can start with one project each from each tier and then we can expand it to two because we will learn from that one project each.

And so you can start with bare minimum project, one, one each. Or if you wanted to start parallelly, more than that also, it's fine. I don't think it is that complex, yeah.

So either approach is fine. So the point is, there are unknowns, uncertainty, the industry is evolving, so there are going to be new ways of doing things. But having said that, we are available.

The moment you see the last slide, there should be some confidence within the project team as well as DRs. Stating that, okay, there is somebody who is available for the support and then the journey should be easier, easy. Sure, got it.

Right, convert this into a flowchart. This came out really well, but again, I don't think we would have, I don't think we need to spend so much of time explaining them about what it is and directly it is like a job aid. And then you can further go into the details.

Well, having said this, these are only at the high level, but each of this activity, how to do, what steps to follow, everything is mentioned here. And while the document is available, we are going to closely work with the project team and help them out in having this implemented. Sure, yeah, got it.

Right? Our goal is to at least make sure that there are two projects. By the end of December, at least there are three projects, one each from each DR, where one entire sprint is implemented using this spec-driven development. Okay, got it.

Okay, and in that flowchart also, if you can have some depiction, like a loop, when you add a new feature, add a second feature like this. Okay, okay. If you can put it in some manner, stating that if the feature is dependent on something else, then we will have to assign that context.

Yes. Right? And you can call it out some aspects, saying that what is expected as a project team will have to have some good knowledge or a good knowledge in terms of architecture, the practices, what they follow about the project. It cannot be somebody who's completely new, onboarded new and say, so that we need to call out.

Otherwise, the risk what we will get into would be we'll be there to support, but from the project team side, there'll only be one member who may be completely new. Correct. So in order for this to execute, you will have to call it out to the last slide that from the project team side, there has to be a member who clearly understand what this application is.

And both in terms of business, at least on the technical front, who has a good knowledge about, or we'll call it as who has a good knowledge, and that becomes evaluation. And when we are supporting them, we will evaluate them. They are not the good fitment, then we'll call it out, saying that this guy is not working.

Because a process should not get a bad name because somebody from the project team, or even somebody from the EA team didn't perform well. EA team, I'm just citing this as an example, not to say that we'll have some problem. But that's the reality, right? At the end of the day, the blame should not go to the process.

And it's, so if we have those three or five slides, we are good. Sure, you got it. Wonderful, right? So that is what the message is going to be.

We'll schedule a meeting with everyone, saying that here is what our plan is. Spec it on a Brownfield project, period. Yes, that's the issue.

Right? Wonderful, yeah. So we'll start with that one, right? Brownfield, it is challenging the code base, it's literally, you can mention that problem statement, right? You can have one slide, one slide which you have already explained STD, it is good. And the next slide is going to be, okay, while it worked on Greenfield, what about Brownfield? Is it even possible? We have a significant portion.

List down all the normal, common concerns that we have, right? We are mostly into support and maintenance. How do we actually have spec it there? Right? Our code base is very big. How do we support that? And we have an answer to that.

That's how we'll articulate it. Okay, yeah, got it. Right? Fundamentally, there is no difference between both of them.

For an LLM to know what is working behind that, you need to condense that information. So RepoMix is one of the tool. And then we are there to review the constitution, but essentially that is what it is, right? It does not require, and the rest everything, whatever it needs to gather, it will gather at the runtime.

By going through the document, by figuring out what the business rules are. Right? See, what is the reason that you're writing it? Whatever it needs to know, the business rules and everything, it can directly go to the code and get the information, right? So what is the value spec is adding? Why do we really need to have a spec? If everything which is there in the spec would have been implemented as a code, right? Yeah. Then why do we really need a spec? So spec is, you know, STD is, you know, kind of a discipline.

So the main thing is to avoid a web coding and a hallucination. The spec is something you need to, you know, finalize your feature specification first to make sure that our testing and regression should not break. And it takes a lot of bugs.

And again, you need to spend so much time to avoid those things. So instead of that, it saves if your spec is good and code generates based on that. So it's a time saver.

And also it's, you know, anyone in the projects, they can also come with what is the feature and they can read the spec and they can understand it. For the newcomers. So once it, ha, so once it, for a newcomer, yeah, maybe, see, what is missing? No, for a newcomer, they can write a code to understand the documentation, right? See, once you understand the project discovery, once you run the project discovery, you will understand the business domain and then you know the technically how it is organized.

Then there could be some. What is missing there is the intent behind this code. That is what the spec is covering, right? Leaving aside that compressed form written in a fairly easy English language rather than deciphering it from the code.

But the output of a specification is your code. But when you're directly writing a code, why this particular code is written, that will not be known. So the gap between the actual code, which is written and why it is written, the why part is what the spec is going to cover.

Right, otherwise your code will have all the information, right? Yeah. So it's not that if you do not have a spec, we will be missing something. I mean, for an existing feature.

So we will have to look at whatever. So when you write a new specification, you'll have to figure out whether all of those components, whatever we expected it to cover, whether has it covered that or not. Yes, yes.

Right? Yeah. So yeah, you can condense this document and let's say I'll share this document to Satish and Radha saying that we are ready. Here is our plan.

So make sure that you have some good nomination. I'll forward one email from Sudhir also. You can consider that to be the latest.

So he has nominated five projects. We'll go back to title and policy anyway it is in place. So we'll have to get some nomination from Kunal and Pradeep and otherwise we are good.

Okay, so I'll add it to those also. Yeah. Okay, perfect man.

That's all what I have. Anything else? Nothing from myself. Money do you have? I would have put it.

Okay, wonderful then. Thank you. I'll have another call.

Yeah, yeah. Thank you, Vijesh. Thank you