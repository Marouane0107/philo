# Philosophers 🍲

A C implementation of the Dining Philosophers problem, a classic computer science challenge focusing on synchronization and concurrency. This 42 School project requires managing multiple threads (philosophers) and shared resources (forks) without causing deadlocks or starvation.

## 📋 Table of Contents

- [About](#about)
- [Core Concepts](#core-concepts)
- [Features](#features)
- [Installation](#installation)
- [Usage](#usage)
- [Simulation Parameters](#simulation-parameters)
- [Synchronization Strategy](#synchronization-strategy)
- [Project Structure](#project-structure)
- [Testing](#testing)
- [Error Handling](#error-handling)
- [Author](#author)
- [Notes](#notes)

## 🎯 About

The Philosophers project is a cornerstone of the 42 School curriculum, designed to teach students about:
- **Threads**: Creating and managing multiple concurrent execution paths.
- **Mutexes**: Using mutual exclusion locks to protect shared data (forks) and prevent race conditions.
- **Synchronization**: Coordinating the actions of multiple threads to achieve a common goal.
- **Deadlock Prevention**: Designing algorithms that avoid situations where threads are perpetually blocked, waiting for each other.
- **Resource Management**: Efficiently handling shared resources in a concurrent environment.

The primary goal is to simulate a group of philosophers at a round table who alternate between thinking, eating, and sleeping. Each philosopher needs two forks to eat, and the challenge is to ensure they can do so without violating the problem's constraints or leading to system failure.

## 🧠 Core Concepts

The simulation revolves around the lifecycle of each philosopher and their interaction with shared resources:

- **Philosophers**: Each philosopher is represented by a separate thread.
- **Forks**: Forks are shared resources, represented by mutexes. A philosopher needs to acquire two forks (one from their left, one from their right) to eat.
- **States**: Each philosopher cycles through three states:
    1.  **Thinking**: The philosopher is contemplating.
    2.  **Eating**: The philosopher has acquired two forks and is eating for a specified duration.
    3.  **Sleeping**: After eating, the philosopher puts down the forks and sleeps for a specified duration.
- **Death Condition**: A philosopher "dies" if they cannot start eating within a `time_to_die` milliseconds from the beginning of their last meal (or the start of the simulation). The simulation ends if any philosopher dies.
- **Optional Eat Count**: The simulation can also end if all philosophers have eaten a specified `number_of_times_each_philosopher_must_eat`.

## ✨ Features

- **Multi-threaded Simulation**: Each philosopher runs as an independent thread.
- **Mutex-based Synchronization**: Forks are protected by mutexes to ensure exclusive access.
- **Precise Timing**: Accurate millisecond-level timing for philosopher actions (eating, sleeping) and the death timer.
- **Status Logging**: Clear and timestamped output for each philosopher's actions (e.g., "X has taken a fork", "X is eating", "X is sleeping", "X is thinking", "X died").
- **Configurable Parameters**: Simulation behavior can be customized via command-line arguments.
- **Deadlock Avoidance**: The core of the project lies in implementing a strategy to prevent philosophers from deadlocking while waiting for forks.
- **Resource Efficiency**: Proper management of threads and mutexes.

## 🚀 Installation

1.  **Clone the repository**
    ```bash
    git clone https://github.com/Marouane0107/philo.git
    cd philo
    ```

2.  **Compile the project**
    ```bash
    make
    ```

    Common Makefile targets (please verify and update if different for your project):
    ```bash
    # Clean object files
    make clean

    # Full clean (object files and executable)
    make fclean

    # Recompile
    make re
    ```

## 🎮 Usage

### Basic Usage
The program is executed from the command line with several arguments:

```bash
./philo number_of_philosophers time_to_die time_to_eat time_to_sleep [number_of_times_each_philosopher_must_eat]
```

### Arguments:
- `number_of_philosophers`: The number of philosophers (and also the number of forks).
- `time_to_die` (in milliseconds): If a philosopher hasn't started eating for this amount of time since their last meal or the beginning of the simulation, they die.
- `time_to_eat` (in milliseconds): The time it takes for a philosopher to eat.
- `time_to_sleep` (in milliseconds): The time it takes for a philosopher to sleep.
- `[number_of_times_each_philosopher_must_eat]` (optional): If all philosophers have eaten at least this many times, the simulation stops. If not specified, the simulation continues until a philosopher dies.

### Examples
```bash
# Simulate 5 philosophers, 800ms to die, 200ms to eat, 200ms to sleep
./philo 5 800 200 200

# Simulate 4 philosophers, with an optional eat count of 7
./philo 4 410 200 200 7

# Simulate 1 philosopher (special case)
./philo 1 800 200 200
```

## 🔧 Simulation Parameters

| Argument                                      | Description                                                                                                | Unit        |
| --------------------------------------------- | ---------------------------------------------------------------------------------------------------------- | ----------- |
| `number_of_philosophers`                      | Total philosophers and forks.                                                                              | integer     |
| `time_to_die`                                 | Max time a philosopher can go without eating before dying.                                                 | milliseconds|
| `time_to_eat`                                 | Duration a philosopher spends eating.                                                                      | milliseconds|
| `time_to_sleep`                               | Duration a philosopher spends sleeping.                                                                    | milliseconds|
| `[number_of_times_each_philosopher_must_eat]` | Optional. If set, simulation ends when all philosophers have eaten this many times.                        | integer     |

## 🤝 Synchronization Strategy

The critical part of the Philosophers problem is how access to forks (mutexes) is managed to prevent deadlocks. Common strategies include:

- **Resource Hierarchy**: Assigning an order to the forks and requiring philosophers to pick them up in that order (e.g., always pick up the lower-indexed fork first, then the higher-indexed one).
- **Waiter/Arbitrator**: Introducing a central entity or mechanism that grants permission to pick up forks.
- **Limiting Concurrent Eaters**: Allowing only N-1 philosophers to attempt to pick up forks simultaneously.

The specific strategy implemented in this project should ensure that:
1.  A philosopher only eats if they have both forks.
2.  No two philosophers can hold the same fork simultaneously.
3.  Deadlocks are avoided (e.g., a circular wait where each philosopher holds one fork and waits for the other).
4.  Starvation is minimized (a philosopher isn't perpetually denied forks).

Output logs should be protected by a separate mutex to prevent garbled messages from concurrent threads.

## 📁 Project Structure

A typical project structure for `philo` might look like this (please adapt to your actual file organization):

```
philo/
├── main.c               # Main simulation logic, thread creation
├── philo.h              # Header with structures, prototypes, constants
├── init.c               # Initialization of philosophers, mutexes, data
├── actions.c            # Philosopher actions (eat, sleep, think)
├── time.c               # Time management utilities
├── utils.c              # Utility functions (e.g., parsing, error handling)
├── monitor.c            # Thread monitoring death/eat counts (if separate)
└── Makefile             # Build configuration
```
*(You can update this section with your specific file names and their roles.)*

## 🧪 Testing

Thorough testing is crucial to ensure the simulation is correct and robust.

### Test Scenarios:
- **Varying `number_of_philosophers`**:
    - `1` (special case, should not die immediately if `time_to_die` > 0)
    - `4 410 200 200` (common test case)
    - `5 800 200 200`
    - Large numbers (e.g., `200 310 100 100`)
- **Varying `time_to_die`**:
    - Small values to induce quick deaths if logic is flawed.
    - Large values to observe long-running simulations.
- **Varying `time_to_eat` and `time_to_sleep`**:
    - Test cases where `time_to_eat` is close to `time_to_die`.
    - Test cases where actions are very short or very long.
- **With and without `[number_of_times_each_philosopher_must_eat]`**:
    - `5 800 200 200 7` (simulation should end after all 5 eat 7 times)
    - Ensure it stops correctly without deaths if the eating goal is met.
- **Edge Cases**:
    - `time_to_die` being very small compared to `time_to_eat` + `time_to_sleep`.
    - All times set to 0 (behavior might be undefined by subject, but good to test).

### What to Check:
- **No Deadlocks**: The simulation should always progress or terminate correctly.
- **Correct Termination**:
    - If a philosopher dies, the simulation stops, and the death is logged.
    - If the optional eat count is met, the simulation stops, and no philosopher should die afterwards.
- **Accurate Timestamps**: Philosopher action logs should have correct and increasing timestamps.
- **No Race Conditions**: Shared data (like philosopher states or fork availability) should be consistently managed.
- **Philosopher Behavior**: Each philosopher should correctly cycle through thinking, acquiring forks, eating, releasing forks, and sleeping.
- **Output Format**: Ensure the output messages match the format specified by the project subject.

## ⚠️ Error Handling

The program must handle various error conditions:
- **Invalid Number of Arguments**: Too few or too many command-line arguments.
- **Invalid Argument Values**:
    - Non-numeric input for any parameter.
    - Negative values for times or number of philosophers.
    - `number_of_philosophers` being zero or exceeding a reasonable limit (e.g., as per `MAX_PHILO` if defined).
- **System Call Failures**:
    - Errors during thread creation (`pthread_create`).
    - Errors during mutex initialization (`pthread_mutex_init`) or destruction (`pthread_mutex_destroy`).
    - Memory allocation failures (`malloc`).

Error messages should be clear and printed to the standard error stream.

## 👨‍💻 Author

**Marouane Aouzal** (Marouane0107)
- GitHub: [@Marouane0107](https://github.com/Marouane0107)
- UM6P-1337 Coding School Student

## 📝 Notes

- This project is a fundamental part of the 42 School curriculum, emphasizing concurrency and synchronization.
- Strict adherence to the project subject's rules regarding output format, allowed functions, and behavior is required.
- Memory leaks and race conditions are strictly forbidden and will result in a failing grade.
- The focus is on creating a robust and correct simulation that respects all constraints of the Dining Philosophers problem.

---

*Developed as part of the 42 School "Philosophers" project.*
*Last Updated: May 2023
