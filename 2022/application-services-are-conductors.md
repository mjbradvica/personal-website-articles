# Application Services Are Conductors

In an orchestra, the role of the conductor is to organize the music. This includes starting and stopping the musicians in synchronization, doing justice to the music, and cueing the musicians to play. A conductors' job is not to tell a violinist or cellist how to play his or her instrument. The conductor does not micro-manage.

An application service's main role is to encapsulate a user-story. A user-story is no different than any piece of music with structure or movements. Like a symphony, not all pieces of a user-story play at the same time. The application service takes on the role of the conductor. Using an interface transitions to the next note. Cueing up the brass section turns into calling a method. Completing the user-story signals the end of the music.

The purpose of treating user-stories this way is testing. Application services that minimize "logic" in their structure are easier to test and maintain. Reducing logic means fewer unit tests, due to fewer logical branches. Because every logical branch in our code requires a test, removing logic makes better music. Application services are some of the hardest pieces to test because they require mocking dependencies. Limiting multi-mocks is the fastest way to create a masterpiece.

Whenever you are implementing a user story, you become Bernstein, Berlioz, and Stokowski. Interfaces become the strings, objects become woodwinds, and methods the brass. As the conductor, these are the tools at your disposal to complete the task at hand. Organize them, and keep them in sync. But refrain from telling them how to do their job.
