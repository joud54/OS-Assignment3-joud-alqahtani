# Assignment 3 - Complete Documentation

**Student Name**: [joud saad alqahtani]  
**Student ID**: [445052069]  
**Date Submitted**: [Submission Date]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [Paste your personal Gmail Google Drive link here]

**Video filename**: `[YourStudentID]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [May 1, 2026, 3:00 PM]
**What I implemented**: 
began the assignment by comprehending the given code and determining any potential race situations and shared resources.

**Challenges encountered**: 
Understanding where race conditions occur in the multithreaded scheduler.

**How I solved it**:
Carefully reviewed the code and traced thread execution. 

**Testing approach**:
Ran the program multiple times to observe inconsistent results. 

**Time spent**:
1.5 hours 

---

### Entry 2 - [May 1, 2026, 5:30 PM]
**What I implemented**: 
Added ReentrantLock to protect counter variables (contextSwitchCount, completedProcessCount, totalWaitingTime).

**Challenges encountered**: 
Ensuring locks are always released properly

**How I solved it**: 
Used try-finally blocks to guarantee unlocking

**Testing approach**: 
Tested by running the program multiple times and checking counter consistency

**Time spent**: 
2.5 hours
---

### Entry 3 - [May 1, 2026, 10:00 PM]
**What I implemented**: 
Added synchronization to execution log using ReentrantLock.

**Challenges encountered**: 
Avoiding ConcurrentModificationException.

**How I solved it**: 
Wrapped all log operations inside lock/unlock blocks

**Testing approach**: 
Observed that no exceptions occur during execution.

**Time spent**: 
1 hour

---

### Entry 4 - [May 1, 2026, 11:00 PM]
**What I implemented**:
Implemented Semaphore to control CPU access 

**Challenges encountered**: 
Understanding how permits affect thread execution

**How I solved it**: 
Used a binary semaphore (1 permit) to ensure only one thread accesses CPU.

**Testing approach**:
Verified sequential execution behavior 

**Time spent**: 
1 hour

---

### Entry 5 - [May 2, 2026, 12:00 AM]
**What I implemented**: 
Completed documentation and final testing.

**Challenges encountered**: 
Ensuring all parts are complete and clear.

**How I solved it**: 
Reviewed assignment requirements and verified all sections

**Testing approach**: 
Ran program 2 times and checked consistency

**Time spent**: 
0.5 hour

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:
Shared counter variables like contextSwitchCount, completedProcessCount, and totalWaitingTime exhibit the first race condition. Multiple threads alter these variables within procedures like addWaitingTime() and incrementContextSwitch(). Two threads may read the same value and change it wrongly in the absence of synchronization, which could result in lost updates and inaccurate outcomes.

The shared executionLog (ArrayList) is where the second race situation takes place. Multiple threads calling executionLog.add() simultaneously may distort the internal structure or result in a ConcurrentModificationException since ArrayList is not thread-safe.

Because operations like increment (++) and list update are not atomic, concurrent access is troublesome. Inconsistent data, such as inaccurate counts or missing log entries, may arise from this. ReentrantLock synchronization makes sure that only one thread can change these shared resources at once.



---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:
ReentrantLock is used to provide mutual exclusion, meaning only one thread can enter a critical section at a time. It gives more control than synchronized blocks and allows explicit lock and unlock operations. In my code, I used a single ReentrantLock (SharedResources.lock) to protect shared counters and the execution log, ensuring thread-safe updates.

A Semaphore, on the other hand, controls access based on permits. It allows a fixed number of threads to access a resource simultaneously. In my implementation, I used a binary semaphore (cpuSemaphore with 1 permit) to simulate a single CPU. This ensures that only one process (thread) can execute at a time.

The key difference is that ReentrantLock is used for protecting critical sections, while Semaphore is used for controlling access to limited resources. In this code, the lock protects shared data, and the semaphore controls CPU execution



---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:
Deadlock is a situation where two or more threads are blocked forever because each one is waiting for a resource held by another thread. This usually happens when locks are not released properly or when there is circular waiting.

One prevention technique is using try-finally blocks to ensure that locks are always released, even if an exception occurs. In my code, every lock.lock() is followed by unlock() inside a finally block, which guarantees that the lock will not remain held.

Another technique is avoiding resource holding for long periods and ensuring proper ordering. In this implementation, I used a simple design with a single lock, which reduces the risk of circular waiting.

Additionally, the semaphore (cpuSemaphore) is always released in a finally block in both run() and runToCompletion() methods. This prevents threads from getting stuck and ensures smooth execution without deadlocks.



---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:
In my implementation, I used a single ReentrantLock (coarse-grained locking) to protect all shared counters and the execution log. This means that whenever any thread needs to update a shared resource, it must acquire the same lock.

The advantage of this approach is simplicity and reduced risk of errors such as deadlocks or inconsistent locking. It ensures correctness because only one thread can access shared resources at a time.

However, the trade-off is reduced concurrency. Even though the counters (contextSwitchCount, completedProcessCount, totalWaitingTime) are independent, threads cannot update them simultaneously because they share the same lock.

A fine-grained approach (separate locks for each counter) would allow better concurrency since multiple threads could update different variables at the same time. Given that the counters are independent, fine-grained locking would provide better performance.

Despite that, I chose coarse-grained locking for simplicity and reliability, especially in this assignment where correctness is more important than performance


---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**:
 contextSwitchCount, completedProcessCount, totalWaitingTime

**Why they need protection**: 
These variables are shared among multiple threads and are updated concurrently

**Synchronization mechanism used**: 
ReentrantLock

**Code snippet**:
```java
lock.lock();
try {
    contextSwitchCount++;
} finally {
    lock.unlock();
}
```

**Justification**: 

Using ReentrantLock ensures mutual exclusion, meaning only one thread can update the shared counters at a time. This prevents race conditions and guarantees correct final values for all statistics.

---

### Critical Section #2: Execution Log

**What resource**: 
executionLog (ArrayList)

**Why it needs protection**: 
ArrayList is not thread-safe.

**Synchronization mechanism used**: 
ReentrantLock

**Code snippet**:
```java
lock.lock();
try {
    executionLog.add(message);
} finally {
    lock.unlock();
}
```

**Justification**: 
prevents data corruption and exceptions

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 
control access to the CPU

**Number of permits and why**:
simulate a single CPU 1 

**Where implemented**: 
inside run() mithod

**Code snippet**:
```java
   SharedResources.cpuSemaphore.acquire();

   SharedResources.cpuSemaphore.release();

```

**Effect on program behavior**: 
ensures that only one process executes at a time

---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
```bash
# Commands used (run the program at least 5 times)
```

**Results**: 
(Show that running multiple times produces consistent, correct results)

**Why synchronization is necessary**: 
(Explain what race conditions COULD occur without synchronization, even if you didn't observe them. Explain which shared resources need protection and why.)

**Conclusion**: 

---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: 

**Results**: 

**What this proves**: 

---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: 

**Actual values**: 

**Analysis**: 

---

### Test 4: Different Scenarios
**Scenario tested**: [e.g., different time quantum, more processes, etc.]

**Purpose**: 

**Results**: 

**What I learned**: 

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[6-8 sentences about key concepts, challenges, insights]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 

**Example 2**: 

---

### How I would explain synchronization to others:

[Explain to someone who just finished Assignment 1 - use simple terms and analogies]

---

## Part 6: GitHub Repository Information

**Repository URL**: 

**Number of commits**: 

**Commit messages**: 
1. 
2. 
3. 
4. 

---

## Summary

**Total time spent on assignment**: 

**Key takeaways**: 
1. 
2. 
3. 

**Most challenging aspect**: 

**What I'm most proud of**: 

---

**End of Documentation**
