# Starve Free Readers-Writers Problem

## Explanation

An approach similar to First Come First Serve (FCFS) is used as solution to this problem.
It is considered that multiple Reader processes can access the critical section while only one Writer process can be allowed to access the critical section at a time.
Also a Reader process and a Writer process cannot access the critical section simultaneously.

Two cases are considered here - 

  1. Initially Writer is executing the critical section.
     - If another Writer approaches, then it has to wait as only one Writer can access the critical section at a time.
     - If continuously many Writers request for access to critical section, then their request will be granted one at a time.
     - Instead if a Reader arrives then, it has wait until all Writers complete their execution.
     - Anymore Writer (that comes after that Reader) cannot access the critical section.
     - Once, all the Writers complete execution, then all waiting Readers are given opportunity to access critical section simultaneously.
  
  2. Initially Readers are executing the critical section.
     - If another Reader arrives, then it can access the critical section as multiple Readers are allowed inside critical section. 
     - Instead if a Writer arrives then, it has to wait until all currently active Readers complete their execution.
     - Anymore Reader (that comes after that Writer) cannot access the critical section.
     - When all active Readers complete execution, then all waiting Writers are given opportunity to access critical section one at a time.
   
   Both of above cases eliminates possibility of the starvation of any Writer or Reader.
   Hence Readers and Writers can access critical section in alternate turns with constraint that multiple Readers can access the critical section simultaneously
   while only one Writer can access the critical section at a time.
   
### Semaphores

Four semaphores are used namely - 

  - allowedReaders -  It confirms that Reader process has necessary permission to access the critical section. It is initialised to 0.
  - allowerWriters - It confirms that Writer process has necessary permission to access the critical section. It is initialised to 0.
  - finishedWriters - It confirms that only one Writer process is inside critical section, even though multiple Writer processes have permission to access it. It is initialised to 0. 
  - mutx - It provides mutual exclusion among Readers and Writers. It is initialised to 1.  

### Variables used in Reader process

  - activeReadercnt - It stores number of Reader processes that are currently inside critical section. It is initialised to 0.
  - waitingReadercnt - It stores number of Reader processes that are waiting for access to the critical section. It gains access after group of Writer processes that came before are completed. It is initialised to 0.

### Variables used in Writer process

  - primaryWritercnt - It stores number of waiting Writer processes which must be given access to critical section before any Reader process. It is initialised to 0.
  - activeWritercnt - It checks whether any Writer process is inside critical section or not. It is 1 if Writer proceess is in critical section else it is 0.
  - waitingWritercnt - It stores count of Writer processes whhich are waiting for access to the critical section. It gains access only when group of Reader processes that came before it completes their execution. It is initialised to 0.
