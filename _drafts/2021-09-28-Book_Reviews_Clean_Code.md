---
layout: post
title: Create interactive game with Spring Boot and WebSocket
comments: false
categories: [ Spring boot ]
tags: [WebSocket, game , interactive]
image: assets/images/spring-websocket.png
excerpt: In this post, I'm going to show you how to create a 
         WebSocket server in Java using Spring Boot, we will learn these together while 
         developing the two-player Mancala game ...


---

#Chapter 2 Meaningful names
don't remember: You should name a variable using the same care with which you name a first-born child. :)
As a Programmer, you are not only responsible for satisfying a compiler or interpreter but also you're responsible for someone who wants reading and understanding your code.
so try to use some names that reveal your intention to reader, when choose a name think about is it helpful and meaningful for readers or is it contain some disinformation and misled readers? 

“Well, over here on the bee cee arr three cee enn tee we have a pee ess zee kyew int, see?”  :) This matters because programming is a social activity so use pronounceable names.

All of us these days use powerful IDEs for programing. so use searchable names and don’t Add Gratuitous Context to all classnames! Why make it hard for the IDE to help you?

Classes and objects should have noun or noun phrase names like Customer, WikiPage, Account, and AddressParser. Avoid words like Manager, Processor, Data, or Info in the name of a class. A class name should not be a verb.

Methods should have verb or verb phrase names like postPayment, deletePage, or save. Accessors, mutators, and predicates should be named for their value and prefixed with get, set, and is according to the javabean standard
When constructors are overloaded, use static factory methods with names that describe the arguments.

Avoid using the same word for two purposes. Using the same term for two different ideas is essentially a pun.

first try to use Solution Domain Names then try to use Problem Domain Names
Separating solution and problem domain concepts is part of the job of a good programmer and designer. The code that has more to do with problem domain concepts should have names drawn from the problem domain

The hardest thing about choosing good names is that it requires good descriptive skills and a shared cultural background.

#3- Functions
Functions are the first line of organization in any program.
The first rule of functions is that they should be small. The second rule of functions is that they should be smaller than that.

This implies that the blocks within if statements, else statements, while statements, and so on should be one line long. Probably that line should be a function call. Not only does this keep the enclosing function small, but it also adds documentary value because the function called within the block can have a nicely descriptive name.
This also implies that functions should not be large enough to hold nested structures. Therefore, the indent level of a function should not be greater than one or two. This, of course, makes the functions easier to read and understand.

FUNCTIONS SHOULD DO ONE THING. THEY SHOULD DO IT WELL. THEY SHOULD DO IT ONLY.

So, another way to know that a function is doing more than “one thing” is if you can extract another function from it with a name that is not merely a restatement of its implementation


Sections within Functions
Look at Listing 4-7 on page 71. Notice that the generatePrimes function is divided into sections such as declarations, initializations, and sieve. This is an obvious symptom of doing more than one thing. Functions that do one thing cannot be reasonably divided into sections.

In order to make sure our functions are doing “one thing,” we need to make sure that the statements within our function are all at the same level of abstraction.

Mixing levels of abstraction within a function is always confusing. Readers may not be able to tell whether a particular expression is an essential concept or a detail. Worse, like broken windows, once details are mixed with essential concepts, more and more details tend to accrete within the function.

We want the code to read like a top-down narrative.5 We want every function to be followed by those at the next level of abstraction so that we can read the program, descending one level of abstraction at a time as we read down the list of functions. I call this The Step-down Rule.

Switch
My general rule for switch statements is that they can be tolerated if they appear only once, are used to create polymorphic objects, and are hidden behind an inheritance relationship so that the rest of the system can’t see them [G23]. Of course every circumstance is unique, and there are times when I violate one or more parts of that rule.

Use Descriptive Names
Remember Ward’s principle: “You know you are working on clean code when each routine turns out to be pretty much what you expected.”
Half the battle to achieving that principle is choosing good names for small functions that do one thing.
Don’t be afraid to make a name long. A long descriptive name is better than a short enigmatic name.
A long descriptive name is better than a long descriptive comment.

####Function Arguments
The ideal number of arguments for a function is zero (niladic). Next comes one (monadic), followed closely by two (dyadic). Three arguments (triadic) should be avoided where possible. More than three (polyadic) requires very special justification—and then shouldn’t be used anyway.

The argument is at a different level of abstraction than the function name and forces you to know a detail.
Arguments are even harder from a testing point of view. Imagine the difficulty of writing all the test cases to ensure that all the various combinations of arguments work properly. If there are no arguments, this is trivial. If there’s one argument, it’s not too hard. With two arguments the problem gets a bit more challenging. With more than two arguments, testing every combination of appropriate values can be daunting.

Common Monadic Forms

There are two very common reasons to pass a single argument into a function. You may be asking a question about that argument, as in boolean fileExists(“MyFile”). Or you may be operating on that argument, transforming it into something else and returning it. For example, InputStream fileOpen(“MyFile”) transforms a file name String into an InputStream return value. These two uses are what readers expect when they see a function. You should choose names that make the distinction clear, and always use the two forms in a consistent context.
A somewhat less common, but still very useful form for a single argument function, is an event. In this form there is an input argument but no output argument. The overall program is meant to interpret the function call as an event and use the argument to alter the state of the system, for example, void passwordAttemptFailedNtimes(int attempts). Use this form with care. It should be very clear to the reader that this is an event. Choose names and contexts carefully.
Try to avoid any monadic functions that don’t follow these forms

Flag Arguments

Flag arguments are ugly. Passing a boolean into a function is a truly terrible practice. It immediately complicates the signature of the method, loudly proclaiming that this function does more than one thing. It does one thing if the flag is true and another if the flag is false!

Dyadic Functions
A function with two arguments is harder to understand than a monadic function
Even obvious dyadic functions like assertEquals(expected, actual) are problematic. How many times have you put the actual where the expected should be? The two arguments have no natural ordering. The expected, actual ordering is a convention that requires practice to learn.


Triads

Functions that take three arguments are significantly harder to understand than dyads. The issues of ordering, pausing, and ignoring are more than doubled. I suggest you think very carefully before creating a triad.
this is a good example to change a triads to dyads:
Circle makeCircle(double x, double y, double radius);
Circle makeCircle(Point center, double radius);


Verbs and Keywords

Choosing good names for a function can go a long way toward explaining the intent of the function and the order and intent of the arguments.


Have No Side Effects

Output Arguments

In general output arguments should be avoided. If your function must change the state of something, have it change the state of its owning object

Command Query Separation

Functions should either do something or answer something, but not both. Either your function should change the state of an object, or it should return some information about that object. Doing both often leads to confusion.

Prefer Exceptions to Returning Error Codes

When you return an error code, you create the problem that the caller must deal with the error immediately.
On the other hand, if you use exceptions instead of returned error codes, then the error processing code can be separated from the happy path code and can be simplified.

Extract Try/Catch Blocks

Try/catch blocks are ugly in their own right. They confuse the structure of the code and mix error processing with normal processing. So it is better to extract the bodies of the try and catch blocks out into functions of their own.

Error Handling Is One Thing

When you use exceptions rather than error codes, then new exceptions are derivatives of the exception class. They can be added without forcing any recompilation or redeployment.

Don’t Repeat Yourself
Duplication may be the root of all evil in software. Many principles and practices have been created for the purpose of controlling or eliminating it. Consider, for example, that all of Codd’s database normal forms serve to eliminate duplication in data. Consider also how object-oriented programming serves to concentrate code into base classes that would otherwise be redundant. Structured programming, Aspect Oriented Programming, Component Oriented Programming, are all, in part, strategies for eliminating duplication. It would appear that since the invention of the subroutine, innovations in software development have been an ongoing attempt to eliminate duplication from our source code.

Structured Programming
if you keep your functions small, then the occasional multiple return, break, or continue statement does no harm and can sometimes even be more expressive than the single-entry, single-exit rule. On the other hand, goto only makes sense in large functions, so it should be avoided.


How Do You Write Functions Like This?
Writing software is like any other kind of writing. When you write a paper or an article, you get your thoughts down first, then you massage it until it reads well. The first draft might be clumsy and disorganized, so you wordsmith it and restructure it and refine it until it reads the way you want it to read.
When I write functions, they come out long and complicated. They have lots of indenting and nested loops. They have long argument lists. The names are arbitrary, and there is duplicated code. But I also have a suite of unit tests that cover every one of those clumsy lines of code.
So then I massage and refine that code, splitting out functions, changing names, eliminating duplication. I shrink the methods and reorder them. Sometimes I break out whole classes, all the while keeping the tests passing.
In the end, I wind up with functions that follow the rules I’ve laid down in this chapter. I don’t write them that way to start. I don’t think anyone could.

Conclusion
Master programmers think of systems as stories to be told rather than programs to be written. They use the facilities of their chosen programming language to construct a much richer and more expressive language that can be used to tell that story. Part of that domain-specific language is the hierarchy of functions that describe all the actions that take place within that system. In an artful act of recursion those actions are written to use the very domain-specific language they define to tell their own small part of the story.

####Comment
Don’t comment bad code—rewrite it.”
—Brian W. Kernighan and P. J. Plaugher
One of the more common motivations for writing comments is bad code. We write a module, and we know it is confusing and
disorganized. We know it’s a mess. So we say to ourselves, “Ooh, I’d better comment that!” No! You’d better clean it!
Rather than spend your time writing the comments that explain the mess you’ve made, spend it cleaning that mess.

comments are, at best, a necessary evil. If our programming languages were expressive enough, or if we had the talent to
subtly wield those languages to express our intent, we would not need comments very much—perhaps not at all.
Programmers can’t realistically maintain comments.

Truth can only be found in one place: the code. Only the code can truly tell you what it does. It is the only source of 
truly accurate information. Therefore, though comments are sometimes necessary, we will expend significant energy to minimize them.

#####Good Comments

that the only truly good comment is the comment you found a way not to write.
Sometimes it is just helpful to translate the meaning of some obscure argument or return value into something that’s readable.
In general, it is better to find a way to make that argument or return value clear in its own right; but when its part 
of the standard library, or in code that you cannot alter, then a helpful clarifying comment can be useful.

TODO Comments
Whatever else a TODO might be, it is not an excuse to leave bad code in the system.


#####Bad Comments
Most comments fall into this category. Usually they are crutches or excuses for poor code or justifications for 
insufficient decisions, amounting to little more than the programmer talking to himself

Mumbling

Any comment that forces you to look in another module for the meaning of that comment has failed to communicate to you 
and is not worth the bits it consumes.

Redundant Comments

It's about comments probably take longer to read than the code itself. It’s certainly not more informative than the code.

Misleading Comments

Sometimes, with all the best intentions, a programmer makes a statement in his comments that isn’t precise enough to be
accurate. The result is comment that is harder to read than the body of the code.

Mandated Comments

It is just plain silly to have a rule that says that every function must have a javadoc, or every variable must have a 
comment. Comments like this just clutter up the code, propagate lies, and lend to general confusion and disorganization.

Journal Comments

Remember and repeat version control do it better

Noise Comments

say obvious thing : //default constractor really? :)
Replace the temptation to create noise with the determination to clean your code. You’ll find it makes you a better 
and happier programmer.

Scary Noise

Javadocs can also be noisy. What purpose do the following Javadocs (from a well-known open-source library) serve? 
Answer: nothing. They are just redundant noisy comments written out of some misplaced desire to provide documentation.

Position Markers
Think of it this way. A banner is startling and obvious if you don’t see banners very often. So use them very sparingly,
and only when the benefit is significant. If you overuse banners, they’ll fall into the background noise and be ignored.

Closing Brace Comments
if you find yourself wanting to mark your closing braces, try to shorten your functions instead.

Attributions and Bylines
There is no exception, The source code control system is a better place for this kind of information.

Commented-Out Code
Few practices are as odious as commenting-out code. Don’t do this!
Others who see that commented-out code won’t have the courage to delete it. They’ll think it is there for a reason 
and is too important to delete. So commented-out code gathers like dregs at the bottom of a bad bottle of wine.


Inobvious Connection
The connection between a comment and the code it describes should be obvious. If you are going to the trouble to write
a comment, then at least you’d like the reader to be able to look at the comment and the code and understand what the 
comment is talking about.
It is a pity when a comment needs its own explanation

Function Headers
Short functions don’t need much description. A well-chosen name for a small function that does one thing is usually better than a comment header.

golden rule: Don’t Use a Comment When You Can Use a Function or a Variable.Believe Most of the time it's worked fine.


###Formatting
You should take care that your code is nicely formatted. You should choose a set of simple rules that govern the format of your code, and then you should consistently apply those rules. If you are working on a team, then the team should agree to a single set of formatting rules and all members should comply. It helps to have an automated tool that can apply those formatting rules for you.

Perhaps you thought that “getting it working” was the first order of business for a professional developer. I hope by now, however, that this book has disabused you of that idea. The functionality that you create today has a good chance of changing in the next release, but the readability of your code will have a profound effect on all the changes that will ever be made. The coding style and readability set precedents that continue to affect maintainability and extensibility long after the original code has been changed beyond recognition. Your style and discipline survives, even though your code does not.

So what are the formatting issues that help us to communicate best?
 
####Vertical Formatting
What does that mean to us? It appears to be possible to build significant systems (FitNesse is close to 50,000 lines) out of files that are typically 200 lines long, with an upper limit of 500. Although this should not be a hard and fast rule, it should be considered very desirable. Small files are usually easier to understand than large files are.

#####The Newspaper Metaphor
We would like a source file to be like a newspaper article. The name should be simple but explanatory.
The name, by itself, should be sufficient to tell us whether we are in the right module or not.
The topmost parts of the source file should provide the high-level concepts and algorithms.
Detail should increase as we move downward, until at the end we find the lowest level functions and 
details in the source file.

#####Vertical Openness Between Concepts
Nearly all code is read left to right and top to bottom. Each line represents an expression or a clause,
and each group of lines represents a complete thought. Those thoughts should be separated from each other
with blank lines.

#####Vertical Distance
Concepts that are closely related should be kept vertically close to each other. Clearly this rule doesn’t
work for concepts that belong in separate files. But then closely related concepts should not be separated 
into different files unless you have a very good reason.

#####Variable Declarations. 
Variables should be declared as close to their usage as possible. Because our functions are very short, 
local variables should appear at the top of each function.
Control variables for loops should usually be declared within the loop statement, as in this cute little 
function from the same source.

Instance variables, on the other hand, should be declared at the top of the class. This should not increase
the vertical distance of these variables, because in a well-designed class, they are used by many, 
if not all, of the methods of the class.
The important thing is for the instance variables to be declared in one well-known place. 
Everybody should know where to go to see the declarations.

Dependent Functions. If one function calls another, they should be vertically close, 
and the caller should be above the callee, if at all possible. This gives the program a natural flow. 
If the convention is followed reliably, readers will be able to trust that function definitions will follow 
shortly after their use. 

Conceptual Affinity. Certain bits of code want to be near other bits. They have a certain conceptual affinity.
The stronger that affinity, the less vertical distance there should be between them.
this affinity might be based on a direct dependence, such as one function calling another, 
or a function using a variable. But there are other possible causes of affinity. 
Affinity might be caused because a group of functions performs a similar operation.

#####Vertical Ordering

In general, we want function call dependencies to point in the downward direction. 
That is, a function that is called should be below a function that does the calling.
This creates a nice flow down the source code module from high level to low level.

As in newspaper articles, we expect the most important concepts to come first, 
and we expect them to be expressed with the least amount of polluting detail. 
We expect the low-level details to come last. This allows us to skim source files, 
getting the gist from the first few functions, without having to immerse ourselves in the details.


#### Horizontal Formatting
Programmers clearly prefer short lines. we should strive to keep our lines short.
I used to follow the rule that you should never have to scroll to the right. But monitors are too wide 
for that nowadays, and younger programmers can shrink the font so small that they can get 200 characters 
across the screen. Don’t do that. I personally set my limit at 120.

##### Horizontal Openness and Density
We use horizontal white space to associate things that are strongly related and disassociate things that are more weakly related.
Unfortunately, most tools for reformatting code are blind to the precedence of operators and impose the same spacing 
throughout. So subtle spacings like those shown above tend to get lost after you reformat the code.

(-b - Math.sqrt(determinant)) / (2*a); 

##### Horizontal Alignment
that this kind of alignment is not useful. The alignment seems to emphasize the wrong things and leads my eye away 
from the true intent.

##### Indentation
To make this hierarchy of scopes visible, we indent the lines of source code in proportion to their position in the hiearchy. Statements at the level of the file, such as most class declarations, are not indented at all. Methods within a class are indented one level to the right of the class. Implementations of those methods are implemented one level to the right of the method declaration. Block implementations are implemented one level to the right of their containing block, and so on.

Breaking Indentation. It is sometimes tempting to break the indentation rule for short if statements, 
short while loops, or short functions. Whenever I have succumbed to this temptation, I have almost always gone back and
put the indentation back in.

#### Team Rules
The title of this section is a play on words. Every programmer has his own favorite formatting rules, but if he works in a team, then the team rules.

A team of developers should agree upon a single formatting style, and then every member of that team should use that style. We want the software to have a consistent style. We don’t want it to appear to have been written by a bunch of disagreeing individuals.

Remember, a good software system is composed of a set of documents that read nicely. They need to have a consistent and smooth style. The reader needs to be able to trust that the formatting gestures he or she has seen in one source file will mean the same thing in others. The last thing we want to do is add more complexity to the source code by writing it in a jumble of different individual styles.


## Objects and Data Structures

#### Data Abstraction
Hiding implementation is not just a matter of putting a layer of functions between the variables.
Hiding implementation is about abstractions! A class does not simply push its variables out through
getters and setters. Rather it exposes abstract interfaces that allow its users to manipulate the 
essence of the data, without having to know its implementation.

We do not want to expose the details of our data. Rather we want to express our data in abstract terms. This is not merely accomplished by using interfaces and/or getters and setters. Serious thought needs to be put into the best way to represent the data that an object contains. 
The worst option is to blithely add getters and setters.

#### Data/Object Anti-Symmetry
Objects hide their data behind abstractions and expose functions that operate on that data. 
Data structure expose their data and have no meaningful functions. Go back and read that again. 
Notice the complimentary nature of the two definitions. They are virtual opposites. 
This difference may seem trivial, but it has far-reaching implications.

Procedural code (code using data structures) makes it easy to add new functions without changing 
the existing data structures. OO code, 
on the other hand, makes it easy to add new classes without changing existing functions.

Procedural code makes it hard to add new data structures because all the functions must change. 
OO code makes it hard to add new functions because all the classes must change.

So, the things that are hard for OO are easy for procedures, 
and the things that are hard for procedures are easy for OO!

In any complex system there are going to be times when we want to add new data types rather 
than new functions. For these cases objects and OO are most appropriate. On the other hand, 
there will also be times when we’ll want to add new functions as opposed to data types.
In that case procedural code and data structures will be more appropriate.

Mature programmers know that the idea that everything is an object is a myth. 
Sometimes you really do want simple data structures with procedures operating on them.

#### The Law of Demeter
There is a well-known heuristic called the Law of Demeter2 that says a module should 
not know about the innards of the objects it manipulates. As we
saw in the last section, objects hide their data and expose operations. 
This means that an object should not expose its internal structure through 
accessors because to do so is to expose, rather than to hide, its internal structure.

More precisely, the Law of Demeter says that a method f of a class C should only call the methods of these:

• C

• An object created by f

• An object passed as an argument to f

• An object held in an instance variable of C

#### Train Wrecks
final String outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();
Whether this is a violation of Demeter depends on whether or not ctxt, Options, 
and ScratchDir are objects or data structures. If they are objects, then their internal 
structure should be hidden rather than exposed, and so knowledge of their innards is a clear 
violation of the Law of Demeter. On the other hand, if ctxt, Options, and ScratchDir are 
just data structures with no behavior, 
then they naturally expose their internal structure, and so Demeter does not apply.

Options opts = ctxt.getOptions();
File scratchDir = opts.getScratchDir();
final String outputDir = scratchDir.getAbsolutePath();

##### Hybrids
This confusion sometimes leads to unfortunate hybrid structures that are half object and 
half data structure.
Such hybrids make it hard to add new functions but also make it hard to add new data structures.
They are the worst of both worlds. Avoid creating them. They are indicative of a muddled design 
whose authors are unsure of—or worse, 
ignorant of—whether they need protection from functions or types.

##### Hiding Structure
What if ctxt, options, and scratchDir are objects with real behavior?
ctxt.getAbsolutePathOfScratchDirectoryOption();
or
ctx.getScratchDirectoryOption().getAbsolutePath()

The first option could lead to an explosion of methods in the ctxt object. 
The second presumes that getScratchDirectoryOption() returns a data structure, 
not an object. Neither option feels good.

If ctxt is an object, we should be telling it to do something; we should not be asking it 
about its internals. So why did we want the absolute path of the scratch directory? 
What were we going to do with it? Consider this code from 
(many lines farther down in) the same module:

String outFile = outputDir + “/” + className.replace('.', '/') + “.class”;
FileOutputStream fout = new FileOutputStream(outFile);
BufferedOutputStream bos = new BufferedOutputStream(fout);
->
BufferedOutputStream bos = ctxt.createScratchFileStream(classFileName);

That seems like a reasonable thing for an object to do! This allows ctxt to hide its internals 
and prevents the current function from having to violate the Law of Demeter by navigating through 
objects it shouldn’t know about.


#### Data Transfer Objects
The quintessential form of a data structure is a class with public variables and no functions. This is sometimes called 
a data transfer object, or DTO. 

#### Active Record
Active Records are special forms of DTOs. They are data structures with public 
(or bean-accessed) variables; but they typically have navigational methods like save and find. 
Typically these Active Records are direct translations 
from database tables, or other data sources.

Unfortunately we often find that developers try to treat these data structures as 
though they were objects by putting business rule methods in them. This is awkward because 
it creates a hybrid between a data structure and an object.

The solution, of course, is to treat the Active Record as a data structure and to create 
separate objects that contain the business rules and that hide their internal data 
(which are probably just instances of the Active Record).


####Conclusion

Objects expose behavior and hide data. This makes it easy to add new kinds of objects without changing existing 
behaviors. It also makes it hard to add new behaviors to existing objects. Data structures expose 
data and have no significant behavior. This makes it easy to add new behaviors to existing data 
structures but makes it hard to add new data structures to existing functions.

In any given system we will sometimes want the flexibility to add new data types, 
and so we prefer objects for that part of the system. Other times we will want the 
flexibility to add new behaviors, and so in that part of the system we prefer data types 
and procedures. Good software developers understand these issues without prejudice and 
choose the approach that is best for the job at hand.

other good resources:
https://stackoverflow.com/questions/23406307/whats-the-difference-between-objects-and-data-structures






















