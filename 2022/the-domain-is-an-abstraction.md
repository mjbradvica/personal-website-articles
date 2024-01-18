# The Domain Is An Abstraction

One of the mistakes we make as software developers is conflating the way we express our domain with the way we persist it.

The domain is an abstraction.

We talk and think about our domain in terms of objects and formal nouns. We persist in the form of basic types.

## What's In A Name?

In our domain a name is a rich object because it has rules around it and must exist in certain states-such as a first and last name must be present. The way we persist our name object is of no consequence to our method of persistance. All the persistance layer sees is two strings in a single table.
