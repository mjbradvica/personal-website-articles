# Get Insured

## Title

Hello Everyone,

Thanks for having me this evening, shout out to Brian and the rest of everyone who makes ODevs a reality. This isn't free money or time-wise to put on to host. So I appreciate everything you do.

This talk is technically a part 1 of 2. Assuming that Brian doesn't hate me in the end--I'll do the second part at a future meetup.

The name of this talk is simply, "Get Insured", while I won't be talking about the extended warranty for your car this evening. I do want to keep the idea around insurance and what we use insurance for as the focal point for this conversation.

Except in this case, the vehicle in question is your application or suite of applications your company maintains.

Just a heads up, I don't intend this to be a lecture or only me talking. Feel free to raise your hand if you would like to share some insight or have a question.

## Personal

Real quick, who am I?

My name is Michael Bradvica, I'm one of the three Mike B's running around this place. Are the other two here today? Currently a full-stack developer at the theme park here in town. We're the ones with the dolphins and animals.

Before that, I did a stint in the professional services industry, and in total, I've done full-stack dotnet development for about a decade now.

And the obligatory, these are my own personal thoughts, they don't necessarily reflect the opinions or thoughts of my employer.

I'll save the shameless plugs for later on in the presentation when I can keep the slide up during Q&A.

## Humans and Risk

Does anyone here have any experience in insurance, risk management, or asset protection?

(No), ok, so what is the point of risk management? What is at the very core of its purpose?

The protection against loss of assets, that, if lost, would result in a catastrophic loss of working capital, possibly destroy our cash flow, from which we may never recover.

Humans, even back in the "ooga-booga" days realized this. As _exaggerate_ far back as Hammurabi, thousands of years ago, rules and regulations were dictating the handling and outcomes of goods and property in the event of adverse conditions.

My favorite of which, is if you built someone a house and it collapses due to your negligence, you as the builder were put to death.

More importantly, there was one of the first instances of disaster relief. If you owned money on a loan or working capital expense and something happened (act of god, injury, someone dies of dysentery) your debt was relieved or forgiven.

As a shipping merchant, one could take out a load, and pay the lender an extra fee, and if someone happened to the shipment such as the boat sank, mutiny, or piracy (and btw, pirate insurance is still a thing today) the merchant was not personally liable. One of the first instances of maritime insurance.

If you've been following the news as of yesterday, maritime insurance is paying off right now.

## Application Service

So we as software engineers, deal in code right? We let the boring insurance salesmen deal with their policies and we have our code babies to watch over.

What we have here is a generic application-level service. This would sit between whatever your presentation framework and persistence framework would be.

Side note, this is c-sharp, but I'm not here to talk about c-sharp. So just imagine it being in whatever your stack is.

I have a small service, called "widget service" and we are taking in a request, modifying some internal fields, updating the persistence model, and returning if the operation was a success or not. Like I said, the code here doesn't matter. More importantly, it's what code _means_ to my organization that matters.

So we as code shipping merchants, have our payload, the widget service. We need to ensure (with an E) that it gets from point A to point B without sinking, being abducted by pirates, or being swallowed up by Poseidon himself.

So what do we do? We need to make sure that the code I've written does what I think it will do.

## Application Service Tests

We write a unit test!

And here it is. This is just the first of three unit tests that are required for this service. My function had three logical branches, one to check for a null entity, and the final status check at the end. But, I only have room here for one. So you get one. But just imagine I have two more here.

I have written an "insurance policy" for the three possible situations in this function. One in case my entity was null, one if the update fails, and one on success.

And because I have this in-code, I can exercise my policy whenever I want, as many times as I want, until my CPU finally decides the time has come to retire.

Writing tests, and not just tests, but automated tests is the best insurance that you as a developer can buy.

## Diversify, diversify, diversify

Just like in code, our insurance needs span a vast array of wants and needs. This is only a small sample of what we deal with personally. I'm sure we could go into inception levels of reinsurance and "insurance-like" products such as annuities and personal trusts.

Life requires it one way or another. And we all know this. At least I hope we all know this. We may not like it, but deep down inside of us, we all know that insurance is a mandatory part of life.

## Developer Insurance

Similar to the real-life products from the previous slide-we as developers have an array of insurance products available to us. The ones we are primarily responsible for are Unit, Integration, and End-To-End.

Just a side note, when I refer to an End-To-End test I am talking about something you would write in Cypress for Node or Playwright for dotnet or Java. These are used when you have some front-end framework that involves your router or needs to make an API. So I'm referring to that when I say, "End-To-End".

Continuing, we as developers are primarily responsible for the left-hand side, and others in the organization are typically responsible for the right-hand side. This is more of a Venn diagram with a small degree of overlap. But I'm not here to split hairs over where those boundaries lie, and if your organization is having territory wars over testing boundaries, then I would say you have bigger problems on your hands than what I'm talking about here.

And just like in real life, you can over-insure your application. Testing superfluous things, or worrying about line and statement coverage. You can easily under-insure, by missing key logical branches. Or just forgoing integration testing altogether.

Just like knowing what your deductible needs to be, or how much collision coverage you require. Learning how and when to apply testing is a learned skill that takes time to perfect.

I know we have a pretty good range of experience here, so if all of this is new to you; don't fret.

## Priorities

So, we need a car to drive to work in and we need a home or apartment to live in.

What do we need to show proof of before we can buy or rent either of those?

We need insurance. So what's more important? The car, or the car insurance?

To me the car has a dependency on the insurance, so the insurance is more important. It's not the most pertinent, but it is more important.

I know everyone here is not like 25% of the people in this state who drive around without car insurance. If I told you to drive a week without insurance, would you do it?

Probably not, because all the possible scenarios are going through your head right now of what could happen.

Why do we throw all this rational thinking out the door when it comes to software?

## What's More Important

Back again to some pseudo-code. Except, what I have here is the simplest example of constructor injection possible. And to remove any other thoughts from our heads, they have been aptly named IDependency, our interface. And Dependence, our class. And I'm sure we would all agree that yes, Dependency Injection is good. We should all use it.

And why do we use it? After all, I'm only registering a single dependency for a single interface in my application, or at least I hope everyone here is.

Because we need to be able to mock this dependency in our test code somewhere to prove my code does what I want it to. Even though I have no run-time use for the interface.

Let me clarify that, when I run this code, I will always use the same implementation of this interface. Negating the requirement for the interface, at runtime. However, the interface is required in my code because I need to run tests against a different implementation.

We've talked ourselves out of absolute necessity that testing is more important than the application code it is testing. Due to us changing our application code to conform to the needs of the tests, our hierarchy is pretty clear. Testing dictates application code, not the other way around.

## Cowboy Everything

Now, I know what you're thinking. I gotta ship my stuff because my boss said so, and because his boss said so. And you do. You do need to ship something at the end of the day. However, I would say that just "shipping code" is not good long-term thinking on any project.

Here is what is going to happen to any project that just "ships code". And I've seen this happen in real time on multiple occasions. You start something, everything goes well for a while. Then you miss a release, unexpected bugs pop up, or maybe you had an issue during a weekend. And over time, your code base becomes worse and worse to work with.

Until your whole team decides, it's time for a rewrite. Where you just repeat the same mistakes you made last time.

And if you ever reach a point where you are terrified of opening a solution or application because you know how bad it is, you've already lost at that point. That needs to be your "come to programming Jesus" moment when you or your team needs to realize what you did in the past and why that won't work going forward. And when you do, I will guarantee part of it is due to letting automated testing go to the wayside.

## Long Term

This is the range of emotions you will go through when you put automated testing first.

My life sucks, my life sucks. I just want to write code.

Oh this isn't so bad. Good thing that test back there saved my bacon on this change I made.

And finally, you'll notice that life isn't so bad.

## Whole New Programming World

Now, it's not all that terrible I promise you.

Suddenly, when we put automated testing before the application code itself a whole new programming world opens up to us. We begin to realize, "Hey, I can't do that.", and "That's not going to work anymore." a lot more than we did before.

We start to see bad techniques such as public setters, private methods, and non-pure static methods as not acceptable anymore.

Then we move onto more complex choices, black-hole service calls where you have Service A calling B, which in turn calls C becomes a lot more annoying when you have unit tests that now require five-plus mocks just to cover a single branch.

Lastly, complex conditional logic such as large switch statements, and endless if-else blocks become a hindrance to our new worldview.

## Extends Beyond Just Code

And this goes beyond just what our code is doing. We can take this idea of automated testing first, and apply it as an architecture-level principle. Suddenly, more things start to pop up.

We need to abstract out our hard dependencies such as the database. We start to incorporate more design patterns such as Chain of Responsibility, and Observer in order to shrink our dependencies.

I can't just jump onto the latest and greatest trend anymore. And lastly, we start to evaluate libraries that no longer gel with our new outlook. Libraries that embrace dynamic over static-AutoMapper I'm looking at you.

## One More

And then what?

Well, I'm going to need to run those tests. Every time I push a commit. Every time I open a PR. Every time I update my master branch.

That sounds just like a DevOps pipeline. So I'll need one of those. And of course, since we all have the attention spans of a goldfish these days, I want those tests running snappy fast. Which means I need to make that pipeline optimized so I'm not wasting half my day to see if the indicator turned green to make sure everything is good.

And all of this, all of these changes occurred, because I flipped my thinking. I said to myself, I don't care about my code. The code doesn't matter, only the tests do. I want the code, and I want it to do things. But those things are secondary, not primary.

## Saliently

1. Prioritize automated testing.
2. Let your automated tests dictate your code, not the other way around.
3. And focus on (mostly) 100% branch coverage in the critical aspects of your code base.

## Shameless Plugs

Saved the best for last, here is where you can find me.

If you want to read more about what I do; I blog @ michaelbradvica.com

If you are a dotnet developer, I have a few libraries that I maintain from my GitHub. Most of these entail giving you a foundation in your back-end code. I have done all the dirty boilerplate for you, and you get to focus on more important things. And yes, they are all 100% tested.

Any questions?
