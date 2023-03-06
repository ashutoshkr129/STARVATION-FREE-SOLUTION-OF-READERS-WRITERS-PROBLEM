# Starve Free Reader-Writer's Problem

## The Problem
The Readers-Writers Problem involves multiple processes concurrently accessing a shared resource, with readers only reading it and writers modifying it. The challenge is to synchronize access to the resource for correctness and efficiency. Variations include readers/writers-preference, and solutions involve synchronization mechanisms like semaphores, monitors, and mutexes.

**The pseudocode mentioned in the source file gives a starve free problem for the Readers Writers problem**



## Solutions to Reader-Writer's Problem
The first and second solutions to the Readers-Writers Problem resolve the issue, but writers may starve in the first solution while readers may starve in the second solution. The third solution proposes a starve-free approach. This article will cover the classic solutions to the first and third problems, as well as a new, more efficient solution.
**In the classical solution the writers will starve.**

##  Starve Free Solution
To prevent writers from being starved in the Readers-Writers Problem, a new mutex lock implemented with a semaphore called queue_in is introduced in the solution. Access to the shared resource is granted only to the process that has access to queue_in. This approach ensures that readers do not continuously block the resource, preventing subsequent writers from accessing it. By pushing all processes into the FIFO queue of the queue_in semaphore, subsequent readers are checked after the writers, ensuring fairness and preventing starvation. As a result, this algorithm guarantees that no process will be starved.

#### Semaphores (Globally Defined)
```cpp
semaphore mutex = 1, rw_mutex = 1, queue_in = 1;
int counter = 0;
```
The rest of the variables are same as the first solution with an addition of `queue_in`. Now, both the reader and the writer implementations have to enclosed within `queue_in` to ensure mutual exclusion in the whole process and thus, making the algorithm starve-free.

### Implementation: Reader
```cpp
do{
    // Entry Section
    wait(queue_in);
    wait(mutex);
    counter++;
    if(counter == 1) wait(w_mutex); 
    signal(mutex);
    signal(queue_in);

    /*
       Critical Section 
    */

   // Exit Section
    wait(mutex);
        counter--;
    if(counter == 0) signal(w_mutex); 
    signal(mutex);

   // Remainder Section
}while(true);
```
The `wait()`function is first called for `queue_in`. If a reader is waiting for a writer process, they are queued with the fellow writers in the FIFO queue of `queue_in`, instead of `mutex`. `queue_in` acts as a medium to ensure that all processes, whether readers or writers, have the same priority. As a result, readers and writers have equal opportunities to access the shared resource.

### Implementation: Writer
```cpp
do{

    // Entry Section
    wait(queue_in);
    wait(w_mutex);

    /*      
      Critical Section  
    */

    // Exit Section
    signal(w_mutex);
    signal(queue_in);

    // Remainder Section

}while(true);
```
Similar to readers, writers are also queued in the FIFO queue of queue_in by invoking `wait()` for `queue_in`. As a result, all processes requiring access to the resource are scheduled in a first-come-first-serve (FCFS) manner. This solution utilizes three semaphores. 


## References

1. Abraham Silberschatz, Peter B. Galvin, Greg Gagne - Operating System Concepts
