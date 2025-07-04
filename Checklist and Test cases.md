# **Checklist and Test cases**  

created:   18/03/2025   
updated: 04/07/2025  

## 🍴🍝🤔 PHILOSOPHERS 😴😵
============================
INDEX:  
├── [1] Philosophers Project Overview (check file "Project_Philosophers")  
├──	[2] THREADS / Synchronization / Mutex lock (check file "Project_Philosophers")  
├──	[3] Managing Threads’ Shared Memory (check file "Project_Philosophers")  
├── [4] Links / Bibliography(check file "Project_Philosophers")  
├── [5] BUILDING THE PROJECT (check file "Building_the_table")  
└── [6] CHECKLIST AND TEST CASES (check file "Testing_philosophers")  
		├── SUMMARY  
		├── CHECKLIST  
		├── TEST CASES  
		└── CONSIDERATIONS REGARDING VALGRIND AND HELGRIND  
  
## ⭐⭐PHILOSOPHERS - EVALUATION:⭐⭐  
==============================

📢 The content of this file was created by Maria Sofia Piantan! I just added some details.   

### 🧵 Summary:
----------------

1) check if there is any global variables : norminette will flag them.   

2) check there is 1 thread per philosopher:  
- Each philosopher is assigned a thread within the loop in init_threads(), ensuring one thread per philosopher.  
- (memory allocation is also determine by the number of philosophers , in init_data())  
  
3) check there is only one fork per philosopher:   
- in init_data(): data->forks gets as much memory as number of philos  
- in init_forks(): left fork is assign according to number of philo (its index). Then the other one that will be assign will belong to the "neighbour" philosopher.  

4) check if here is a mutex per fork and that it´s used to check the fork and/or change it:  
- in init_data_mutexes() , while loop asigns 1 fork_mutex per fork  

5) check the outputs are never mixed up:  
- print_msg() uses print_lock. The print_lock mutex prevents mixed-up output by enforcing exclusive access during message printing  

6) check how the death of a philosopher is verified and if there is a mutex to prevent a philosopher from dying and starting to eat at the same time:  
- philo_routine() checks for simulation status and chcks for dead-flag ON before eating.   
- take_forks() also checks for death before calculating which fork to take.   
- eat_philos() ALSO has a check before doing anything else.  
they all use has_died()   
- has_died() function in simulation.c . It uses a mutex to safely access the dead flag before allowing further actions. The death_lock mutex ensures consistency, preventing a philosopher from eating after being marked as dead.  


### 🧵 Checklist  
-----------------

1. Compilation & Code Structure  

[  ] Program compiles without warnings using gcc and -Wall -Wextra -Werror.  
[  ] All functions adhere to 42 Norminette rules.  
[  ] No memory leaks (test with valgrind --leak-check=full ./philo).  
[  ] Code is modular (separate files for validation, initialization, routines, monitoring, printing, and cleanup).  
[  ] No global variables (verified using norminette --checkForbidden).  

2. Argument Parsing and Validation  

[  ] Check if the argument count is between 5 and 6.  
[  ] Check amount of philos is 1 to 200.  
[  ] Validate that arguments are positive integers.  
[  ] Handle invalid input gracefully (error message and proper return).  
[  ] Edge Cases: Zero or negative numbers should return an error.  

3. Initialization of Data Structures  

[  ] Allocate memory for philosophers and forks.  
[  ] Initialize mutexes (forks, print_lock, death_lock, mut_die_t).  
[  ] Initialize philosopher’s attributes:   
		- id  
		- meals_eaten  
		- last_meal_time  
		- left_fork and right_fork  
[  ] Set dead flag to 0 (not dead).  

4. Threads and Actions  
 
[  ] Create one thread per philosopher.   
[  ] Handle one-philosopher case: He should die after picking up a fork.  
[  ] Implement philosophers’ actions:  
		Thinking  
		Picking up forks  
		Eating  
		Sleeping   
[  ] Implement a monitor thread:  
		Check philosopher deaths.  
		Verify if all philosophers have eaten enough.  
		Ensure proper synchronization.  
[  ] Time Management:  
		Track timestamps using gettimeofday().  
		Use usleep() or similar delay function to sync actions.  
[  ] Cleanup:  
		Destroy all mutexes.  
		Free allocated memory.  

5. Input Handling & Error Management  

[  ] Handle invalid arguments correctly.  
[  ] Handle invalid argument count gracefully.  
[  ] Handle memory allocation failures.  
[  ] Check for deadlocks: Ensure correct mutex locking/unlocking.  
[  ] Ensure no out-of-bounds memory access.  

6. Debugging & Performance  

[  ] 🧪 Valgrind: No memory leaks (valgrind --leak-check=full ./philo).  
[  ] 👺 Helgrind: No thread errors (valgrind --tool=helgrind ./philo).  
[  ] Test with different numbers of philosophers.  
[  ] Edge Cases:  
		One philosopher (should die after taking a fork).  
		Philosophers reach meal goal and stop.  
[  ] Test extreme time constraints: very short or very long time values.  
[  ] No segmentation faults or crashes.  
[  ] Check thread synchronization.  

### 🧵 Test Cases  
----------------

#### Mandatory Part Test Cases  
-----------------------------

**Basic Cases (No Philosopher Should Die)**  
./philo 5 800 200 200  
./philo 7 800 200 350  
./philo 4 600 200 200  
./philo 2 1000 200 400  
./philo 5 600 150 150  
./philo 4 410 200 200  
./philo 100 800 200 200  
./philo 105 800 200 200  
./philo 200 800 200 200  

**Simulation Should Stop After Eating N Meals**  
./philo 4 410 200 200 5  
./philo 2 500 200 200 10  
./philo 6 1000 400 400 2  
./philo 3 800 200 200 2  
./philo 5 800 200 200 7  
./philo 4 410 200 200 10   

**Edge Cases**  
**Single Philosopher (Should Die)**  
./philo 1 800 200 200   

**Philosophers Die Quickly (time_to_die < time_to_eat + time_to_sleep)**  
./philo 5 200 200 200  
./philo 5 200 100 100  
./philo 6 100 70 50  
./philo 4 310 200 100  
./philo 4 200 205 200  

**High Number of Philosophers**  
./philo 200 800 200 200  
  
**Error Cases**  
./philo                # No input  
./philo 5 200 100 100 5 42  # Too many arguments  
./philo five 200 100 100  # Invalid input  
./philo 4 200 100 100 2,  # Invalid input (comma)  
./philo 5 -200 100 100  # Negative values  
./philo 0 800 200 200  # Zero values  
./philo 5 800 200 200 0  # 0 meals should be invalid  

**Memory and Thread Testing** 🧪👺  
valgrind ./philo 5 200 100 100  # Memory leak check  
valgrind --leak-check=full --show-leak-kinds=all ./philo 1 300 300 300   
valgrind --tool=helgrind ./philo 4 410 200 200 5  # Race condition check  
valgrind --tool=helgrind ./philo 1 300 300 300  

#### **Bonus Part Test Cases (`./philo_bonus`)**  

**Basic Cases (No Philosopher Should Die)**  
./philo_bonus 5 800 200 200  
./philo_bonus 7 800 200 350  
./philo_bonus 4 600 200 200  
./philo_bonus 2 1000 200 400  
./philo_bonus 5 600 150 150  
./philo_bonus 4 410 200 200  
./philo_bonus 100 800 200 200  
./philo_bonus 105 800 200 200  
./philo_bonus 200 800 200 200  
  
**Simulation Should Stop After Eating N Meals**  
./philo_bonus 4 410 200 200 5  
./philo_bonus 2 500 200 200 10  
./philo_bonus 6 1000 400 400 2  
./philo_bonus 3 800 200 200 2  
./philo_bonus 5 800 200 200 7  
./philo_bonus 4 410 200 200 10  

#### **Edge Cases**  
**Single Philosopher (Should Die)**  
./philo_bonus 1 800 200 200  

**Philosophers Die Quickly (time_to_die < time_to_eat + time_to_sleep)**  
./philo_bonus 5 200 200 200  
./philo_bonus 5 200 100 100  
./philo_bonus 6 100 70 50  
./philo_bonus 4 310 200 100  
./philo_bonus 4 200 205 200  
  
#### **Memory and Thread Testing**  
valgrind ./philo_bonus 5 200 100 100  # Memory leak check  
valgrind --trace-children=yes ./philo_bonus 4 410 200 200 5  # Race condition check  

--- 

### 🧵 Considerations wen using Valgrind or Helgrind  
------------------------------------------------
When you run your program under **Valgrind** or **Helgrind**, especially for a multi-threaded simulation like **Philosophers**, these tools can interfere with timing and synchronization. Here's **why that happens**:   

🧠 How do Valgrind & Helgrind Work?  
- **Valgrind** is a *dynamic binary instrumentation* framework. It intercepts **every memory operation**, system call, and thread-related instruction to track:  
  - Memory leaks  
  - Invalid accesses  
  - Thread races (Helgrind)  
- This means that **every single operation** in your code is being **wrapped, analyzed, and reported** by Valgrind.  
- Helgrind (a Valgrind tool) adds **additional tracking** for mutexes, condition variables, and memory access patterns between threads.  

🐢 Why Threads Run Slower or Out of Sync  
1. **Timing overhead:**  
   - What normally takes a few microseconds may now take milliseconds.  
   - For example: `usleep(200)` may be delayed under Valgrind because it's not just sleeping — Valgrind is tracking everything happening before and after that sleep.  

2. **Race timing is affected:**  
   - Normally synchronized threads can now **appear out-of-sync** due to Valgrind's slowdown.  
   - Deadlines like `time_to_die` might be missed even if they're met under normal execution.  

3. **Syscall latency:**  
   - Accessing `gettimeofday()` or using `pthread_mutex_lock()` might take much longer under Valgrind.  

4. **Thread scheduling distortion:**  
   - Valgrind has **no control over actual OS thread scheduling**, but the slowdown it causes leads to **non-deterministic execution** that can expose (or simulate) thread starvation or desynchronization that doesn't happen at native speed.  

🛠️ What You Can Do About It?  
- When testing under Valgrind:  
  - Use **longer values** for `time_to_die`, `time_to_eat`, and `time_to_sleep`.  
  - Example: `./philo 5 2000 800 800` instead of `./philo 5 800 200 200`  
- Helgrind is still **very useful** to catch race conditions — just don’t rely on it for *performance-sensitive behavior* like tight timing windows.  

🧪 Summary :   
Valgrind/Helgrind slows down your program and affects thread timing. That’s expected, because they analyze every memory and thread operation. For timing-based simulations like *Philosophers*, this can lead to philosophers dying *under Valgrind* when they wouldn’t under normal execution.  

GOOD LUCK WITH YOUR PROJECT !  
