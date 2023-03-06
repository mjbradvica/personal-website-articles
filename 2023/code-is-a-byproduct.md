# Code is a By-product

"Control, vision, determination. These are the three fundamental components of the new generation race car driver. Speed is a by-product."

This line from the movie "Gone in 60 Seconds" perfectly encapsulates the essence of software engineering. Randall Raines, as played by Nicholas Cage, explains to new drivers that going fast is not your main objective. If the precursors to driving are perfected, then the end result will be the speed the driver desires. However, instead of driving fast, we are concerned with producing code.

Software engineering is parallel to this concept. Replace control, vision, and determination with tooling, testing, and DevOps. These are the fundamental components of software engineering. Every successful project succeeds at these core concepts. Without these fundamentals, a modern application is gambling with success and flirting with failure.

## Tooling

Tooling may relate to any of the following:

- IDE or Editor and any respective plugins and settings files
- Documentation either done manually or automatically such as Storybook
- Code Analysis tools such as the .NET Analyzers, ESLint, or Prettier
- All formatting, linting, and code analysis is enforced via the CI/CD pipeline

## Testing

An automated suite of well written tests is the best insurance your development team can buy. A well written test suite will:

- Force your team to write testable code, requiring a heightened quality
- Provide yourself with a feedback loop for changes and refactoring
- Reduce the load on manual testing, thus reducing costs
- Enable developers to move on and off the project with a smaller risk of domain knowledge loss

## DevOps

DevOps is a large umbrella term that may contain--but is not limited to--the following:

- Source Control via Git or another tool such as SVN
- Automated pipelines that will build, test, and deploy libraries and applications
- A sanity check against self-centered development (it works on my machine)
- Separation of environments for developers, QA, and end-users to confirm feature and changes

## Control your Code or be Controlled

Failing to implement these core concepts in a project will result in project failure or an application that is severely hampered by a lack of infrastructure. As a project lead, you prioritize these topics more than the code itself--as these will dictate what code is ultimately produced and shipped.

Code is a by-product of the processes and tooling you define.

Bruckheimer, J. (Producer), & Sena, D. (Director). (2000). Gone in 60 Seconds [Motion Picture]. United States: Buena Vista Pictures
