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

**Video Link**: [https://drive.google.com/file/d/1yrV7uvCB0HuDAVdud_tJQpWaWTaX5vfo/view?usp=sharing]

**Video filename**: `[445052777]_Assignment3_Synchronization.mp4`

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
I completed the assignment documentation and added the testing results.

**Challenges encountered**: 
The testing part took some time because I had to run the program more than once and compare the final values.

**How I solved it**: 
I ran the program several times, wrote down the results, and checked that the completed processes, context switches, and log entries were correct.

**Testing approach**: 
I used the terminal output from multiple runs and also tried a different time quantum for one test.

**Time spent**: 
3 hours
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
`executionLog`.

**Why it needs protection**: 
`executionLog` is shared between the threads, and it is an `ArrayList`. If more than one thread tries to add a message at the same time, the log may not be updated correctly. Some entries may be missed, or the order may become confusing.

**Synchronization mechanism used**: 
I used a separate `ReentrantLock` called `logLock`.

**Code snippet**:
```java
// Paste your implementation here
```
public static final ReentrantLock logLock = new ReentrantLock();

public static void logExecution(String message) {
    logLock.lock();
    try {
        executionLog.add(message);
    } finally {
        logLock.unlock();
    }
}
**Justification**: 
I used a separate lock for the log because it is a different shared resource from the counters. The lock makes sure only one thread can add to the log at a time. I also used `finally` so the lock will be released after adding the message.
---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 
The purpose of the semaphore is to control access to the CPU execution part. A process should acquire the semaphore before it starts executing, then release it when it finishes that part.

**Number of permits and why**: 
I used one permit only: `new Semaphore(1)`. I used one permit because I want only one process to enter the CPU execution section at a time.

**Where implemented**: 
I added `cpuSemaphore` inside the `SharedResources` class. Then I used `acquire()` at the beginning of the `run()` method, and `release()` in the `finally` block.

**Code snippet**:
public static final Semaphore cpuSemaphore = new Semaphore(1);
```

```java
try {
    SharedResources.cpuSemaphore.acquire();
} catch (InterruptedException e) {
    System.out.println(Colors.RED + "\n  ✗ " + name + " was interrupted." + Colors.RESET);
    return;
}

try {
    if (startTime == -1) {
        startTime = System.currentTimeMillis();
    }

    // process execution code

} finally {
    SharedResources.cpuSemaphore.release();
}
```

**Effect on program behavior**: 
The semaphore makes the CPU execution section controlled. If one process is already using the CPU part, another process must wait until the semaphore is released. This helps avoid two processes entering the same execution part at the same time.
---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
```bash
# Commands used (run the program at least 5 times)
```
javac SchedulerSimulationSync.java
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
```

**Results**: 
(Show that running multiple times produces consistent, correct results)
I ran the program five times. The program completed successfully every time and reached the final statistics section. The main values stayed consistent in all runs: the number of completed processes was 16, the number of context switches was 31, and the total log entries was 62.

 Run 1: completed processes = 16, context switches = 31, log entries = 62, total waiting time = 1184652ms
 Run 2: completed processes = 16, context switches = 31, log entries = 62, total waiting time = 1183298ms
 Run 3: completed processes = 16, context switches = 31, log entries = 62, total waiting time = 1182568ms
 Run 4: completed processes = 16, context switches = 31, log entries = 62, total waiting time = 1183720ms
 Run 5: completed processes = 16, context switches = 31, log entries = 62, total waiting time = 1182029ms
The waiting time was not exactly the same in every run, which is normal because it depends on real thread timing and system scheduling. However, the important final values stayed correct and the program did not crash.

**Why synchronization is necessary**: 
(Synchronization is necessary because the program has shared data. The counters such as `contextSwitchCount` and `completedProcessCount` are updated by threads, so without a lock one update might overwrite another update. Also, `executionLog` is shared, and without protection the log may miss entries or become unstable. The semaphore is also needed to control the CPU execution part, so only one process enters that part at a time.
.)

**Conclusion**: 
The test showed that the program is stable after adding synchronization. The locks protected the shared counters and the execution log, while the semaphore controlled access to the CPU execution section.
---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException
Checking for ConcurrentModificationException

**Testing procedure**: 
I ran the program several times and watched the terminal output until the program reached the final statistics. I focused on the execution log part because `executionLog` is an `ArrayList`, and it can cause problems if many threads update it without protection.

**Results**: 
The program finished normally in all runs. I did not get `ConcurrentModificationException`, and the program printed the execution log summary at the end. The total log entries was 62 in the runs I tested.

**What this proves**: 
This shows that protecting `executionLog` with `logLock` helped make the log update safer. Since only one thread can add to the log at a time, the `ArrayList` is not being updated by multiple threads at the same moment.
---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches)

**Expected values**: 
I expected the program to finish all created processes and show the final statistics without missing any process. Since my run created 16 processes, I expected `completedProcessCount` to be 16. I also expected the context switch count and log entries to stay the same between runs because the same student ID gives the same process setup.

**Actual values**: 
The program showed these values in the runs:
Total Completed Processes: 16
Total Context Switches: 31
Total log entries: 62
The total waiting time changed a little between runs, for example around `1182029ms` to `1184652ms`.

**Analysis**: 
The important values were correct because all 16 processes completed, and the context switches stayed at 31. The execution log entries also stayed at 62, which means the log update was not losing entries. The waiting time was slightly different between runs, but I think this is normal because it depends on the real timing of threads and when the system runs them. Overall, the final values show that the synchronization did not break the scheduler logic.
---

### Test 4: Different Scenarios
**Scenario tested**: [e.g., different time quantum, more processes, etc.]
I tested the program with a different time quantum. I changed the time quantum temporarily to `1000` to see if the program still works with a smaller quantum.

**Purpose**: 
The purpose was to check if the synchronization still works when the scheduling behavior changes. A smaller quantum can create more CPU turns, so I wanted to see if the counters, log, and semaphore still work correctly.

**Results**: 
The program completed successfully and reached the final statistics. The results were:
Total Context Switches: 24
 Total Completed Processes: 10
 Total Waiting Time: 147490ms
 Average Waiting Time: 14749ms
 Total log entries: 48
**What I learned**: 
Changing the time quantum changed the number of processes and the final statistics, but the program still completed without errors. This showed me that the synchronization code was still working even when the scenario was different. After testing, I changed the time quantum line back to the original code.
---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[I learned that synchronization is important when more than one thread uses the same data. Before this assignment, I thought a line like `counter++` is simple and safe, but now I understand it can cause a race condition because it is not really one step. I also learned that the critical section should be protected, but it should not be too large. If I lock too much code, the program may become slower or harder to understand. I used `ReentrantLock` for shared variables because I wanted only one thread to update them at a time. I used `Semaphore` for CPU access because it controls how many threads can enter that part. The hardest part for me was placing `acquire()` and `release()` correctly without causing errors.
]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 
Bank account transactions. If two withdrawals happen at the same time from the same account, the balance must be updated correctly. Without synchronization, both transactions may read the same old balance and the final balance can be wrong.

**Example 2**: 
Online ticket or seat booking. If two users try to book the last seat at the same time, the system must allow only one booking to succeed. Without synchronization, the same seat may be sold twice.

---

### How I would explain synchronization to others:

[I would explain synchronization like a key for a shared room. If many people want to enter the same room and change something inside, they should not enter all at once. One person takes the key, enters, finishes the work, then gives the key back. In the program, the shared room is the shared data, and the key is the lock or semaphore. This prevents two threads from changing the same thing at the same time and making the result wrong.]

---

## Part 6: GitHub Repository Information

**Repository URL**: https://github.com/ahmed-ali-445052777/OS-Assignment3-ahmed-ali


**Number of commits**: 8 commits

**Commit messages**: 
1. Set my student ID
2. Add protection for counters
3. Update development log
4. Protect execution log with lock
5. Add CPU semaphore
6.Update development log for synchronization tasks
7.finish part 2,3
8.Done Documentation 
---

## Summary

**Total time spent on assignment**: 
5:30 hours
**Key takeaways**: 
1.  I learned that shared variables can give wrong results if more than one thread updates them without protection.

2.  I learned how to use `ReentrantLock` for small critical sections like counters and the execution log.

3.  I learned that `Semaphore` can be used to control access to a shared resource, like the CPU execution part.


**Most challenging aspect**: 
The most challenging part was adding the semaphore in the correct place inside `run()` without breaking the code structure.

**What I'm most proud of**: 
I am proud that the program ran several times without errors and the final results stayed correct after adding synchronization.

---

**End of Documentation**
