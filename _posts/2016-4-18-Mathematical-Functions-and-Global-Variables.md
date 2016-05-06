---
title: Mathematical Functions and Global Variables
published: true
layout: post
---

I was always taught that global variables were bad - not evil - but something
to be avoided. When one of my Computer Science professors gave us some starter code for
a group programming assignment doing a 5-stage pipeline and cache simulator
with __a lot__ of global variables, I got
thinking about why I don't like them. I came up with a good answer, and it 
comes down to math, like many things.

![alt text]({{ site.baseurl }}/images/GlobalVariables.jpg "My Professor's
Global Variables")

The above picture shows 21 global variables for a 550 line program, there is
also one more unpictured and three `#define`s. You might see some variables
that make sense. `unsigned int branch_count=0;` is forgivable, it just counts
how many branch instructions were called, right? Or does it count how many branch
instructions were taken... Uh oh. _Okay, well maybe it just needs a better
variable name._ Well, yeah but, what function is responsible for
incrementing the counter? The function that parses the instruction, the
function that executes the instruction, some pipeline manipulation function?
You are probably beginning to see some problems with this implementation,
specifically about what function modified this variable, and when that
modification happens. And this is just for a simple counter. See the line
`cache_line_t *cache=NULL` at the top. Yes, that global variable is
a dynamically allocated array that holds all of the simulated cache for the
program. :open_mouth: Oh no! The answer to the question of "what function
can modify this variable?" is: "probably all of them" and the specific order and
quality of those modifications is much more unclear than a simple case of
incrementing a counter.

Okay, forget all of that for a minute for a short example. Here is a short
story that illustrates the problem with using global variables.
There is a painter who gets hired by a woman to paint her shed. The painter is
a professional and has a set of clean brushes, rollers, tape, and all the other
tools he needs, except of course the paint. The woman says, "The paint is in
the shed on the paint shelf in the spot labeled 'red paint'. So, she leaves for
work and the painter goes and gets the bucket of paint from the spot labeled 'red paint'
and starts painting. He is a little surprised that the paint is blue, since it
was in the 'red paint' spot, but the woman must know that and have left him the
color paint she wanted, even though the label doesn't correspond. He paints the
shed blue, she comes home exhausted from work and is furious that the painter
painted the shed blue. "I left the can of red paint in that spot yesterday!"
she says. "It isn't my fault," the painter says, "I did exactly as you said.
How was I supposed to know the wrong can was there?" Clearly the painter is
right, he did his job. But the woman did put the red can of paint in the spot
labeled 'red paint' there only yesterday. Unbeknownst to both of them, the
woman's husband was up late last night putting the second coat of blue paint on
his new birdhouse, and in his haste to get to sleep, he mixed up the cans of
paint when he put them back on the shelf. What a disaster! _That_ is my
problem with global variables. The paint shelf is a global variable and
entirely unnecessary. What if the woman just handed the painter the
correct bucket of paint, exactly the color she
wanted, at the beginning of the day? There wouldn't be this problem. That is
the idea of no global variables. 

### Mathematical Functions

Mathematical functions are functions that take a number of inputs and return
an output, without changing the state of anything. The output value of
a function depends only on the arguments that are input to the function, so
calling a function `f` twice with the same inputs will always return the same
outputs, regardless of the state of any global or member variables. This idea is a cornerstone of [functional programming](https://en.wikipedia.org/wiki/Functional_programming), but it can be used without all the other specifications that come along with functional programming. Take this mathematical function as an example.

~~~
f(x,y) {
  ret x + 2y 
}
~~~

It would be dumb to call `f` with a complex number, or with a pointer. It
shouldn't be `f`s job to check that the function that called it isn't dumb. `f`
should do its task assuming good input, and with bad input is free to return
bad output, or crash, or light the computer on fire. :fire:
A function in object oriented design might modify the state of some objects, or
change its output depending on the state of something, like this:

~~~
global a

f(x) {
  ret x + 2a
}
~~~

Clearly a program written of mathematical functions is less complex than one
that depends on state and global variables. Simple design is easier to debug.
It allows the function to be freed from checking inputs and states of global
or member variables. A function can assume that its arguments are valid, since
it is the parent function's responsibility to pass in valid variables.

### Functions shouldn't rely on globals

Functions that don't depend on global variables are safer, simpler, and more
modular because they can assume valid input. It is a calling function's
responsibility to pass in good input, thereby freeing the called function from
the responsibility of checking in more detail than the type system can. Once
a function has been written and it's corner cases tested, it is easier to
narrow down the source of bugs in a program.


