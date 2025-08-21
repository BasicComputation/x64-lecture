# Multitasking
A process is all that belong to an running application. That is its page tables for example. <br>
The operating system keep track of all the resources an application uses.

The CPU can only run one process at a time in a core. 
For each core there is timers, and the operating system will set a timer to trigger, 
when expiring transferring control over to the operating system, leaving opportunity to 
let an other process get time to run in the core. 
A process can also be interrupted by other hardware that needs attention.

In order for this to work, all relevant registers needs to be saved, the ‘context’. <br>
So it will seem to an application like nothing have happened when starting again, the context is loaded.

### Threads <br>
Creating threads is about starting execution of parts of a process, a function, in parallel, like it’s a process, but it also shares address space with the main thread. 

See mutexes, semaphores, critical sections, race conditions, barrier, spin lock, deadlock… (advanced) <br>
https://learn.microsoft.com/en-us/windows/win32/procthread/processes-and-threads 
