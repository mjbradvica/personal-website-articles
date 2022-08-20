# Your Application Is NOT the Data(base)

If you took a survey on what your customers desire when they interact with your software, what do you think they would say?

Probably an application that is modern looking, responsive, predictable, and--crucially--not frustrating.

So, then, why are we (as developers) obsessed with the correct table associations, foreign key constraints, and ensuring everything is in proper third normal form?

This is a disconnect that engineers experience daily because we focus on what the application is doing from a development perspective instead of a customer perspective.

## Customers Don't Know What Cassandra Is

They are also blissfully unaware of Cosmos, Neo4j, Redis, or Mongo. There is no point making these technologies the focal point of the application. The customers' wants and needs should be at the center, and the database or databases that fit those needs should follow accordingly. We need to flip this engineers’ mentality on its head and think about what the customers is doing. Then we can act accordingly, instead of hoping they adapt to our decisions.

## Keep Details Out of the Database

Databases are good for one purpose: persisting knowledge in a non-volatile way. They are not designed to be substitutes for traditional programming languages. You should not have stored procedures or functions performing calculations that can be accomplished by a normal static language. Using a database as a proxy is defeating the purpose of having the wonderful tool that is available in your IDE and static analysis tools. By keeping logic out of the database, you make testing that logic far easier. In other words, writing a unit test for a language such as Java or C# is much simpler than trying to accomplish a similar task in a database. Let the database do what it does best--store data.

## Keep Details out of the Domain

Similar to how databases should have zero domain knowledge, your domain should keep details about your persistence to a minimum. The application should interact with your method of persistence through a simple interface. You should be able to swap your current means of storage for an API call, floppy disk, or stone tablet, and your code should not be able to tell the difference. The purpose of doing so leads back to testing. Application code that is sprinkled with data access logic is nigh impossible to test correctly. Hard coded dependencies should be an immediate red flag due to the inability to mock them correctly. There are small edge cases where this is unavoidable. Using certain ORM's may require you to make compromises in certain areas. However, these compromises should be kept to an absolute minimum in order to maintain the impacts of our decisions.

## Think Data Agnostic from the Start

Sometimes it’s good to kick the can down the road. Instead of jumping to premature conclusions about which technology you will use to persist data, wait until you absolutely need it. There are more important things to get right early in an application's lifecycle than the method of persistence. Architecture, domain modeling, and user experience will have a far greater impact on customer satisfaction than choosing between PostgreSQL or MySQL. Only commit to a technology when development otherwise becomes impassable; this includes persistence.
