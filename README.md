# Custom Page Fault Handling Program 

### By Nathaniel Hoaglund

## Overview of work accomplished

In this project I have created a custom page fault handling mechanism using the `userfaultfd` system call. 
This program takes in no command line arguments. When it starts it asks a user to enter in a number between 0 and 3.
Given the input number, it will trigger a page fault either on page 0, 1, 2, or 3 of the memory it is looking at.
This page fault information is then given to the server and the server returns a quote from the following movies:
0 - star wars 
1 - harry potter 
2 - devil wears prada
3 - lord of the rings 

Which then gets put into the corresponding page which caused the page fault. Once that page is in memory, it will not cause another page fault
to try and view the same quote.

## Running Instructions

To create this program, run `make build` from the command line. This will create a `fault` and `pg_server` executable.

Next run `sudo make run` which will run both `pg_server` and `fault`. It does this by running `pg_server` in the background. 
The program will ask the user to input a number between 0 and 3. If -1 is inputted the program closes. Ctrl-C can also close the program.

To then kill the server, you can run `sudo make teardown`. Both these commands require sudo permissions.

`make clean` removes the executables. Running `sudo make clean` will also terminate the server running in the background.


## List of source files
pg_fault.c
pg_fault.h
pg_server.c
pg_server.h


## Challenges and Solutions

I wanted to have the server return information based on which page faulted. It 
felt like that was more "correct" in a way. The way I did this is by taking the address of the beginning of the memory which was allocated with mmap, and then
using the page size to find out which page of that allocated memory it faulted on.
Then that information is sent over to the server and gets the corresponding page 
(which corresponds to a different quote).

## Testing

I did not write a test script but instead just did my testing manually. I tried getting the quotes in different orders, gave input which was outside the range,
running without the server (which did not work but exited somewhat gracefully), running the `fault` program multiple times with the same server. I believe that 
this was sufficient for the purpose of this assignment but for a more robust project I would create some sort of automated testing system.

Almost all memory I believe was freed right after using and file descriptors/connections closed at appropiate times. I usually use valgrind to check for 
memory errors but that did not work for the reason given in notes. 

## Notes

I found it interesting that `userfaultfd` is not registered in valgrind. Whenever I tried to use valgrind,
valgrind threw in error because it did not recognize `userfaultfd` as a valid function.

In `pg_server.c` I run the following line:
`setsockopt(server->fd, SOL_SOCKET, SO_REUSEADDR, &option, sizeof(option));`.
This line from my understanding makes it so the socket can be reused right away.
I kept running the server then making a change and closing it and the socket then 
would not be able to be used which was very frusterating. I do not believe using this is actual good practice.

I really should have done a better job commenting.

I did formatting with the `indent` command line tool. I never used it before but it seemed to work well.

## Improvement ideas

I feel like the way I forced page faults to occur could be used differently. It feels like instead of it being a true natural page fault, instead it was artificially created. I think creating a program which naturally creates page faults and fills the information would be better.

## Online resources used

My biggest two inspirations I pulled from were the `demo.c` file on the man page for `userfaultfd` and 
`project 3b` from `CIT5950` which I TA'd before. `demo.c` gave me the initial starting point and introduced me to the idea of using
the mmap flags given to force page faults at the first access. The `project 3b` was mostly for the TCP server and served as a starting point for that. I also used chatgpt very minimally but it did not prove to be very helpful to me when completing this project. 

Other than those resources, just man pages and some stack overflow posts.

I tried looking at the book `Advanced programming in the unix environment` to help me understand the mmap flags `MAP_ANONYMOUS` and `MAP_PRIVATE` but it did not fully clear it up for me.


## Final note

I really loved doing this project. I had such a good time learning about this new system call and then creating a project around it. It was fun to figure out small issues and to get better at programming. I am proud of my work and really hope that you enjoy. Thank you for taking the time to grade and I hope you enjoy it too! 
