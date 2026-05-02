# Assignment 3 - Complete Documentation

**Student Name**: [meshari awd alonzy]  
**Student ID**: [444050603]  
**Date Submitted**: [2/5]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [masharialonzy@gmail.com]

**Video filename**: `https://drive.google.com/file/d/1Jb9sxSgM1NWHCF8TcmlCjRYXiwMlnwZT/view?usp=drive_link`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [29, 1:21]
**What I implemented**: 
I started by reviewing the assignment requirements and analyzing the provided CPU scheduler code. I identified the shared resources such as contextSwitchCount, completedProcessCount, totalWaitingTime
**Challenges encountered**: 
The main challenge was understanding how race conditions occur in a multithreaded environment. It was initially difficult to detect where multiple threads were modifying shared variables simultaneously.
**How I solved it**: 
I carefully followed the execution flow of threads and identified critical sections where shared variables were updated. I studied synchronization concepts from the course material and confirmed that these sections required protection using locks
**Testing approach**: 
I ran the program multiple times without synchronization and observed inconsistent outputs, especially incorrect values in completed processes and waiting time.
**Time spent**:2 hour 

---

### Entry 2 - [30, 2 am]
**What I implemented**: 
executionLog. I also traced how threads interact with these shared variables during execution.
**Challenges encountered**: 
The counter was defind out side the functhon 
**How I solved it**: 
send to gpt and the informed me error
**Testing approach**: 
After implementing locks, I tested the program again and verified that the counters were updated correctly
**Time spent**: 2 hour

---

### Entry 3 - [1/5, 2pm]
**What I implemented**: 
I added a binary semaphore to control CPU access, ensuring that only one process executes at a time. I integrated the semaphore into the run() method using acquire and release operations.
**Challenges encountered**: 
determining the correct placement of the semaphore operations without affecting the execution flow. I also had to ensure that no thread would be blocked indefinitely and that the semaphore would not cause deadlocks or missed releases.
**How I solved it**: 
I placed the semaphore acquisition at the beginning of the execution and ensured it is always released in the finally block. This guaranteed safe and controlled access to the CPU.
**Testing approach**: 
I performed multiple runs of the program and verified that all processes completed successfully. The number of completed processes matched the expected value, and no inconsistencies were observed.
**Time spent**: 3 hour

---

### Entry 4 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

### Entry 5 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

The first race condition is in the shared counters like contextSwitchCount and totalWaitingTime. When multiple threads try to update these at once (count++), the operation isn't atomic, leading to "lost updates" where the final counts are lower than they should be. The second race condition is in the executionLog ArrayList. Because ArrayList is not thread-safe, if two threads try to add messages at the same time, it can cause a ConcurrentModificationException and crash the program. For example, executionLog.add(message) without a lock is dangerous when many processes are running
---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

A ReentrantLock is a mutual exclusion (mutex) tool used to ensure only one thread can access a specific piece of code at a time. A Semaphore is more flexible because it manages a set of "permits" to limit how many threads can access a resource. In my code, I used ReentrantLock to protect the counters and the log because I needed strict one-at-a-time access to prevent data corruption. I used a Binary Semaphore (with 1 permit) to simulate the CPU core, ensuring that only one process can "occupy" the processor and execute its quantum, which perfectly models a single-core system.
---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

A deadlock is a "traffic jam" in the system where threads are stuck waiting for each other to release locks, and no progress is made. Two ways to prevent this are Lock Ordering (always acquiring locks in the same order) and using Try-Finally blocks. In my implementation, I focused on the try-finally method. By putting the unlock() or release() call inside the finally block, I ensure that the lock is always opened even if the program crashes or an exception occurs. This prevents a thread from holding a lock forever and blocking everyone else.
---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

For Task 1, I decided to use ONE lock for all three counters, which is known as Coarse-grained locking. I chose this approach because it is simpler to implement and reduces the risk of making mistakes or causing deadlocks with multiple locks. The trade-off is that coarse-grained locking can be slower in very high-speed systems because threads must wait for the single lock even if they want to update different variables. Since the three counters are independent, a Fine-grained approach (using a separate lock for each counter) would actually provide better concurrency because it allows different threads to update different counters at the same time. However, for the scale of this assignment, one lock provides a good balance of safety and performance.
---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**:contextSwitchCount, completedProcessCount, totalWaitingTime 

**Why they need protection**: 
without protection, updates would be lost due to race conditions
**Synchronization mechanism used**: 
ReentrantLock (counterLock).
**Code snippet**:
counterLock.lock();
try {
    contextSwitchCount++;
} finally {
    counterLock.unlock();
}

**Justification**: This ensures that increment operations are atomic and visible across all threads.

---

### Critical Section #2: Execution Log

**What resource**: ArrayList<String> executionLog.

**Why it needs protection**: 
ArrayList is not thread-safe; concurrent access during the simulation leads to ConcurrentModificationException.
**Synchronization mechanism used**: 
eentrantLock (logLock).
**Code snippet**:
logLock.lock();
try {
    executionLog.add(message);
} finally {
    logLock.unlock();
}
**Justification**: 

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: to simulate mutual exclusion for a single-core CPU resource.

**Number of permits and why**: 
1 permit; because the simulation models a single CPU that can only run one process at a time.
**Where implemented**: 
At the beginning and end of the run() and runToCompletion() methods in the Process class.
**Code snippet**:
SharedResources.cpuSemaphore.acquire();
try {
    // Process Execution Logic
} finally {
    SharedResources.cpuSemaphore.release();
}

**Effect on program behavior**: 
It prevents process outputs from interleaving and ensures a realistic scheduling flow where processes wait for the CPU to become available
---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
```bash
# Commands used (run the program at least 5 times)
```java SchedulerSimulationSync

**Results**: 
(Show that running multiple times produces consistent, correct results)
 "Total Context Switches" and "Average Waiting Time" remained identical across all 5 runs.

**Why synchronization is necessary**: 
(Explain what race conditions COULD occur without synchronization, even if you didn't observe them. Explain which shared resources need protection and why.)
Without it, the "Total Context Switches" might vary or be lower than expected because some increments might fail silently during thread context switching.

**Conclusion**: 
The synchronization mechanisms are working correctly to produce deterministic results.
---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: 
bserved the terminal output during long execution cycles with many processes.
**Results**: 
ConcurrentModificationException errors were recorded.
**What this proves**: 
The logLock successfully protected the executionLog from simultaneous thread access.
---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: 
Final "Completed Processes" should equal the initial numProcesses
**Actual values**: 
Total Context Switches: 19
Total Completed Processes: 20
Total Waiting Time: 343885ms
Average Waiting Time: 34388ms
**Analysis**: 
The completedProcessCount was correctly protected, proving the counterLock is effective.
---

### Test 4: Different Scenarios
**Scenario tested**: [e.g., different time quantum, more processes, etc.]
Increasing the number of processes to 30 and reducing the timeQuantum to 1000ms

**Purpose**: 
To stress-test the synchronization mechanisms under high contention. By having more processes and frequent context switches, the probability of race conditions increases significantly.


**Results**: 
The program handled the increased load perfectly. The contextSwitchCount increased as expected due to the smaller quantum, but the data remained consistent, and no ConcurrentModificationException occurred despite the heavy logging


**What I learned**: 
I learned that a well-synchronized program is scalable. Regardless of how many threads are competing for the counters or the CPU, the ReentrantLock and Semaphore ensure that the integrity of the shared data is never compromised. This confirms that the synchronization overhead is a necessary trade-off for system stability
---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[6-8 sentences about key concepts, challenges, insights]

I learned that synchronization is the cornerstone of reliable multithreaded programming. Without it, even a simple counter can become a source of non-deterministic bugs. I understood the "Critical Section" concept and how to use Mutex locks to enforce mutual exclusion. Implementing the try-finally pattern taught me how to write robust code that avoids deadlocks. I also realized that while synchronization ensures correctness, it must be designed carefully to avoid unnecessary performance bottlenecks. Overall, this assignment bridged the gap between theoretical OS concepts and practical Java concurrency tools
---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 
Banking Systems: Ensuring that two simultaneous withdrawals from the same account don't result in an incorrect balance.
**Example 2**: 
Database Management: Controlling concurrent access to data records to maintain ACID properties.
---

### How I would explain synchronization to others:
Think of a shared printer in an office. If everyone tries to print at the exact same time without a system, the pages will come out mixed and corrupted
[Explain to someone who just finished Assignment 1 - use simple terms and analogies]

---

## Part 6: GitHub Repository Information

**Repository URL**: https://github.com/meshari444/OS-Assignment3-meshari-alonzy.git

**Number of commits**: 4

**Commit messages**: 
1. Set my student ID : 444050603
2. ReantrantLoock
3. Secure execution log using mutex synchronization 
4. add semaphore and modyfiyy counter
5. adding acquire and realease

---

## Summary

**Total time spent on assignment**: 11 hour 

**Key takeaways**: 
1. Race conditions are silent but dangerous bugs
2. try-finally is essential for deadlock prevention.
3. Semaphores are powerful for resource management.

**Most challenging aspect**: 
task 3 
**What I'm most proud of**: 
I am most proud of successfully transforming a non-thread-safe simulation into a robust, synchronized system
---

**End of Documentation**
