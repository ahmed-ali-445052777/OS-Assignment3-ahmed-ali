# Assignment 3 - Complete Documentation

**Student Name**: [Your Full Name]  
**Student ID**: [Your ID]  
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

### Entry 1 - [Apr 28, 2026, 9:18 PM]
**What I implemented**: 
I changed the  student ID  to my real student ID.

**Challenges encountered**: 
At first I did not know exactly where the student ID

**How I solved it**: 
I searched for `studentID` in VS Code

**Time spent**: 
 10 minutes
---

### Entry 2 - [Apr 29, 2026, 10:45 PM]
**What I implemented**: 
I added a `ReentrantLock` to protect the shared counter variables in the program.

**Challenges encountered**: 
I needed to understand why counters like `contextSwitchCount` can be a problem when more than one thread can access them.

**How I solved it**: 
I used one lock called `counterLock` before updating the shared counters, and I released the lock inside a `finally` block.

**Testing approach**: 
I checked the methods that update the counters and made sure each update is inside the lock section.

**Time spent**: 
45 minutes
---

### Entry 3 - [ Apr 30, 2026, 11:30 PM]
**What I implemented**: 
I added a separate lock for the execution log and used it inside `logExecution()`

**Challenges encountered**: 
I noticed that `executionLog` is an `ArrayList`, and it can cause problems if more than one thread tries to update it at the same time.

**How I solved it**: 
I added `logLock` and used it before adding a new message to the log. I also released it in the `finally` block

**Testing approach**: 
I checked the `logExecution()` method and made sure the add operation is inside the lock

**Time spent**: 
20 minutes
---

### Entry 4 - [Date, Time]
**What I implemented**: 
I added a binary semaphore to control CPU access so only one process can execute at a time

**Challenges encountered**: 

At firstI had some errors because the semaphore needs an import and the `release()` must be placed correctly

**How I solved it**: 
I imported `Semaphore`, added `cpuSemaphore` with one permit, used `acquire()` before the process execution, and used `release()` in the `finally` block.

**Testing approach**: 
I saved the code and checked that the program compiles and the semaphore code is placed only around the execution part.

**Time spent**: 
47 minutes 
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

[In the original code, I think the first race condition was in the counters. For example, `contextSwitchCount++` looks like one small line, but actually the thread reads the value, increases it, then writes it back. If two threads do this near the same time, the final number may not include both updates. The same idea can happen with `completedProcessCount` and `totalWaitingTime`.

The second race condition was in `executionLog`. It is shared between processes, and more than one thread may try to add a message to it. Since `ArrayList` is not safe for this type of access, the log could have missing or mixed entries. In a worse case, it may cause an exception while the program is running.]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

[I used `ReentrantLock` when the problem was about protecting shared variables. In my code, the counters are shared, so I used `counterLock` before changing them. I also used another lock called `logLock` for `executionLog`, because the log is also shared and it should not be updated by more than one thread at the same time.

For the semaphore, I used it in a different way. I used `cpuSemaphore` with one permit to control the CPU execution part. This means a process must acquire the semaphore before it executes, and then release it after finishing that part. I used one permit because the assignment wants the CPU access to be limited like a binary semaphore.]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

[Deadlock means that threads get stuck because each one is waiting for something that will not be released. For example, if a thread takes a lock and never releases it, other threads that need the same lock will keep waiting.

To avoid this, I used `finally` when releasing locks. This is important because the release code will still run even if an error or interruption happens. I used this idea with `counterLock`, `logLock`, and the CPU semaphore.

Another thing I tried to do is to keep the locked code short. I did not put the whole program inside the lock. I only locked the small part where the shared counter or log is updated, then released the lock directly.]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

[For Task 1, I used one lock called `counterLock` for the three counters. This means I used a coarse-grained lock. I chose this because the counter updates are small and simple, and using one lock made the code easier for me to follow.

The disadvantage is that if one thread is updating `contextSwitchCount`, another thread cannot update `totalWaitingTime` at the exact same time, even though they are different variables. So this can reduce concurrency a little. If I used separate locks for each counter, that would be fine-grained locking and it would allow more parallel updates.

For this assignment, I preferred one lock because it is clear and less confusing. But if the program was bigger or had many threads updating these counters a lot, then separate locks would probably be better for concurrency.]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 
`contextSwitchCount`, `completedProcessCount`,  `totalWaitingTime`.

**Why they need protection**: 
These variables are shared between the threads. The problem is that updating a counter is not always one step only. For example, the value can be read, changed, then written back. If two threads do this at almost the same time, one update may be lost and the final result may be wrong.

**Synchronization mechanism used**: 
I used `ReentrantLock`. I created one lock called `counterLock` and used it before updating the counter variables.

**Code snippet**:
```java
// Paste your implementation here
```
public static final ReentrantLock counterLock = new ReentrantLock();

public static void incrementContextSwitch() {
    counterLock.lock();
    try {
        contextSwitchCount++;
    } finally {
        counterLock.unlock();
    }
}

public static void incrementCompletedProcess() {
    counterLock.lock();
    try {
        completedProcessCount++;
    } finally {
        counterLock.unlock();
    }
}

public static void addWaitingTime(long time) {
    counterLock.lock();
    try {
        totalWaitingTime += time;
    } finally {
        counterLock.unlock();
    }
}
**Justification**: 
I used the lock because these counter updates are critical sections. Only one thread should update them at a time, otherwise the result can depend on the timing of the threads. I also put unlock() inside finally because I want to make sure the lock is released after the update.
---

### Critical Section #2: Execution Log

**What resource**: 

**Why it needs protection**: 

**Synchronization mechanism used**: 

**Code snippet**:
```java
// Paste your implementation here
```

**Justification**: 

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 

**Number of permits and why**: 

**Where implemented**: 

**Code snippet**:
```java
// Paste your implementation here
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
