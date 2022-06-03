**<p align = "center"> CS446-Summer22-PA1</p>**

**Learning Outcomes**
1. You will implement the general system process call API in C using fork, wait, and execvp. You will be able to describe how Unix implements general process execution of a child from a parent process.
2. You will simulate priority sort and shortest remaining job process scheduling algorithms. You will be able to compare and contrast the ease of implementation and the effect on turnaround and wait times.

**General Instructions and Hints**: 

-Name files exactly as described in the documentation below.<br/> 
-All functions should match the assignment descriptions. Do not add parameters, change names, or return different values. <br/> 
-All output should match exactly what is in this document (including spacing!). If it does not match, it will not pass the autograder.<br/> 
-When parts 1 and 2 are done, open a terminal and cd to your github repo, wherever you saved it. Do the following: _git add ._ then _git commit -m <whatevermessageyouwant>_ then _git push_.<br/>
-All work should be done on a machine where you have sudoer permission. <br/>
-All work should be your own. <br/>



**<p align = "center"> Part 1, the Process API</p>**
 **Background** <br/>
 Normally, when you log in, the OS will create a user process that binds to the login port; this causes the user process at that port to execute a shell. A shell (command line interpreter) allows the user to interact with the OS and the OS to respond to the user. The shell is a character-oriented interface that allows users to type characters terminated by Enter (the \n char). The OS will respond by sending characters back to the screen. If the OS is using a GUI, the window manager software will take over any shell tasks, and the user can access them by using a screen display with a fixed number of characters and lines. We commonly call this display your terminal, shell, or console, and in Linux, it will output a prompt, which is usually your user@machineName followed by the terminal’s current place in the file system and then a $ (see Figure 2).  

 

Common commands in Linux use bash, and usually take on the form of<br/>

command argument1 ... argumentN

For example, in
_chmod u+x <filename>_

_chmod_ is the command, _u+x _is an argument and <filename> is an argument. Not all commands require arguments- for example, you can run ls with and without any arguments. After entering a command at the prompt, the shell creates a child process to execute whatever command you provided. The following are the steps that the shell must take to be functional: 

1) Print a prompt and wait for input. <br/>
2) Get the command line input.<br/>
3) Parse the command line input.<br/>
4) Find associated files.<br/>
5) Pass any parameters from the shell to the OS command function.<br/>
6) Execute the command (with applicable parameters).<br/>

**General Directions** <br/>
Name your program _commandLauncher.c_ and your executable _commandLauncher_ . You will turn in the C code **and** executable. **Failure to do so will result in a 0 on this portion.** In this part of the assignment, you will write a function called _launchProcesses_ in the **C language**. Main() will parse your command line arguments (such as _ls_), and then pass them to _launchProcesses_.

The purpose of this portion of the assignment is to show you how Unix processes system calls from the command line using fork, wait, and execvp.
In other words, I'm asking you to write a very stripped down process API. Here's a pretty good resource if you're [stuck](https://www.section.io/engineering-education/fork-in-c-programming-language/) <br/>

You may only use the following libraries: <br/>
 ```
<stdio.h> 
<string.h> 
<stdlib.h> 
<sys/wait.h> 
<sys/types.h> 
<unistd.h> 
<fcntl.h> 
<errno.h> 
<sys/stat.h> 
```

_main_ <br/>
**Input Parameters**: int argc, char* argv[] (description of command line arguments can be found [here]( https://www.tutorialspoint.com/cprogramming/c_command_line_arguments.htm)) <br/>
**Returned Output**: int <br/>
**Functionality**: main uses supplied command line arguments as parameters to a call to launchProcesses. <br/>
argv[] should be passed to launchProcesses.
The return from launchProcesses should be stored and used as the return from main to indicate program success or failure.

 <br/>Note: this function does not need to worry about checking number of arguments but it should be able to execute an argument with parameters (such as _ls -la_). 
It should simply call the auxillary function and pass it the argv array that can be used to run a basic execvp command. <br/>
 

_launchProcesses_<br/>
**Input Parameters**: char*[] <br/>
**Returned Output**: int <br/>
**Functionality**: launchProcesses uses [execvp](https://linux.die.net/man/3/execvp) to execute a provided command as a process.  <br/>
This function should parse the command line input from the user that was passed in as a parameter.
This function should [fork](https://linux.die.net/man/3/fork) a child process for the provided argument.
The fork should be checked to see if the child process was successfully created (see below). 
Then [execvp](https://linux.die.net/man/3/execvp) should be provided the command from the user that was passed into the funciton (_ls_) and any associated arguments (_ls -la_).
See General Directions if you don't know what portion of the char** array is the argument and what is the command.
The return from [execvp](https://linux.die.net/man/3/execvp) should be stored to check for errors. 
Finally, after checking for errors, [wait](https://linux.die.net/man/3/wait) should be used to wait for the process executed by [execvp](https://linux.die.net/man/3/execvp) to finish before the parent process can move on.
If the process successfully executes and forks without error, return 0. Otherwise return 1. <br/>

**Edge Cases:** If a process is not successfully forked, your function should print _fork failed!_

Hint: execvp returns -1 if it wasn't successful. <br/>

 
 
 
 **<p align = "center"> Part 2, Process Scheduling</p>**<br/>
**Background**

Process scheduling algorithms come in many different variations, and they have many different tradeoffs to consider like resource sharing and simplicity of execution. 
Understanding how Shortest Remaining Job, and Priority Scheduling work will give you a better understanding of the ways in which these algorithms differ. 
_Waiting time_ measures the amount of time from when a process enters a queue to when the process is executed. 
_Turn around time_ measures the amount of time from when a process enters a queue to when the process is terminated.
The are both used in operating systems to determine CPU usage efficiency. <br/>

 **Directions**

The code for this portion should be written in **Python** (trust me, it's easier than C). There is a test batchfile for this assignment located in the repo. Each line of the batchfile contains 4 comma separated values. They are: PID, Arrival Time, Burst Time (also known as execution time), and Priority. PID is the process id. Arrival time is the time since the process arrived in the ready queue. Burst time is the amount of time required to fully execute the process on the CPU. Priority should only be used by the priority scheduling algorithm, and it decides which process should run first if more than one process arrives at the same time. Let's look at a simplified example of the batchfile:
 
```
1, 0, 20, 2
3, 0, 50, 1
7, 9,  4, 3
2, 10, 12, 4
```

Your program should consist of 4 functions: Main, ShortestRemainingSort, PrioritySort, ComputeStat. [Please note that the way Python implements main is different than the way that C or C++ implements it](https://www.geeksforgeeks.org/python-main-function/). Below, I provide the general description of each of the functions. You will notice that these descriptions are much less comprehensive than the first assignment. This is because I would like you to begin working on implementing algorithms from a general description (much like you would in an interview).

Name your program _batchSchedulingComparison.py_

You can implement sorting in many ways in Python: you can take your data and create a tuple (or other object) and sort a list of those objects, 
you can zip, sort and unzip lists, you can create parallel lists and sort (not recommended since mistakes with this method are common), etc. 
In each of the sort functions, it is up to you to decide what data structures and process you use to implement the sorting algorithm. 
If you want to create a list of dict objects containing each of the variables and sort by key or item, that's fine. 
Or you could create three separate lists (one for PID, one for arrival time, and one for burst time) and sort them using zip/unzip. 
There are many ways to sort in python, so pick whatever makes the most sense to you.




Note: you will only use the priority entry in the batchfile to implement PriorityScheduling.<br/>
 

_main()_<br/>

From the terminal, the user should be able to enter the program name batchfile Name and type of process sort they would like to do. 
So for example: python3 batchSchedulingComparison.py batchfile.txt Priority could be entered on the commandline when you want to run the program. 
If the user does not enter your three arguments (program name batchfileName and sortType), then you should return 1 for failure print the following:
 
 ```
 Please provide command line arguments when running.
 python3 batchSchedulingComparison.py batchfile.txt Priority
 ```
 
There are many ways to accomplish this check. You will likely want to _import sys_ and use sys.argv to get all of the arguments given from the command line.

Once the user supplies the correct number of arguments (which you can get by taking the length of the sys.argv list, see link above),  
use argv to get the batch file name, and then read all of the data from it, if you can. If you can't (because the user entered a non-existent file name),
you should return 1, and print the following:
```
 Input batchfile not found!
 ```
 
If you're able to read from the file name provided by the user (again there are many ways to do this, but I like using _readlines()_), then you should get the algorithm name from the argv list. 
Expected algorithm names supplied by the user are and ShortestRemaining and Priority (with that exact spelling and capitalization). 
If the user does not provide one of these arguments, you should return 1 and output the following:
 ```
 Unidentified sorting algorithm. Please input either ShortestRemaining or Priority.
 ```
 
If the user enters an acceptable algorithm name, perform a logical check to see which function you should call ( ShortestRemaining or PrioritySort). Call the appropriate algorithm, which returns a list of process execution times and sorted (by algorithm) PIDs. Call ComputeStats to get the average wait time and average turnaround time. 
For each algorithm, the output to the terminal should be the processes in the order that they should execute (each on their own line), the average process waiting time, and the average process turn around time, each on their own line. 
All input and output should be gathered and executed IN MAIN.  In other words, your reading and printing should happen here. 
Examples of output for each algorithm are below, but please make sure that you print from main. 
The easiest way to do that is to have each of the algorithm functions return a list of the times that each process is completed at.
Then you can pass that and the relevant data to ComputeStats, whose returns can be used to print the turnaround and wait times.<br/>

_ComputeStat(processCompletionTimes, processArrivalTimes, processBurstTimes)_<br/>
**Parameters**: accepts the time that the process would be completed at by the algorithm, accepts the time that each process arrives (I suggest using two lists)

**Returns**: (1)the average turnaround, (2)a list of each process turnaround times, (3)the average wait time and (4)a list of each process wait times (note: Python will let you return multiple values at once. For ease of implementation, you should do that in this function)

This function calculates your average turnaround time and average wait time stats, as well as the turnaround and wait time for each process (as lists).
 
Turnaround time is calculated by subtracting each processArrivalTime from its processCompletionTime. For example, using FCFS process 3 takes 50 seconds to execute and arrived at time 0, so process 3 has a turnaround time of 70 because it has to wait 20 seconds for process 1 to fully execute. To calculate the average, sum each process' turnaround time, and divide by the number of processes. So if we only executed process 1 and 3, we would add 20 and 70 and divide by 2- the turnaround time of those two processes averaged (ignoring the rest of the list for simplicity) is 45.

Wait time is calculated by subtracting each processBurstTime from its processTurnaroundTime. For example, using FCFS, we previously calculated that process 3 has a turnaround time of 70, and process 1 has a turnaround time of 20. To calculate the waitTime for process 3, we subtract the burst time from the turnaround (70-50) and get 20; doing the same for process 1, we get 0. To calculate the average, sum each process' wait time, and divide by the number of processes. So if we only executed process 1 and 3, we would add 20 and 0, and divide by 2- the wait time of those two processes averaged (ignoring the rest of the list for simplicity) is 10.<br/>

_ShortestRemainingSort(batchFileData)_<br/>

**Parameters**: accepts all of the batchFileData from the batchfile opened in main

**Returns**: (1) a list (or other data structure) of the time each process is completed at, and (2) a list (or other data structure) containing the PID of the processes in the order the algorithm sorted them by.

If the command line argument states that the user wants to process batch jobs using ShortestRemaining, then this function will be called. The data from the batchfile should be passed in and sorted by arrival time (again, there are many ways to do this).  For this version of shortest remaining, as processes arrive, they are added to the queue. If the burst time of the newest process in the queue is less than the remaining time to execute the current process, the current process should be added back to the queue and the new process should be executed. So in the batchfile above, process 1 and 3 arrive at the same time, but process 1 has a burst time of 20 seconds, so we run process 1. At time = 9, process 7 enters the queue. Process 1 has 11 seconds of execution remaining. Process 7 has a burst time of 4. Therefore, we will pause process 1, save its new burst time (which is the remaining time), and execute process 7. At time 10, process 2 enters the queue. The current process, PID 7, has 3 seconds remaining until full execution, while process 2 needs 12 seconds to fully execute. Therefore, we continue to execute PID 7 until it completes, and check the queue for the process with the shortest remaining burst time- in this case, we would run PID 1 until it is fully executed, then PID 2, then PID 3. This would complete the batch file's process scheduling algorithm. The basic algorithm can be summarized as:

At each time (think for loop)
     Check what processes have arrived in the queue.
     Compare the arrived process' burst time to the time that remains for the current process to fully execute.
     If the newest process has a shorter burst time than the remaining time on the current process, update the burst time for the current process to be the remaining time. Then execute the new process with the shorter burst time.
     Otherwise, continue executing the current process and decrement its remaining time.
     If two processes arrive at the same time and have the same burst time, execute the process with the smaller PID first

The simplest way to check "at each time" is to sort all of the processes by arrival time, but there are a multitude of ways to simulate ShortestRemaining. There are many different ways to update your process queue from the batch file. You can swap items in the batchFileData list. You can construct a dictionary object that tracks each process and the remaining burst time. You can simply update the burst time of a process each time you would have to pause the process.

Finally, print the PID values of the processes in the order that they will be executed by the algorithm, the average process waiting time, and average process turn around time. Using the example batchfile, the input would look like this:
 
```
python3 batchSchedulingComparison.py batchfile.txt ShortestRemaining
 ```

And the output  (FROM MAIN BASED ON YOUR RETURNED VALUES) would be:
 ```

PID ORDER OF EXECUTION:

1
7
1
2
3

Average Process Turnaround Time: 35.00
Average Process Wait Time: 13.5
```



_PrioritySort(batchFileData)_ <br/>

Parameters: accepts all of the batchFileData from the batchfile opened in main

Returns: (1)a list (or other data structure) of the time each process is completed at, and (2) a list (or other data structure) containing the PID of the processes in the order the algorithm sorted them by.

If the command line argument states that the user wants to process batch jobs using Priority, then this function will be called. The data from the batchfile should be passed in and sorted by arrival (again, there are many ways to do this). The basic algorithm can be summarized as:

At each time
     Check what processes are available for execution.
     Compare the arrival times, and execute the process with the smallest arrival time.
     If two or more processes have the same arrival time, consult each process' priority.

Of the processes that have the same arrival time, run the process with the lower priority value (lower priority value means higher priority, so priority 1 is always executed before priority 3).
If the processes have the same arrival time AND same priority value, execute from smallest to largest PID

This algorithm differs from ShortestRemaining, because each process must fully execute before considering what processes are available in the queue.  Finally, print the PID values of the processes in the order that they will be executed by the algorithm, the average process waiting time, and average process turn around time. If you are performing an internet search, priority can be preemptive or non-preemptive. You should be creating a non-preemptive priority algorithm. Using the example batchfile, the input would look like this: 
 
 ```
python3 batchSchedulingComparison.py batchfile.txt Priority
 ```

And the output (FROM MAIN BASED ON YOUR RETURNED VALUES) would be:

```
PID ORDER OF EXECUTION:

3
1
7
2

Average Process Turnaround Time: 65.25
Average Process Wait Time: 43.75
```



**Part 2 Requirements and Hints**: 

Part 1 should be done in python3 (trust me, even with a learning curve, this is going to be way simpler in Python- I would prefer you have an understanding of the algorithms over an understanding of c syntax). <br/>
Note that the test batch file for grading will be different than the supplied batch file. Make sure you test a few different scenarios to ensure your algorithms are working right. <br/>
You do not need to actually create processes to implement these algorithms. You are simply deciding the order that processes would execute in, then outputting that, the average turnaround time, and the average wait time. <br/>
All file input and output should be performed in main. Nothing should be printed from the process sorting functions, or the averageWait or averageTurnaround functions. <br/>

 **To Submit**<br/>
 When you are done, you should use git to git push the following to your assignment repo (see instructions above):
 1) _commandLauncher.c_
 2) _commandLauncher_ (your executable must have this name and be submitted)
 3) _batchSchedulingComparison.py_

You can submit as many times as you would like, and you can check whether your code is functioning correctly using the autograder.
