A web of trust can have many applications in a globally distributed community. It can add some confidence when making business with people you can't meet in person.

Research existing tech to build and maintain a web of trust. Find most UX friendly software (not at the cost of security and privacy of course).

Some fundamental properties it must have:

* all data (keys and signatures) must be easy to download and sync, Git is good for this (see the #47 subtask) 
* agnostic to cryptography used, e.g. must support different key and signature types
* at the base storage layer it must be arbitrary messages signed with keys
  * things like -1/+1 trust ratings must built on top of it

Storing arbitrary messages allows to express a variety of trust relationships:

* "met in person, nice guy"
* "we had a business together, he handled difficult situations well"
* "I trust him in math domain"
* "I don't trust him in politics domain"

## Applications

* Reputation system for Decred DEX
* Decred Ambassadors (#48)

## Candidates

[qubes-secpack](https://github.com/QubesOS/qubes-secpack/tree/master/keys) subproject of Qubes OS may be a good starting point.
