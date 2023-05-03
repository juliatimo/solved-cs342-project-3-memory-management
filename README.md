Download Link: https://assignmentchef.com/product/solved-cs342-project-3-memory-management
<br>
In this project you will implement a memory management library called <strong>sbmem.a</strong> (buddy memory allocation from shared memory). Processes will use your library to allocate memory space dynamically, instead of using the well-known malloc function.  A shared memory object, i.e., a shared memory segment, needs to be created first. Memory space allocations will be made from that segment to the requesting processes. Your library will implement the necessary initialization, allocation and deallocation routines.   Your library will keep track of the allocated and free spaces in the memory segment. For that it will use the Buddy memory allocation algoritm [1,

2]. Study buddy algorithm first. It is and old and elegant algorithm. It is easy to grasp.




Your library will implement the following functions. The first two functions will be used by programs that will create or destroy the shared segment to be used by other processes.




<ul>

 <li>int sbmem_init (int segsize). This function will create and initialize a shared memory segment of the given size. The given size is in bytes and must be a power of 2. Memory will be allocated from that segment to the requesting processes later. If operation is successful, the function will return 0, otherwise, it will return -1.  You will use POSIX shm_open()  and ftruncate() functions in the implementation of this function. If there is already a shared segment, the function will destroy the segment first, before creating a new one with the same name.  After initialization, the shared segment will be ready to use. That means processes can make memory allocation requests, and memory can be allocated from this segment, if available.</li>

</ul>




<ul>

 <li>sbmem_remove (). This function will remove the shared memory segment from the system. You will use shm_unlink () function to implement this. The function will do all the necessary cleanup. For example, the created semaphore(s) will be removed as well.</li>

</ul>




A process that would like to use the library for memory allocations will use the following functions that you will implement in your library (<strong>sbmemlib.c</strong>).




<ul>

 <li>int sbmem_open(). This function will indicate to the library that the process would like to use the library. In this way, the library can keep track of the processes that are interested in using the library. The library will map the shared segment to the virtual address space of the process using the mmap function. If there are too many processes using the library at the moment, sem_open will return -1. Otherwise, if the process can use the library, sem_open will return 0.</li>

</ul>




<ul>

 <li>void *sbmem_alloc (int reqsize). This function will allocate memory of size n, which is the next power of 2 greater than or equal to reqsize.  The reqsize is in bytes. For example, if reqsize is 200 (bytes), then the library will allocate memory of size 256, because 256 is the next power of 2 greater than or equal to 200. If allocation succeeds, the function will return a pointer to the allocated space. It is up to the program what to store in the allocated space. NULL will be returned if memory could not be allocated. This can happen, for example, when there is not enough memory.</li>

</ul>




<ul>

 <li>void sbmem_free (void *ptr). This function will deallocate the memory space allocated earlier and pointed by the pointer ptr. The deallocated memory space will be part of the free memory in the segment.</li>

</ul>




<ul>

 <li>int sbmem_close (). When a process has finished using the library it will call sbmem_close(). In this way your library will know that this process will not use the library for memory allocation, hence shared segment can be unmapped from the virtual address space of the process. If the process would like to use the library again, it should call sbmem_open again. Normally you need to deallocate all the space allocated earlier by the process when the process calls sbmem_close() (if the process did not free already). But to reduce bookkeeping, deallocating all space allocated earlier by the process when the process call sbmem_close() is OPTIONAL for the project . You don’t have to do it. In our tests, we will free each memory chunk that we allocated earlier before calling sbmem_close().</li>

</ul>




You will use semaphore(s) in your library to protect your shared structures. Processes may call allocation and free functions concurrently. This should not cause race conditions. You will ensure that by using semaphores.




A program, for example app.c,  that wants to use your library for memory allocations may use the related functions as below.  The program should include the library header file <strong>smem.h</strong>. This header file is the interface of the library.




include &lt;unistd.h&gt; #include &lt;stdlib.h&gt;




#include “sbmem.h”

#define ASIZE 256

int main() {     int i, ret;      char *p;

ret = sbmem_open();      if (ret == -1)

exit (1);




p = sbmem_alloc (ASIZE); // allocate space to for characters     for (i = 0; i &lt; ASIZE; ++i)        p[i] = ‘a’; // init all chars to ‘a’     sbmem_free (p);

sbmem_close();      return (0);

}




You will also implement two simple programs that will be called as create_memory.c and destroy_memory.c. The create_memory program will basically call the sbmem_init function of your library to create and initialize a shared memory segment. The destroy_mem program will basically call your sbmem_remove function to remove the shared segment from system.




We will test your library with various programs that we will implement using your library.




<h1>Experiments and Report (20 pts)</h1>




Do a lot of experiments to measure fragmentation amount. Plot your results. Draw conclusions. Write a report to include all the experiment descriptions and results.


