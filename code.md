## Below mentioned is the definition of Semaphore 
```cpp 
struct semaphore{
    int value;
    struct process *list;
};
```
## Implementation of signal() operation 
```cpp
void signal(semaphore *S){
    S->value++;
    if(S->value <= 0){
        process P = S->list.removeProcess(); /* Removes a process from the list */
        /* The removed process is random in nature */
        /* Implies that all processes in the list have equal probability of being removed */
        P.wakeup(); 
    }
}
```
## Below mentioned implementation of wait() operation and signal() operation avoids busy waiting 

### Implementation of wait() operation 
```cpp
void wait(semaphore *S){
    S->value--;
    if(S->value < 0){
        S->list.addProcess(); /* Adds the calling process to the list */
        this.block(); /* Blocks the calling process */
    }
}
```

Here we assumed variables such as activeReadercnt, waitingWritercnt, activeWritercnt, primaryWritercnt and waitingWritercnt to be global variable.
All global variables are initd to zero.
### Below listed is the method to initalise the global variables and semaphore.
```cpp
void init(){
    int activeReadercnt = 0,waitingWritercnt = 0,activeWritercnt = 0,primaryWritercnt = 0,waitingWritercnt = 0;
    semaphore *mutx, *allowedReaders, *allowedWriters, *finishedWriters;
    mutx = new semaphore;
    allowedReaders = new semaphore;
    allowedWriters = new semaphore;
    finishedWriters = new semaphore;

    mutx->value = 1;
    mutx->list = NULL;

    allowedReaders->value = 0;
    allowedReaders->list = NULL;

    allowedWriters->value = 0;
    allowedWriters->list = NULL;

    finishedWriters->value = 0;
    finishedWriters->list = NULL;
}
```
```cpp
### Below mentioned is the structure of a Reader Process 
void Readers(){
    wait (*mutx);
    /* Reader process checks whether there is any Writer process that is waiting for execution prior to it */
    /* If No, then if statment is true, otherwise, it is false */
    if(activeWritercnt + primaryWritercnt + waitingWritercnt == 0){
        activeReadercnt++;
        /* Allow Reader process to access critical section */
        signal (*allowedReaders)
    }else{
        waitingWritercnt++;
    }
    signal (*mutx);
    wait (*allowedReaders);

    /* Entry to Critical Section */
    wait (*mutx);
    activeReadercnt--;
    if(activeReadercnt == 0 and waitingWritercnt > 0){
        activeWritercnt = 1;
        signal (*allowedWriters);
        primaryWritercnt = waitingWritercnt - 1;
    }
    signal (*mutx);
} 
```
```cpp
void Writer(){
    wait (*mutx);
    if(activeReadercnt + waitingWritercnt == 0){
        /* If no Writer process is inside critical section then make it as active Writer process */
        /*  Otherwise make it as primary Writer process */
        if(activeWritercnt == 0){
            activeWritercnt = 1;
            /* Allow active Writer process to access critical section */
            signal (*allowedWriters);
        }else{
            primaryWritercnt++;
        }
    }else{
        waitingWritercnt++;
    }
    signal (*mutx);
    wait (*allowedWriters);

    /* Entry to Critical Section */

    signal (*finishedWriters);
    wait (*mutx);
    while(primaryWritercnt > 0){
        signal (*allowedWriters);
        primaryWritercnt--;
        /* Wait until prior Writer process has finished execution of critical section */
        wait (*finishedWriters);
    }
    activeWritercnt = 0;

    /* If there are waiting Readers process and execute that group to access critical section and complete their execution */
    while(waitingReadercnt > 0){
        signal (*allowedReaders);
        activeReadercnt++;
        waitingReadercnt--;
    } 
    signal (*mutx);
}
```
