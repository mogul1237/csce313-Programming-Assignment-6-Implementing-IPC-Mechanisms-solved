# csce313-Programming-Assignment-6-Implementing-IPC-Mechanisms-solved

Download Here: [csce313 Programming Assignment 6: Implementing IPC Mechanisms solved](https://jarviscodinghub.com/assignment/programming-assignment-6-implementing-ipc-mechanisms-solution/)

For Custom/Original Work email jarviscodinghub@gmail.com/whatsapp +1(541)423-7793

Introduction
The last two programming assignments have made heavy use of the RequestChannel class,
which was pre-written and given to you along with the dataserver code so that you could
focus on learning synchronization techniques. In fact, it was possible to complete both PA4
and PA5 without even looking at either dataserver.cpp or reqchannel.cpp.
If you did look at them, you may have noticed that the RequestChannel class uses a
mechanism called “named pipes” or “FIFOs” to communicate between the two sides of the
channel. However, FIFOs are only one of several different IPC mechanisms, each of which
have their own particular uses that make them suited to particular applications. In this
programming assignment, we are going to expand our toolbox by learning about 2 “new”
IPC mechanisms in addition to named pipes: message queues and shared memory, where the
latter would in turn use Kernel Semaphores.
Background
Message Queues
While pipes provide a byte stream between two processes, message queues allow for the
exchange of messages between processes. There are library functions (POSIX library, not
STL) for message queue opening/creation, sending messages, receiving messages, closing the
message queue, deleting the message queue, and modifying the message queue’s attributes.
You may be able to use default attributes for this assignment, but those defaults vary by
system. Visit the man pages for System V IPC (not the POSIX IPC) for how to check and
set default message queue attributes.
Shared Memory
Up until now there have been IPC mechanisms to provide byte streams and message passing,
but what if something a little more versatile is needed? Shared memory is exactly what
it sounds like: a segment of memory that can be read and modified (depending on its
configuration) by multiple different processes. You will notice that there are no system
calls for reading and writing the shared memory segment. This is because a shared memory
segment is semantically identical to any other memory segment, such as can be obtained
from malloc, except for the IPC and synchronization considerations. One can read and
modify it using memset, strcpy, memcpy, or just about any other memory-reading/writing
operations. This brings in synchronization concerns between the writer and reader, which we
will have to solve using Kernel Semaphores.
1
CSCE 313 Fall 2018
Kernel Semaphores
You are already familiar with the concept of semaphores from PA4 and PA5, and even had to
write your own. However, that semaphore was only “process-local” or “thread-shared,” since
it was allocated within the address space of a given process. What if two different processes
needed to synchronize on the same semaphore, a “process-shared” or kernel semaphore?
For this programming assignment, part of what you will need to do is fill in the methods of
the KernelSemaphore class. KernelSemaphore implements the familiar Semaphore interface
but is distinct from the process-local semaphore from PA4 and PA5.
The Assignment
Code
You have to start off of your code from PA4. We are assuming that you have a working PA4.
If that is not the case, please contact us for a working version of PA4 (if you get a working
PA4 from us, you cannot later make up by submitting a late PA4). Please do not start based
on PA5 because the select() at the heart of PA5 will not work with either shared memory
or message queues.
You then have to make up 3 versions of your PA4 each using a separate IPC-method-based
request channels: FIFO, message queue, and shared memory. Call these versions PA6 FIFO,
PA6 MQ, PA6 SHM, respectively. You should have an abstract RequestChannel class and 3
sub-classes:
• FIFORequestChannel
• MQRequestChannel
• SHMRequestChannel
Here, the first one FIFOReqeustChannel would be taken directly from PA4 and you need to
implement the others. The API of base RequestChannel class should be as follows:
class RequestChannel {
public :
typedef enum {SERVER SIDE, CLIENT SIDE} Si d e ;
typedef enum {READ MODE, WRITE MODE} Mode ;
/* CONSTRUCTOR/DESTRUCTOR */
RequestChannel ( const s t r i n g name , const Si d e s i d e ) ;
˜ RequestChannel ( ) ;
virtual s t r i n g c r e a d ( )= 0;
/* Blocking read of data from the channel. Returns a string of
characters read from the channel. Returns NULL if read failed. */
virtual int c w ri t e ( s t r i n g msg )=0;
/* Write the data to the channel. The function returns
the number of characters written to the channel. */
} ;
2
CSCE 313 Fall 2018
You will also need KernelSemaphore class for a properly functioning SHMRequestChannel,
where the semaphore´s API should look like the following:
class KernelSemaphore {
private :
/* INTERNAL DATA STRUCTURES */
public :
/* CONSTRUCTOR/DESTRUCTOR */
KernelSemaphore ( int v a l ) ;
˜ KernelSemaphore ( ) ; // make sure to remove all allocated resources
void P ( ) ; /* Acquire Lock*/
void V ( ) ; /* Release Lock */
} ;
0.1 Compiling and Running Your Code
There should be only 1 makefile to compile everything together.
Take an additional runtime argument option “-i” which should get one of:
• “f” for FIFO
• “q” for message queue
• “s” for shared memory
The following is an example command to run PA6:
./ client -n < requests / person > -b < bounded buffer size >
-w < number of request channels > -i
You must resolve the derived type of RequestChannel class in the runtime using polymorphism
and run-time binding in C++.
0.2 Clean Up
You must clean up all IPC objects from the kernel memory and all temporary files you created
for doing ftok(). Checking the /dev/mqueue and /dev/shm directories (or alternatively,
running ipcs command in shell) will aid you in this clean-up process. In addition, you should
clean all heap-allocated objects.
Report
• Gather timing data on the same set of n, b, w arguments on each of PA6 FIFO, PA6 MQ,
and PA6 SHM.
• Present a performance comparison of the different IPC mechanisms based on this data,
and attempt to provide explanation for any differences and similarities.
• Present the results in separate graphs using PA6 FIFO (i.e., PA4) as the baseline for
comparison
3
CSCE 313 Fall 2018
• What is the maximum w and thus the max number of RequestChannels that you can
use for each IPC? How much more can you go beyond the limit in PA4? (recall that
the limit imposed by how many file descriptors each user can have. Now that we are
not using file descriptors for MQ or SHM, has that situation changed?)
• What are some of the limits encountered by each class, either due to the specific
implementation or to operating system limitations, and how does the program behave
when it encounters them?
• Describe the clean-up activity you have done for each IPC
Bonus worth 12 points
• Using only one MQ object for all the request channels: 4 pts
• Using only one shared memory segment for all request channels: 4 pts
• Using minimum number of temporary files (needed for calling ftok() function) for
each request channel: 4 pts
What to Turn In
Turn in a single zip file PA6.zip containing the report, all the class files (separated into .h
and .cpp) and a makefile. Note that the same makefile should build all request channel
versions (FIFO, MQ and SHM).
