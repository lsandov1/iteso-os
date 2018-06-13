# Scheduler

Problem: Tasks >> CPU Cores, so which to tasks to run? when? and how long?

One of the kernel's task tries to solve it efficiently and all this code is
what we call the 'scheduler'.

## Process Life-Cycle

Simplistic process life-cycle diagram

![alt text][plc]


## Kernel scheduler

Key functions of the kernel CPU scheduler are:

![alt text][ksf]


These are:

* **Time sharing**: multitasking between runnable threads, executing those
  with the highest priority first.
* **Preemption**: For threads that have become runnable at a high priority,
  the scheduler can preempt the current running thread, so that execution of
  higher-priority thread can begin immediately.
* **Load balancing**: moving runnable threads to the run queues of idle or
  less busy CPUs.

The figure shows run queues per CPU. There are also run queues per priority
level, so that the scheduler can easily manage which thread of the same
priority should run

### Linux

system timer interrupt -> `scheduler_tick()` -> (corresponding scheduler class
manage priorities and expiry of units of CPU time called *time slices*).


Preemption occurs when: threads become runnable and the scheduler class
`check_preempt_curr()` is called. Switching of threads is managed by
`__schedule()`, which selects the highest-priority thread via `pick_next_task()`
for running.

## Scheduling Classes

Scheduling classes manage the behaviour of runnable threads, specifically
their **priorities**, whether their on-CPU time is time-sliced and the duration of
those time slices (also know as *time quantum*). Additional controls via
**scheduling policies**, which may be selected within a scheduling class and
can control scheduling between threads of the same priority.

* **RT**: fixed and high priorities for real-time workloads.
* **O(1)**: Introduced in Linux 2.6 as the default time-sharing scheduler for
  user processes. Improve previous *O(n)* scheduler, dynamically improves the
  priority I/O-bound over CPU-bound workloads, to reduce latency of
  interactive and I/O workloads.
* **CFS**: Completely Fair Scheduling was added to the Linux 2.6.23 kernel as
  the default time-sharing scheduler for user processes. Uses red-black tree
  instead of tradition run queues.
  
![alt text][tsp]

### Scheduler policies

Scheduler policies are:

* **RR**: `SCHED_RR` is round-robin scheduling: Once a thread has used its
  time quantum, it is moved to the end of the run queue for that priority
  level, allowing others of the same priority to run.
* **FIFO**: `SCHED_FIFO`: is first-in first-out scheduling, which continues
  running the thread at the head of the run queue until it voluntary leaves,
  or a high-priority thread arrives.
* **NORMAL**:`SCHED_NORMAL` (previously known as `SCHED_OTHER`) is a
  time-sharing scheduling and is the default for user processes. The scheduler
  dynamically adjusts priority based on the scheduling class. For `O(1)`, the
  time slice duration is set based on the static priority: longer duration for
  higher-priority work. For CFS, the time slice is dynamic.
* **BATCH**: `SCHED_BATCH` is similar to `SCHED_NORMAL`, but with the
  expectation that the thread will be CPU-bound and should not be scheduler to
  interrupt other I/O-bound interactive work.

When there is no thread to run, a special *idle task* (also called *idle
thread*) is executed as a placeholder until another thread is runnable.
  
### Idle thread
  
The kernel "idle" thread (or "idle task") runs on-CPU when there is no other
runnable thread and has the lowest possible priority. It is usually
programmed to inform the process that CPU execution may be halted (halt
instruction) or throttled down to conserve power. The CPU will wake up on
the next HW interrupt.  


[plc]: images/process-life-cycle.jpg
[ksf]: images/kernel-scheduler-functions.jpg
[tsp]: images/thread-scheduler-priorities.jpg
