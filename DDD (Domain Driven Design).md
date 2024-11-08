-  DDD
	- Means our software design is driven by a **Domain** 
		- **Domain** is the subject area around which the application that is being developed is centered
		- Always good to know the context in which our application applies
		- To deal with large domains, you can divide the model you have into different zones called Bounded Context
			- each zone operates within its own context, and have its own domain experts
			- Allows for the work to be easier and better organized
	- an approach to developing the software that deeply connects the implementation to the core business concepts or to your own domain
- Should only apply to large scale projects or large organizations
	- Small projects do not need this kinda of design
- An important rule for DDD is communication, one way to achieve that is to use Ubiquitous Language.
	- Language shared by the dev team and the Domain experts
	- language used in the discussion, in the domain model, in the code of the app, in the classes, in the methods
		- An example of this can be with the word ==client==
			- **client** could mean a human identity, with a username and password used to authenticate
			- **client** could also mean a system service which consumes an API
		- Another example can be ==booking==
			- **booking** reserving a spot or a time for a particular event
			- **booking** a player being punished by the referee for a foul play
	- Makes sure to clearly define the meaning of each word and term being used
	- domains must have their own ubiquitious language, preventing them from polluting each other
- Anti-corruption layer
	- protects your domain from outer influences
	- helps translate between two domain models and if often implemented by well-known patterns

			UI <-------> Application
							^
							|
							|
							|
							Domain ------> Infrastructure
							|
							|
							|
							V
							Repostiories

| Pros                                                                      | Cons                                                                                                                         |
| ------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| Similar communication because of Ubiquitous Lanuage                       | Domain knowledge required, there must be at least one domain specialist who understands the subject at the center of the app |
| DDD is Object- Oriented, making i flexible and easier to modify / improve | Does not work for highly technical projects, also might not be best for apps with minor domain involvement                   |

		