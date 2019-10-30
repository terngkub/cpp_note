# My Note on The Art of Debugging

## Chapter 1

Main Debugger Operations
* Stepping Through the Source Code
    * Breakpoints
        set breakpoint to pause the execution
        ```
        break [breakpoint]
        ```
    * Single-stepping
        execute each line of code
        ```
        step // enter the function at function call
        next // not enter
        ```
    * Resume operation
        resume execution to the next breakpoint
        ```
        continue
        ```
    * Temporary breakpoints
        ```
        tbreak
        until
        finish
        ```
* Inspecting Variables
    ```
    print [variable]
    ```
* Issuing an "All Points Bulletin" for Changes to a Variable
    pause execution when the value of a specified variable changes
    ```
    watch [variable]
    watch [expression]

    watch z
    watch z > 28
    ```
* Moving Up and Down the Call Stack
    each function call put a frame to the stack.
    the current function is number 0.
    ```
    frame [number]
    up // go to the parent
    down
    backtrace // show entire stack
    ```

Useful commands
```
// break on condition
break [line]
condition [breakpoint] [condition]
// or
break [line] if [condition]

// list breakpoints
info break

// delete a breakpoint
clear [breakpoint]
```

Abbreviation
```
b = break
cond = condition
p = print
```