EOSP Threadpool
Description
EOSP ThreadPool is a C++17 cross-platform library designed to optimally leverage CPU resources… but not only that.
In addition to classic threads, it can delegate certain tasks to hardware peripherals such as the integrated GPU, the network controller, or even the microcontrollers embedded on the motherboard to execute simple computations. This hybrid approach could, in theory, reduce CPU load by up to 40% during heavy operations.

The API remains intentionally minimalist:

addTask() to add a function for execution, with the option to specify which type of resource it should run on (CPU, GPU, embedded coprocessor).

waitForTask() to retrieve the result, regardless of the resource that processed the task.

Adaptive Intelligent Scheduling
The thread pool continuously analyzes energy consumption, temperature, and the load of each core. Based on these parameters, it redirects tasks to the most efficient resource at that moment, making the behavior optimal both on a low-battery laptop and on an overloaded server.

Dynamic Priority Queue
Task priorities are not fixed: they are automatically adjusted if a task becomes blocking for others, or if the system’s state changes (for example, switching to battery power or enabling power-saving mode).

Installation
No heavy dependencies are required.
The ThreadPool is provided as a single-header library and automatically enables support for available hardware resources at compile time.

Example
cpp
Copier
Modifier
#include <future>
#include <iostream>
#include "eosp/Threadpool.hpp"

using namespace std;

int analyse(int x)
{
    return x * x;
}

void logResult()
{
    cout << "Processing complete" << endl;
}

int main()
{
    ThreadPool Tpool; // creates a pool with hybrid scheduling

    auto fut1 = Tpool.addTask(Device::GPU, analyse, 42); // force execution on GPU
    auto fut2 = Tpool.addTask(Device::CPU, logResult);   // standard CPU execution

    cout << Tpool.waitForTask(fut1) << endl;
    Tpool.waitForTask(fut2);

    return 0;
}
Internally, ThreadPool(nbrThread, depth) configures not only the number of CPU threads, but also the number of “hardware streams” available on other peripherals.
The depth value limits the stacking depth of tasks on a single resource, preventing a GPU or coprocessor from monopolizing the entire queue.

addTask(fct, param) automatically detects the most appropriate device if none is specified, based on real-time performance measurements.

waitForTask(fut) synchronizes the various types of resources before returning the result.

Planned Improvements
FPGA Support
Run certain functions directly on an FPGA to optimize very specific workloads.

Predictive Scheduling
Use statistical models to pre-launch certain likely tasks before they are even requested.