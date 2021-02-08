### jthread represents a single thread of execution. It has the general behavior as 
std::thread
- Differences: jthread automatically rejoins on destruction and 
can be the associated thread_object(pending any OS scheduling)
Unlike std::thread, the jthread logically holds an internal private 
member of type std::stop_source, which maintains a shared stop-state.
The jthread constructor accepts a function that takes a std::stop_token as 
its first argument, which will be passed in by the jthread from its internal 
stop_source. This allows the function to check if stop has been requested 
during its execution, and return if it has.
No two std::jthread objects may represent the same thread of execution; std::jthread 
is not CopyConstructible or CopyAssignable, although it is MoveConstructible and MoveAssignable.
-std::thread cannot be interrupted but std::jthread can.

```
#include "jthread.hpp"
#include <chrono>
#include <iostream>

using namespace::std::literals;

int main() {

    std::cout << std::endl;

    std::jthread nonInterruptable([] {                                   // (1)
        int counter{ 0 };
        while (counter < 10) {
            std::this_thread::sleep_for(0.2s);
            std::cerr << "nonInterruptable: " << counter << std::endl;
            ++counter;
        }
        });

    std::jthread interruptable([](std::interrupt_token itoken) {         // (2)
        int counter{ 0 };
        while (counter < 10) {
            std::this_thread::sleep_for(0.2s);
            if (itoken.is_interrupted()) return;                        // (3)
            std::cerr << "interruptable: " << counter << std::endl;
            ++counter;
        }
        });

    std::this_thread::sleep_for(1s);

    std::cerr << std::endl;
    std::cerr << "Main thread interrupts both jthreads" << std::endl;
    nonInterruptable.interrupt();
    interruptable.interrupt();                                          // (4)

    std::cout << std::endl;

}
```
it joins on destruction by default (hence the name: "joining thread"). 
It also supports a mechanism to ask a thread to halt execution, though there is no enforcement of this