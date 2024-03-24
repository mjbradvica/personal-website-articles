# Get Insured

## Title

Hello Everyone,

Thanks for having me this evening, shout out to Brian and the rest of everyone who makes ODevs a reality. This isn't free money or time-wise to put on to host. So I appreciate everything you do.

This talk is technically a part 1 of 2. Assuming that Brian doesn't hate me in the end--I'll do the second part at a future meetup.

The name of this talk is, "Get Insured, Or crash the Car", and yes, I will be asking you if you would like to buy an extended warranty for your vehicle today.

Except in this case, the vehicle in question is your application or suite of applications your company maintains.

## Personal

Real quick, who am I?

My name is Michael Bradvica (my last name is Croatian in case you weren't wondering), I'm a full-stack developer at the theme here in town with the dolphins and animals. It's the one that starts with a "U".

And below you can see the worthless pieces of paper that I hold.

I'll save the shameless plugs for later on in the presentation when I can keep the slide up during Q&A.

## Humans and Risk

Does anyone here have any experience in insurance, risk management, or asset protection?

(No), ok, so what is the point of risk management? What is at the very core of its purpose?

The protection against loss of assets, that, if lost, would result in a catastrophic loss of working capital, possibly destroy our cash flow, from which we may never recover. (So then what? We yolo the rest of our money into BitCoin and cross our fingers? That's not financial advice btw)

Humans, even back in the "ooga-booga" days realized this. As _exaggerate_ far back as Hammurabi, thousands of years ago, rules and regulations were dictating the handling and outcomes of goods and property in the event of adverse conditions.

My favorite of which, is if you built someone a house and the big bad wolf blows it down, you as the builder, "get the chair".

More importantly, there was one of the first instances of disaster relief. If you owned money on a load or working capital expense and something happened (act of god, injury, someone dies of dysentery) your debt was relieved or forgiven.

As a shipping merchant, one could take out a load, and pay the lender an extra fee, and if someone happened to the shipment, the merchant was not personally liable. One of the first instances of maritime insurance.

## Application Service

So we as software engineers, deal in code right? We let the boring insurance salesmen deal with their policies and we have our code babies to watch over.

What we have here is a generic application-level service. This would sit between whatever your presentation framework and persistence framework would be.

Side note, this is c-sharp, but I'm not here to talk about c-sharp. So just imagine it being in whatever your stack is.

I have a small service, called "widget service" and we are taking in a request, modifying some internal fields, updating the persistence model, and returning if the operation was a success or not. Like I said, the code here doesn't matter. More importantly, it's what code _means_ to my organization that matters.

So we as code shipping merchants, have our payload, the widget service. We need to ensure (with an E) that it gets the point A to point B without sinking, being abducted by pirates, or bis swallowed up by Poseidon himself.

So what do we do? We need to make sure that the code I've written does what I think it will do.

## Application Service Tests

We write a unit test!

And here it is. This is just the first of three unit tests that are required for this service. My function had three logical branches, one to check for a null entity, and the final status check at the end. But, I only have room here for one. So you get one. But just imagine I have two more here.

## Shameless Plugs

If you want to read more about what I do; I blog @ michaelbradvica.com

If you are a dotnet developer, I have a few libraries that I maintain from my GitHub. Most of these entail giving you a foundation in your back-end code. I have done all the dirty boilerplate for you, and you get to focus on more important things.


