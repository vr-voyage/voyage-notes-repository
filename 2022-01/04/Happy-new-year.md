---
date: 2022-01-04
---

# Happy new year !

Greetings everyone !

To begin, I hope you had some nice time during
christmas and the new year, and I wish you a
happy new year !

Now, the next update will be on the Map Maker,
since I found, at last, the reason why the
normals in TextureArray were behaving in a
weird fashion, compared to the single textures
versions.

I will create a post about it, since it actually
required me to use RenderDoc to get to the
bottom of this, and there's still one thing that
isn't completely resolved, to get exactly the
same expected.  
It appears that the color keywords defaults, for
texture parameters in shaders, are linked to
internal textures containing a single pixel
colored the same way, but for whatever reason,
Unity will create default gray 2D Array textures,
but no white 2D Array textures.

Then, the next updates will be SharpLogix, since
I have a good idea about how to avoid using
registers read/write calls for every local read/
write, while still using before and after code
blocks, in order to sync with any value changed
in these blocks, before continuing with the rest
of the code.  
The system will rely on some kind of "checkpoint"
system.  
Basically, if a variable is modified inside a child
block (any depth), then a register read/write is
placed before the code block, where the current
value of the local variable is written, then at
the end of the child blocks, the value is also
written into the checkpoint.  
At last, after the blocks, the local variable resumes
from a read from that register.  
This should make the code readable, and avoid using
a register per local variable assignment, which is
insane.

Meanwhile, I'm trying to put in place a few small
fixes for VRM models import on Unity, for VRChat
mainly, but that might also help fix them in
NeosVR as well.  
These fixes are mostly to avoid simple Full-Body
Tracking issues.

Speaking of VRM, after SharpLogix, I'll try to
update the Quick Avatar Setup editor, to support
VRM models out-of-the-box, and also start making
the software a bit more "user-friendly".  
Which mean actual camera controls and default zoom
on the head.  
I'll see if I have enough time to add blendshapes
categorization, which is extremely useful for
when recreating the animations, but might take some
time to do it right.

So, stay tuned !
