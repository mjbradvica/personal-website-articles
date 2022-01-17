# Establish Coding Standards Early

Tabs vs Spaces
Semicolon placement
Parenthesis around lambdas

These are just a few examples, but how many altercations regarding coding standards have you had on your projects?

How many times have you had to comment a pull request because someone did not follow the rules?

The ideal answer is zero.

We get to zero when we establish standards and rules early. After all, languages are based on standardization of meaning--this includes code.

Minimizing disagreements in pull requests exorcises the inner passive-aggressive demons that otherwise may arise.

Arguing over coding standards improves neither product nor experience. Ultimately, delivering a good product at an acceptable price to your customer should take priority. Customers don't care about what line endings you choose, as long as you choose in the beginning.

## Pick a Style, and Stick with It

The only thing that matters is consistency in your project.

We don't have to agree with every rule, or even like it, but we must stay consistent.

Pick a set of rules, then enforce them at all costs. Don't let a rogue developer erode your team's morale by skirting the rules that everyone else must follow.

## Use the Compiler to Enforce the Rules

Embrace the compiler. It's a soulless entity that doesn't play favorites--a perfect vehicle to keep your team in check at all times.

You can even make it so that the compiler will fail to build the project unless all rules are enforced. Save time and money by having the compiler do the dirty work for you.

## Use the Default Rule if Your Team Can't Decide

Most language teams readily publish a set of recommended rules. Reference these when your own team can't decide on a certain rule. ESLint has a [recommended](https://eslint.org/docs/rules/) ruleset. Microsoft [publishes](https://docs.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions) their recommended code rules. There's always one who will fall on a sword or die on a hill for a trivial rule. If you are unable to reach a conclusion, agree ahead of time to defer to the language standard.

## Conclusion

Fighting over coding styles and rules is an exercise in futility. How you structure your code is entirely  an internal team detail. Air your grievances early, then hit the ground coding. If your team is unable to come to a conclusion, then defer to the standard for that language or framework.
