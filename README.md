pngee
=====

Minimal PNG implementation, with a little bit of focus on learning how to make things easy to understand, optimize and secure.


Motivation
----------

This happened when I tried to find out why writing PNG images from GIMP or pretty much any other software on my notebook was so darn slow.
I git clone'd the libpng repo, and read the first page of documentation:

> When libpng encounters an error, it expects to longjmp back
> to your routine.  Therefore, you will need to call setjmp and pass
> your png_jmpbuf(png_ptr).  If you read the file from different
> routines, you will need to update the longjmp buffer every time you enter
> a new routine that will call a png_*() function.
> 
> See your documentation of setjmp/longjmp for your compiler for more
> information on setjmp/longjmp.  See the discussion on libpng error
> handling in the Customizing Libpng section below for more information
> on the libpng error handling.  If an error occurs, and libpng longjmp's
> back to your setjmp, you will want to call png_destroy_read_struct() to
> free any memory.

That kind of sounded like my worst programming nightmare, because it means two things:

1. I have to set my own jump targets. I mean, this is a nice idea for error handling, but it's not the early 60's anymore, so I might not expect the C routines I call to return anywhere but at the point I called it.
2. If for any reason someone else (ie. another thread in the same process) happens to change that pointer, we'll end up in hell. Like, I-can't-even-debug-this-hell. Oh by the way, can you imagine what happens if someone is able to manipulate that and then cause a libpng error?

Obviously, you can't use a library like that in multithreaded applications. But everyone does. Why? I guess it's a case of "it's there, and it works", and libpng even boast about being time-tested. Since that is true, and everyone else is using libpng, I figured it must be really hard to write a PNG writing library. Therefore, I had to try.
