# Process Scheduler

The process scheduler (located at the kernel) deals with *processes* so lets
review what are these entities.

## Process

A instance of a program (object code stored on some media) running. On a
modern OS, even on *idle*, there are many process running (kernel and user
space processes).

To see all processes

```bash
ps -aux
```

Or top CPU process consumers

```bash
top
```
### Process States

![alt text][ps]

Look at the `STAT` column

```bash
ps -aux
```

In UNIX systems, `man` pages are great for leaning. Open `ps` man page and
look for *PROCESS STATE CODES*.

## Process Scheduler

A kernel subsytems that puts process to work. Decides **which** process to run,
**when**, and for **how long**.

The scheduler is the basis of a multitasking operating system such as Linux.

The scheduler is responsible for **best utilizing** the system and giving
users the impression that multiple process are executing simultaneously.


## Multitasking

A *multitasking* OS is one that can simultaneously interleave execution of
more that one process.

**Single processor machines**: gives the illusion of multiple processes running
concurrently.

**Multiprocessor machines**: process actually run concurrenlty, in parallel,
on different processors.

Not all process run at the same time, so many procceses to *block* or *sleep*.

Multitasking OSs come in two flavors: *coopertive multitasking* and
*preemptive multitasking*. Most modern OS are preemptive, so the scheduler
decides when a process is to cease running and a new process is to begin
running.

## Linux's process scheduler

A [phrase][linus-easy-scheduler-2001] from Linus Torvalds about scheduling in 2001

> And you have to realize that there are not very many things that have
> aged as well as the scheduler. Which is just another proof that scheduling
> is easy.

But the **multicore** came and complexity came to this area.


```bash
cat /proc/cpuinfo
```

**First scheduler**: Pros: First version of the Linux's scheduler was simple. Cons: Easy to understand, but
scaled poorly in light of many runnable processes or many processors.

**`O(1)`**: Pros: Fast, scalable on large "iron" with tens if not hundreds of
processors. Cons: pathological failures related to scheduling
latency-sensitive applications, called *interactive processes*, so it was good
for batch runs in servers but bad for desktops.

**CFS**: Completely Fair Scheduler. Improves previous scheduler and currently
is the default in Linux.


## Policy

defines **what* runs and *when*. Often determines the overall feel of a system
and is responsible for optimally utilizing processor time.

### I/0-Bound Versus Process-Bound Processes

### Process Priority

Linux implements two separate priority ranges: *nice* and *real-time* priotity

Nice:

```bash
ps -el # for nice values
ps -eo state,uid,pid,ppid,rtprio,time,comm # for real-time values
```


### Timeslice

Numeric value that represents how long a task can run until it is preempted.

Too causes causes the system to have a poor interactive performance and too
short causes significant amounts of processor time to be wasted on switching processes.

The Linux's CFS scheduler does not directly assign timeslices to process: it
provides a **proportion** of the processor, so the assigned timeslice is a
function of the **load of the system** and further affected by each process's
nice value.

## The Linux scheduilng algorithm

## Scheduler classes

Each class defines/enable different algorithms to schedule different types of
processes. Classes have priorities so several scheduler classes can coexist.

CFS is registered for normal processes, called `SCHED_NORMAL` in Linux.

## Fair Scheduling

CFS is based on a simple concept: Model process scheduling as if the system
had an ideal, perfectly multitasking processor: each process would recive
`1/n` of the processor's time, where `n` is the number of runnable processes,
and we'd schedule them for infinitely small durations, so any measureble
period we'd have run all `n` processes for the same amount of time.

[ps]:  images/process-states.jpg

[linus-easy-scheduler-2001]: http://tech-insider.org/linux/research/2001/1215.html
