# Assignment 3 - Complete Documentation

**Student Name**: [shaden abdulaah aldosry]  
**Student ID**: [445052024]  
**Date Submitted**: [2026-05-2]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [[Paste your personal Gmail Google Drive link here](https://drive.google.com/file/d/1zFEsEJkihyJmM4aDTJwl0alVf-eZyprC/view?usp=drivesdk)]

**Video filename**: `[445052024_Assignment3_Synchronization.mp4]

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [2026-04-29, 10:30]
- What I implemented: Set up the repository, changed student ID, made first commit.
 - Challenges encountered: None – straightforward setup.
 - How I solved it: Followed the VS Code cloning steps.
 - Testing approach: Compiled and ran the original unsynchronized code to see the race con ditions (inconsistent log counts).
 - Time spent: 30 min

---

### Entry 2 - [2026-04-29, 10:30]
- What I implemented: Task 1 – fine-grained ReentrantLocks for the three counters.- Challenges encountered: Understanding why fine-grained locking is better than a single lock.- How I solved it: Read about lock granularity; decided to use three separate locks becau se the counters are independent.- Testing approach: Ran the program 10 times; counters now give the same values each run.- Time spent: 1h

---

### Entry 3 - [2026-04-29, 11:30]
- What I implemented: Task 2 – ReentrantLock for the execution log (ArrayList).
- - Challenges encountered: Initially forgot to unlock in finally block.
  - - How I solved it: Corrected to lock/unlock inside try-finally.
    - - Testing approach: Added heavy logging; never saw ConcurrentModificationException.
- - Time spent: 45 min

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

[- **First race condition** – `contextSwitchCount++` (and the other counters).  Shared resource: the integer counters.  Problem: `++` is not atomic; two threads can read the same value, increment, and write back, causing a lost update.  Incorrect behaviour: The final counter value is less than the actual number of incremen ts.
- **Second race condition** – `executionLog.add(message)`.  Shared resource: `ArrayList<String>`.  Problem: `ArrayList` is not thread-safe; concurrent `add()` calls can corrupt internal structure, throw `ConcurrentModificationException`, or lose entries.  Incorrect behaviour: Program may crash or log entries may disappear.]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

[- **ReentrantLock** is a mutual exclusion lock (binary). It guarantees that only one thre ad holds the lock at a time. I used it for the counters and the log because those resourc es require exclusive access.- **Semaphore** maintains a set of permits. A binary semaphore (permits = 1) acts like a lock, but semaphores can also allow N concurrent accesses (e.g., a connection pool). I us ed a `Semaphore(1)` to limit CPU execution – only one process can run at any moment, exac tly matching a single-core CPU.]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

[- **Deadlock** occurs when two or more threads wait forever for each other’s locked resou rces.- Prevention techniques I used: 1. **Lock ordering** – I never acquire more than one lock at a time, so cyclic wait can not happen. 2. **try-finally blocks** – Every `lock()` or `acquire()` is followed by a `finally` block that releases the resource. This guarantees release even if an exception occurs, prev enting resource leaks.- Additionally, the semaphore is acquired at the very beginning of the critical section a nd released immediately after, so there is no nested locking.]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

[- I chose **fine-grained locking** – three separate `ReentrantLock`s, one per counter (`c ontextSwitchLock`, `completedProcessLock`, `waitingTimeLock`).- **Why:** The three counters are completely independent (updating one does not depend on the others). With a single coarse-grained lock, threads updating different counters would still block each other, creating unnecessary contention. Fine-grained locking allows true parallelism: while one thread increments `contextSwitchCount`, another can simultaneously increment `completedProcessCount`.- **Trade-offs:** Fine-grained requires more code and careful reasoning, but for independ ent resources the concurrency gain is worth it. Coarse-grained is simpler but reduces thr oughput.- Because the counters are independent, fine-grained locking provides **better concurrenc y** – it exactly follows the principle: protect each shared resource with its own lock..]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

- Which variables: `contextSwitchCount`, `completedProcessCount`, `totalWaitingTime`- Why protection: The read-modify-write operations (increment, addition) are not atomic; without locks, updates can be lost.- Mechanism: Three separate `ReentrantLock`s (fine-grained)- Code snippet: ```java public static void incrementContextSwitch() { contextSwitchLock.lock(); try { contextSwitchCount++; } finally { contextSwitchLock.unlock(); }

**Justification**: 

---

### Critical Section #2: Execution Log
Resource: List<String> executionLog Why protection: ArrayList is not thread‑safe; concurrent add() calls cause corruption or exceptions.Mechanism: ReentrantLock logLock Code snippet: java public static void logExecution(String message) { logLock.lock(); try { executionLog.add(message); } finally { logLock.unlock(); } }

**Justification**: 

---

### Critical Section #3: CPU Semaphore

Purpose: Simulate a single‑core CPU – only one process can execute at a time. Number of permits: 1 (binary semaphore) Implemented in
: Process.run() and Process.runToCompletion() Code snippet (inside run() ): java SharedResources.cpuSemaphore.acquire(); try { // ... execution code ... } finally { SharedResources.cpuSemaphore.release(); }
```

**Effect on program behavior**: 

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
Procedure: Added a loop that ran the program 20 times. Results: No ConcurrentModificationException or any other exception occurred. What this proves: The logLock successfully serialises access to the ArrayList , making it thread‑safe.

---

### Test 3: Correctness Verification
Expected: completedProcessCount should equal number of processes created; total waiting time should be consistent with per‑process waiting times. Actual: All values matched the manual calculation (checked with print statements). Analysis: The synchronisation does not change the logical behaviour – it only ensures correctness under concurrency.
---

### Test 4: Different Scenarios
Scenario: Changed Semaphore(1) to Semaphore(2) temporarily. Purpose: Observe effect of allowing two concurrent processes. Results: With 2 permits, execution overlapped (interleaving in output). Still no race conditions because counters remained protected. What I learned: Semaphores are extremely flexible – they control the degree of concurrency without changing the core logic.

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[Race conditions are subtle: the code can run correctly many times and then suddenly fail. Synchronisation makes concurrent programs predictable. Fine‑grained locking is powerful: protecting independent resources with separate locks unlocks real parallelism. The try-finally pattern is non‑negotiable – forgetting to unlock in a finally block leads to deadlocks that are very hard to debug. A binary semaphore ( Semaphore(1) ) is functionally similar to a mutex, but a mutex (ReentrantLock) is usually preferred for mutual exclusion because it provides ownership and reentrancy. Synchronisation adds overhead, but the safety it buys is essential for any multithreaded program.]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

1. Banking systems – When multiple tellers update the same account balance, locks prevent lost deposits or withdrawals.
2.  Print spooler – A semaphore with a limit equal to the number of printers controls access to physical printers.

---

### How I would explain synchronization to others:

[“Imagine a shared whiteboard where many students want to write. If two write at the same time, their notes become unreadable. A mutex lock is like giving the marker to only one student at a time. A semaphore is like having a few markers – it lets a limited number of students write together. Without these rules, the whiteboard would be a mess. Thatʼs exactly what synchronisation does for shared data in a program.”]

---

## Part 6: GitHub Repository Information

**Repository URL**: https://github.com/shaden-abdullah-445/OS-Assignment3-Shaden-Abdullah.git

**Number of commits**: 6 (all meaningful, spread over 5 days)

**Commit messages**: 
1. Set my student ID: 445052024
2.   Task 1 (445052024): Added fine-grained ReentrantLock for counters 3. Task 2 (445052024): Added ReentrantLock for execution log 4.
3.   Task 3 (445052024): Implemented Semaphore for CPU control 5.
4.   Task 4 (445052024): Completed comprehensive documentation 6. Final cleanup and verification of synchronisation. 

---

## Summary

**Total time spent on assignment**: ~5.5 hours

**Key takeaways**: 
1. Fine‑grained locking improves performance for independent resources.
2.  try-finally is the only safe way to release locks.
 3. A semaphore can control both mutual exclusion and resource limits. 

**Most challenging aspect**: Deciding on lock granularity and proving that separate locks are safe (no deadlock because locks are never nested).

**What I'm most proud of**: The final program runs deterministically, with no exceptions, and the code clearly shows why each synchronisation mechanism is used.

---

**End of Documentation**
