NetBSD_6.1_Stable
=================

Kernfs and Procfs for netbsd 6.1 (Stable Branch)

kernfs is a virtual file system that reports information about the running system,
and in some cases allows adjusting this information. procfs is a virtual file system 
that provides information about currently running processes. Both of these file systems
work by exposing virtual files containing textual data.

-- Guidelines of the project

- kernfs and procfs should share most of their code, and in particular they should share
all the code for managing lists of virtual files. They should remain separate entities,
however, at least from the user perspective: community consensus is that mixing system and 
per-process data, as Linux always has, is ugly.

- It should be possible to add and remove entries on the fly, e.g. as modules are loaded and unloaded.

- Because userlevel programs can become dependent on the format of the virtual files 
(Linux has historically had compatibility problems because of this) they should if possible
not have complex formats at all, and if they do the format should be clearly specifiable in 
some way that isn't procedural code. (This makes it easier to reason about, and harder for it t
o get changed by accident.)

- There is an additional interface in the kernel for retrieving and adjusting arbitrary
kernel information: sysctl. Currently the sysctl code is a third completely separate mechanism,
on many points redundant with kernfs and/or procfs. It is somewhat less primitive, but the current
implementation is cumbersome and not especially liked. Integrating kernfs and procfs with sysctl 
(perhaps somewhat like the Linux sysfs) is not automatically the right design choice, but it is 
likely to be a good idea. At a minimum we would like to be able to have one way to handle
reportable/adjustable data within the kernel, so that kernfs, procfs, and/or sysctl can be attached
to any particular data element as desired.

- While most of the implementations of things like procfs and sysctl found in the wild 
(including the ones we currently have) work by attaching callbacks, and then writing code
all over the kernel to implement the callback API, it is possible to design instead to attach
data, that is, pointers to variables within the kernel, so that the kernfs/procfs or sysctl 
code itself takes responsibility for fetching that data. Please consider such a design strongly 
and pursue it if feasible, as it is much tidier. (Note that attaching data also in general requires 
specifying a locking model and, for writeable data, possibly a condition variable to signal on when
the value changes.)



Sanchit Gupta
Undergraduate Electrical Engineering
University of Illinois Urbana Champaign
