**<p align = "center"> CS446-Summer22-PA1</p>
<p align = "center"> **Part 1, the Process API</p>**

In this part of the assignment, you will write a function called _launchProcesses_ in the **C language**. Main() will parse your command line arguments (such as _ls_), and then pass them to _launchProcesses_ 
launchProcesses.

The purpose of this portion of the assignment is to show you how Unix processes system calls from the command line using fork, wait, and execvp.
In other words, I'm asking you to write a very stripped down process API. Here's a pretty good resource if you're [stuck](https://www.section.io/engineering-education/fork-in-c-programming-language/) <br/>

You may only use the following libraries: <br/>
<stdio.h> <br/>
<string.h> <br/>
<stdlib.h> <br/>
<sys/wait.h> <br/>
<sys/types.h> <br/>
<unistd.h> <br/>
<fcntl.h> <br/>
<errno.h> <br/>
<sys/stat.h> <br/>


_main_ <br/>
**Input Parameters**: int argc, char* argv[] (description of command line arguments can be found [here]( https://www.tutorialspoint.com/cprogramming/c_command_line_arguments.htm) <br/>
**Returned Output**: int <br/>
**Functionality**: main uses supplied command line arguments as parameters to a call to launchProcesses. <br/>
argv[] should be passed to launchProcesses.
The return from launchProcesses should be stored and used as the return from main to indicate program success or failure.

 <br/>Note: this function does not need to worry about checking number of arguments but it should be able to execute an argument with parameters (such as _ls -la_). 
It should simply call the auxillary function and pass it a the argv array that can be used to run a basic execvp command. <br/>

_launchProcesses_

**Input Parameters**: char*[] <br/>

**Returned Output**: int <br/>

**Functionality**: launchProcesses uses execvp (https://linux.die.net/man/3/execvp) to execute a provided command as a process.  <br/>

This function should parse the command line input from the user that was passed in as a parameter
This function should fork (https://linux.die.net/man/3/fork) a child process for the provided argument.
The fork should be checked to see if the child process was successfully created (see below). 
Then execvp (https://linux.die.net/man/3/execvp) should be provided the command from the user that was passed into the funciton (_ls_) and any associated arguments (_ls -la_).
See General Directions if you don't know what portion of the char** array is the argument and what is the command.
The return from execvp ((https://linux.die.net/man/3/execvp)) should be stored to check for errors. 
Finally, after checking for errors, wait (https://linux.die.net/man/3/wait) should be used to wait for the process executed by execvp ((https://linux.die.net/man/3/execvp)) to finish before the parent process can move on.
If the process successfully executes and forks without error, return 0. Otherwise return 1. <br/>

**Edge Cases:** If a process is not successfully forked, your function should print ``fork failed!"

Hint: execvp returns -1 if it wasn't successful. <br/>

**<p = "center"> Part 2, Process Scheduling</b>**
**Background**

Process scheduling algorithms come in many different variations, and they have many different tradeoffs to consider like resource sharing and simplicity of execution. 
Understanding how First Come First Served, Shortest Remaining Job, and Priority Scheduling work will give you a better understanding of the ways in which these algorithms differ. 
_Waiting time_ measures the amount of time from when a process enters a queue to when the process is executed. 
_Turn around time_ measures the amount of time from when a process enters a queue to when the process is terminated.
The are both used in operating systems to determine CPU usage efficiency. 
**Directions**

There is a test batchfile for this assignment here() Each line of the batchfile contains 4 comma separated values. They are: PID, Arrival Time, Burst Time (also known as execution time), and Priority. PID is the process id. Arrival time is the time since the process arrived in the ready queue. Burst time is the amount of time required to fully execute the process on the CPU. Priority should only be used by the priority scheduling algorithm, and it decides which process should run first if more than one process arrives at the same time. Let's look at a simplified example of the batchfile:

1, 0, 20, 2

3, 0, 50, 1

7, 9, 4, 3

2, 10, 12, 4


Your program should consist of at least 5 functions: Main, FirstComeFirstServedSort, ShortestJobRemainingSort, PrioritySort, ComputeStat. Please note that the way Python implements main is different than the way that C or C++ implements it. Below, I provide the general description of each of the functions. You will notice that these descriptions are much less comprehensive than the first assignment. This is because I would like you to begin working on implementing algorithms from a general description (much like you would in an interview).

Name your program batchSchedulingComparison.py 

You can implement sorting in many ways in Python: you can take your data and create a tuple (or other object) and sort a list of those objects, 
you can zip, sort and unzip lists, you can create parallel lists and sort (not recommended since mistakes with this method are common), etc. 
In each of the sort functions, it is up to you to decide what data structures and process you use to implement the sorting algorithm. 
If you want to create a list of dict objects containing each of the variables and sort by key or item, that's fine. 
Or you could create three separate lists (one for PID, one for arrival time, and one for burst time) and sort them using zip/unzip. 
There are many ways to sort in python, so pick whatever makes the most sense to you.




Note: you will only use the priority entry in the batchfile to implement PriorityScheduling.

_main()_

From the terminal, the user should be able to enter the program name batchfileName and type of process sort they would like to do. 
So for example: python3 batchSchedulingComparison.py batchfile.txt FCFS could be entered on the commandline when you want to run the program. 
If the user does not enter your three arguments (program name batchfileName and sortType), then you should prompt the user repeatedly and take their input until they enter the correct number of arguments. 
There are many ways to accomplish this check. You will likely want to import sys and use sys.argv to get all of the arguments given from the command line.

Once the user supplies the correct number of arguments (which you can get by taking the length of the sys.argv list, see link above),  
use argv to get the batch file name, and then read all of the data from it, if you can. If you can't (because the user entered a non-existing file name),
you should tell the user that the file doesn't exist and exit the program.
If you're able to read from the file name provided by the user (again there are many ways to do this, but I like using readlines()), then you should get the algorithm name from the argv list. 
Expected algorithm names supplied by the user are FCFS and ShortestFirst and Priority (with that exact spelling and capitalization). 
If the user does not provide one of these arguments, you should tell the user that their process scheduling options are FCFS, ShortestFirst, or Priority, and exit the program. 
If the user enters an acceptable algorithm name, perform a logical check to see which function you should call (FirstComeFirstServedSort or ShortestJobRemainingSort or PrioritySort).
For each algorithm, the output to the terminal should be the processes in the order that they should execute, the average process waiting time, and the average process turn around time, each on their own line. 
All input and output should be gathered and executed IN MAIN.  In other words, your reading and printing should happen here. 
Examples of output for each algorithm are below, but please make sure that you print from main. 
The easiest way to do that is to have each of the algorithm functions return a list of the times that each process is completed at.
Then you can pass that and the relevant data to averageTurnaround and averageWait, whose returns can be used to print the turnaround and wait times.




_AverageTurnaround(processCompletionTimes, processArrivalTimes)_

Parameters: accepts the time that the process would be completed at by the algorithm, accepts the time that each process arrives (I suggest using two lists)

Returns: (1)the average turn around, (2)a list of each process turn around times (note: Python will let you return multiple values at once. For ease of implementation, you should do that in this function)

Turnaround time is calculated by subtracting each arrivalTime from its processCompletionTime. For example, using FCFS (see below) process 3 takes 50 seconds to execute and arrived at time 0, so process 3 has a turnaround time of 70 because it has to wait 20 seconds for process 1 to fully execute. To calculate the average, sum each process' turnaround time, and divide by the number of processes. So if we only executed process 1 and 3, we would add 20 and 70 and divide by 2- the turnaround time of those two processes averaged (ignoring the rest of the list for simplicity) is 45.




AverageWait(processTurnaroundTimes, processBurstTime)

Parameters: accepts the list of process turnaround times that is returned by AverageTurnaround, accepts the burst time of each process(I suggest using two lists)

Returns: (1)AverageWait

Wait time is calculated by subtracting each processBurstTime from its processTurnaroundTime. For example, using FCFS (see below) we previously calculated that process 3 has a turnaround time of 70, and process 1 has a turnaround time of 20. To calculate the waitTime for process 3, we subtract the burst time from the turnaround (70-50) and get 20; doing the same for process 1, we get 0. To calculate the average, sum each process' wait time, and divide by the number of processes. So if we only executed process 1 and 3, we would add 20 and 0and divide by 2- the wait time of those two processes averaged (ignoring the rest of the list for simplicity) is 10.

FirstComeFirstServedSort(batchFileData)

Parameters: accepts all of the batchFileData from the batchfile opened in main

Returns: (1) a list (or other data structure) of the time each process is completed at, and (2) a list (or other data structure) containing the PID of the processes in the order the algorithm sorted them by.

If the command line argument for the algorithm is FCFS, then this function will be called. The data from the batchfile should be passed in and sorted by arrival time (again, there are many ways to do this). The basic algorithm can be summarized as:

Sort processes by arrival time.
Execute in the order that they arrived.
If two processes arrive at the same time, execute the process with the smaller PID.

In the data structure of your choice, store the time that each process would be completed at. So, for example, process 1 would finish executing at time 20, process 3 would finish executing at time 70, and process 7 would finish executing at time 74.  Return your data structure with the completed times in it. 

Using the batchfile above as an example, the input would look like

python3 batchSchedulingComparison.py batchfile.txt FCFS

And the output  (FROM MAIN BASED ON YOUR RETURNED VALUES) to the screen would be:

PID ORDER OF EXECUTION

1

3

7

2

Average Process Turnaround Time: 57.75

Average Process Wait Time: 36.25




ShortestJobFirst(batchFileData)

Parameters: accepts all of the batchFileData from the batchfile opened in main

Returns: (1) a list (or other data structure) of the time each process is completed at, and (2) a list (or other data structure) containing the PID of the processes in the order the algorithm sorted them by.

If the command line argument states that the user wants to process batch jobs using ShortestFirst, then this function will be called. The data from the batchfile should be passed in and sorted by arrival time (again, there are many ways to do this).  For this version of shortest job first, as processes arrive, they are added to the queue. If the burst time of the newest process in the queue is less than the remaining time to execute the current process, the current process should be added back to the queue and the new process should be executed. So in the batchfile above, process 1 and 3 arrive at the same time, but process 1 has a burst time of 20 seconds, so we run process 1. At time = 9, process 7 enters the queue. Process 1 has 11 seconds of execution remaining. Process 7 has a burst time of 4. Therefore, we will pause process 1, save its new burst time (which is the remaining time), and execute process 7. At time 10, process 2 enters the queue. The current process, PID 7, has 3 seconds remaining until full execution, while process 2 needs 12 seconds to fully execute. Therefore, we continue to execute PID 7 until it completes, and check the queue for the process with the shortest remaining burst time- in this case, we would run PID 1 until it is fully executed, then PID 2, then PID 3. This would complete the batch file's process scheduling algorithm. Students have asked "what's the difference between this and Shortest Remaining Time?". Absolutely nothing, the preemptive version of Shortest Job First that you are implementing is the same thing as Shortest Remaining Time.  The basic algorithm can be summarized as:

At each time
Check what processes have arrived in the queue.
Compare the arrived process' burst time to the time that remains for the current process to fully execute.
If the newest process has a shorter burst time than the remaining time on the current process, update the burst time for the current process to be the remaining time. Then execute the new process with the shorter burst time.
Otherwise, continue executing the current process and decrement its remaining time.
If two processes arrive at the same time and have the same burst time, execute the process with the smaller PID first

The simplest way to check "at each time" is to sort all of the processes by arrival time, but there are a multitude of ways to simulate ShortestJobFirst. There are many different ways to update your process queue from the batch file. You can swap items in the batchFileData list. You can construct a dictionary object that tracks each process and the remaining burst time. You can simply update the burst time of a process each time you would have to pause the process.

Finally, print the PID values of the processes in the order that they will be executed by the algorithm, the average process waiting time, and average process turn around time. Using the example batchfile, the input would look like this

python3 batchSchedulingComparison.py batchfile.txt ShortestJobFirst

And the output  (FROM MAIN BASED ON YOUR RETURNED VALUES) would be:

PID ORDER OF EXECUTION:

1

7

1

2

3

Average Process Turnaround Time: 35.00

Average Process Wait Time: 13.5




PrioritySort(batchFileData)

Parameters: accepts all of the batchFileData from the batchfile opened in main

Returns: (1)a list (or other data structure) of the time each process is completed at, and (2) a list (or other data structure) containing the PID of the processes in the order the algorithm sorted them by.

If the command line argument states that the user wants to process batch jobs using Priority, then this function will be called. The data from the batchfile should be passed in and sorted by arrival (again, there are many ways to do this). The basic algorithm can be summarized as:

At each time
Check what processes are available for execution.
Compare the arrival times, and execute the process with the smallest arrival time.
If two or more processes have the same arrival time, consult each process' priority.

Of the processes that have the same arrival time, run the process with the lower priority value (lower priority value means higher priority, so priority 1 is always executed before priority 3).
If the processes have the same arrival time AND same priority value, execute from smallest to largest PID

This algorithm differs from ShortestJobFirst, because each process must fully execute before considering what processes are available in the queue.  Finally, print the PID values of the processes in the order that they will be executed by the algorithm, the average process waiting time, and average process turn around time. If you are performing an internet search, priority can be preemptive or non-preemptive. You should be creating a non-preemptive priority algorithm. Using the example batchfile, the input would look like this 

python3 batchSchedulingComparison.py batchfile.txt Priority

And the output (FROM MAIN BASED ON YOUR RETURNED VALUES)  would be:




PID ORDER OF EXECUTION:

3

1

7

2




Average Process Turnaround Time: 65.25

Average Process Wait Time: 43.75




Part 1 Requirements and Hints : 

Part 1 should be done in python3 (trust me, even with a learning curve, this is going to be way simpler in Python- I would prefer you have an understanding of the algorithms over an understanding of c syntax).
Note that the test batch file for grading will be different than the supplied batch file. Make sure you test a few different scenarios to ensure your algorithms are working right.
You do not need to actually create processes to implement these algorithms. You are simply deciding the order that processes would execute in, then outputting that, the average turnaround time, and the average wait time.
All file input and output should be performed in main. Nothing should be printed from the process sorting functions, or the averageWait or averageTurnaround functions.
The only thing in your zip or tar.gz should be your copy of batchSchedulingComparison.py

