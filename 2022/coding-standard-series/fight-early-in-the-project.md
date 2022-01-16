# Fight Early in Your Project, Then Bury the Hatchet

How many coding standard fights do you have on a project?

How many times have you had to nit-pick at pull request become someone did not "follow the rules"?

The idea answer is zero.

The next best answer is one fight, and no pull request where someone had to exercise their inner passive aggressive demons.

Tabs vs spaces.
How many indents.
Parenthesis with lambdas.

All of these are silly arguments that do nothing to provide a better product to your customer. Because ultimately the only way your product makes money is if you can deliver a good project at an acceptable price to your customer. They don't care about what line endings you choose.

## Pick a style, and stick with it

The only thing that matters is consistency in your project.

I would rather work on a project that I disagreed with every style rules that was consistent, than one that half-enforced the rules I did prefer.

Pick a set of rules and enforce them at all costs. Don't let a rogue developer who thinks, "the rules don't apply to me" to erode team morale by skirting the rules that everyone else has to adhere to.

## Use the compiler to enforce the rules

Embrace the compiler. It's a soulless entity that doesn't play favorites. A perfect vehicle to keep your team in check at all times.

You can even have it so the compiler will fail the ability to build the project unless all rules are enforced. The best way of not having to deal with an issue in a pull request is for the issue to never have the issue in the first place.

## Use the default rule if your team can't decide

Most languages already have coding standards that the teams have decided to use internally. Use these when you can't come to a conclusion about a certain rule.


## Conclusion

Fighting over coding styles and rules is an exercise in stupid. How you structure code is an internal team detail that a customer will have zero knowledge of.

* Fights over coding styles and rules are stupid
* If your going to fight over them, do it on day one then never do it again
* Use the compiler to enforce your rules
* Use the already existing rules that other teams have adopted
