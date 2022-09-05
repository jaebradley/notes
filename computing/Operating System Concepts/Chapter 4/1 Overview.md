# 4.1 Overview

* A thread is a basic unit of CPU utilization, it comprises a thread ID, a program counter, a register set, and a stack
  * Shares code section, data section, operating-system resources (open files, signals) with other threads belonging to the same process
* Examples: web processor has one thread for displaying graphics, another thread for responding to keystrokes, and a third for performing spelling and grammar checking
