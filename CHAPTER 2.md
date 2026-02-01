
## CONTROL FLOW

1. Sequential Control Flow (Default Flow)
	The “do things top to bottom like a good kid” mode
	Statement execution order
	Expression evaluation order
	Block scope { }
	Short-circuit evaluation (&&, ||)
	📌 Concepts
	Order of execution
	Side effects
	Undefined vs unspecified behavior
2. Decision / Selection Statements (Branching)
	2.1 if Statement
		Simple if
		if–else
		if–else if–else
		Nested if
		Multiple independent ifs
		Boolean expressions
		Truthy / falsy values
		Dangling else problem
	2.2 switch Statement
		switch expression rules
		case labels
		break behavior
		Fall-through (intentional & accidental)
		default case
		Constant expression requirement
		enum with switch
		switch vs if-else performance
		Modern [[fallthrough]] attribute
	2.3 Conditional (Ternary) Operator ?:
		Syntax and associativity
		Nested ternary
		Ternary vs if-else
		L-value ternary (advanced)
		Readability pitfalls
3.  Iteration / Looping Constructs
	3.1 for Loop
		Initialization,Condition,Increment/decrement
		Infinite for(;;)
		Nested loops
		Multiple variables in loop
		Loop unrolling (concept)
		Time complexity implications
	3.2 while Loop
		Entry-controlled loop
		Infinite while(true)
		Sentinel-controlled loops
		Input-based loops
		Difference from for
	3.3 do-while Loop
		Exit-controlled loop
		Guaranteed single execution
		Menu-driven programs
		Validation loops
	3.4 Range-Based for Loop (Modern C++)
		Iterating arrays
		Iterating STL containers
		auto usage
		Reference vs value (auto&)
		Const iteration
		Hidden iterator mechanism
4.  Loop Control Statements (Flow Modifiers)
	4.1 break
		Loop termination
		switch exit
		Nested loop behavior
		Label-less breaking
	4.2 continue
		Skipping iteration
		Effect in for, while, do-while
		Common logic bugs
	4.3 goto (Yes, it exists. No, don’t.)
		Labels
		Jumping forward/backward
		Why it’s dangerous
		Rare valid use cases (error handling in C-style code)
5. Function Control Flow
	5.1 return Statement
		Returning values
		Returning void
		Multiple return points
		Early exit patterns
		Returning references
		Undefined behavior pitfalls
	5.2 Function Calls
		Call stack mechanics
		Parameter passing
		By value
		By reference
		By pointer
		Recursion as control flow
		Tail recursion concept
6. Recursion (Advanced Control Flow)
	Base case
	Recursive case
	Stack frames
	Infinite recursion
	Stack overflow
	Recursion vs iteration
	Backtracking control flow
	Memoization interaction
7. exception-Based Control Flow
	7.1 try
		Protected block
		Scope rules
	7.2 catch
		Catching by value vs reference
		Multiple catch blocks
		Catch-all (catch(...))
		Exception matching order
	7.3 throw
		Throwing objects
		Stack unwinding
		Rethrowing exceptions
	7.4 Exception Safety
		Strong guarantee
		Basic guarantee
		No-throw guarantee
8.	Jump

	8.1  Jump & Transfer Control
			Jumping across scopes (danger zone)
			Stack unwinding effects
			Control transfer vs execution flow
			
	8.2 Concurrency-Affected Control Flow (Advanced)
		Race conditions
		Control flow in multithreading
		Atomic operations
		Mutex lock/unlock flow
		Deadlock scenarios
		Condition variables
9. Compile-Time Control Flow (Meta)
	11.1 Preprocessor Directives
	#if
	#ifdef
	#ifndef
	#elif
	#else
	#endif
	📌 Code that decides whether code exists
	11.2 constexpr & if constexpr
	Compile-time branching
	Template-based control flow
	Zero runtime cost decisions
	
	1️⃣3️⃣ Undefined & Unspecified Control Flow (⚠️ Important)
	Order of evaluation
	Multiple side effects
	Sequence points
	UB-caused flow chaos
	If control flow feels haunted — it’s probably UB.

## SEQUENTIAL CONTROL FLOW

Theoretical Foundation

Control flow describes the order in which statements execute in a program. The simplest and most fundamental model is **sequential execution**: statements execute top-to-bottom, one after another, exactly as written. This is the default behavior unless explicitly modified by control transfer statements.

**Key Concepts:**

**Order of Execution**: The program counter (instruction pointer) progresses linearly through statements. Each statement completes before the next begins, establishing a strict temporal ordering.

**Expression Evaluation Order**: Within a single expression, the order matters. Consider `a = b++ + ++c;`—the order of side effects (like incrementing) affects the final value. C++ has specific rules about when side effects occur relative to other operations.

**Block Scope `{}`**: A block creates a new scope. Variables declared inside a block are local to that block and destroyed when the block exits. This affects the lifetime of objects and memory management.

**Short-Circuit Evaluation** (`&&`, `||`): The logical AND and OR operators don't always evaluate both operands:

- `a && b`: If `a` is false, `b` is never evaluated (short-circuits)
- `a || b`: If `a` is true, `b` is never evaluated (short-circuits)

This is a control flow optimization that prevents unnecessary computation and can prevent undefined behavior.


| **Feature**             | **Unspecified Behavior**            | **Undefined Behavior**                  |
| ----------------------- | ----------------------------------- | --------------------------------------- |
| **Status of Program**   | Well-formed (Legal)                 | Erroneous (Illegal)                     |
| **Range of Outcomes**   | Limited to a set of valid choices   | Infinite (crashes, silent errors, etc.) |
| **Compiler Assumption** | Must handle one of the valid cases  | Can assume it never happens             |
| **Portability**         | Risky (behavior varies by platform) | Non-existent (code is broken)           |
| **Documentation**       | Not required to be documented       | Not required to be documented           |

**Unspecified Behavior** =  The program behavior is correct per the standard, but _which_ correct behavior occurs is not determined. Different compilers or runs might produce different results, but all are valid.

```c++
void print(int a, int b) {
    std::cout << a << ", " << b;
}

int main() {
    int i = 0;
    print(++i, ++i); // Unspecified Behavior!
}
// now compiler will give you the answer 1,2 or 2,1 because it is unable to comprehend which one to put first.
```

Undefined Behavior (UB) =  The standard makes no guarantee about what happens. The program might crash, produce garbage output, or appear to work. Anything is possible.

```c++
int x = INT_MAX;
x = x + 1; // Undefined Behavior!
```


## DECISION / SELECTION STATEMENTS


2.1  The if Statement

**Theory**: The `if` statement allows conditional execution. The condition is evaluated to a boolean (true/false). If true, the dependent statement (or block) executes. If false, it's skipped.

```c++
// Syntax
if (condition) {
    // executes if condition is true
}

if (condition) {
    // executes if condition is true
} else {
    // executes if condition is false
}

if (condition1) {
    // executes if condition1 is true
} else if (condition2) {
    // executes if condition1 is false AND condition2 is true
} else if (condition3) {
    // executes if condition1 and condition2 are false AND condition3 is true
} else {
    // executes if all above conditions are false
}

```

**Truthy/Falsy Values**: In C++, any integer or pointer value can be used where a boolean is expected:

- `0` is falsy
- Non-zero values are truthy
- Pointers: `nullptr` is falsy, non-null pointers are truthy

**Dangling else Problem**: When if statements are nested without braces, the `else` associates with the nearest unmatched `if`:

```c++
if (x > 0)
    if (y > 0)
        cout << "Both positive\n";
else
    cout << "x is not positive\n";  // This else pairs with inner if, not outer!

```


**What the compiler actually does:** In C++, the rule is that an `else` is always associated with the **nearest preceding** `if` that doesn't already have an `else`.

2.2 The switch Statement

**Theory**: `switch` provides an efficient alternative to multiple `if-else if` chains when checking a single value against many constants. The expression is evaluated once, then the value is compared to each case label.

**Control Flow**:

1. Evaluate the switch expression
2. Compare against each case label (in order)
3. Jump to the matching case
4. Execute statements until a `break` is encountered
5. If no match, jump to `default` (if present)

**Fall-Through**: Unlike some languages, C++ `switch` statements continue executing through subsequent cases unless stopped by `break`. This can be intentional or accidental.

**Requirements**:

- Case labels must be **constant expressions** (compile-time constants)
- `switch` expression must be integral or enum type
- Only one `default` case allowed
- Case labels must be unique

```c++
switch (expression)
{
    case constant1:
        // Code to execute if expression == constant1
        break; 
    case constant2:
        // Code to execute if expression == constant2
        break;
    default:
        // Code to execute if no match is found
}
```


Key Components

The Expression

- In C++, the expression inside `switch()` must evaluate to an **integral type** (e.g., `int`, `char`, `short`, `long`) or an **enumeration**.
- **You cannot** use strings, floats, or doubles in a standard C++ switch statement.

### The `break` Keyword

- The `break` statement is crucial. It tells the computer to "jump out" of the switch block once a case is finished.
- **Fall-through:** If you omit the `break`, the computer will continue executing the code in the _next_ case, even if that case doesn't match the expression. (Sometimes this is used intentionally, but usually, it's a bug).

### The `default` Case

- The `default` block is optional. It acts like the final `else` in an if-else chain. It runs only if none of the `case` constants match the expression.


Common Pitfalls
1. Variable Initialization
You cannot initialize a variable inside a case if that variable is visible to other cases, unless you wrap the case in curly braces `{ }`.

```c++
switch (x)
{
    case 1:
        int val = 5; // Error! val is visible to Case 2 but might not be initialized
        break;
    case 2:
        // ...
}
```

**Fix:** Use `{ int val = 5; ... }` to limit the scope.

The primary use of **fallthrough** is to avoid duplicating code when multiple conditions share some or all of the same logic.it is basically when you forget the break statement and the compiler runs the whole block of code with a warning, to avoid that warning we write this fallthrough in it

```c++
switch (cmd)
{
    case 'a':
    case 'A': // Multiple cases can trigger the same code
        do_action();
        break;
    case 'b':
        setup();
        [[fallthrough]]; // Explicitly telling the compiler we want to run 'c' too 
    case 'c':
        finish();
        break;
}
```


```c++
#include <iostream>
using namespace std;

int main()
	{
    // EXAMPLE 1: Basic switch
    int day = 3;
    
    switch (day) {
        case 1:
            cout << "Monday\n";
            break;
        case 2:
            cout << "Tuesday\n";
            break;
        case 3:
            cout << "Wednesday\n";
            break;
        default:
            cout << "Unknown day\n";
    }
    // Output: Wednesday
    
    // EXAMPLE 2: Intentional fall-through
    int grade = 2;
    switch (grade)
    {
        case 1:
        case 2:
            cout << "Excellent or Very Good\n";
            break;
        case 3:
        case 4:
            cout << "Good or Satisfactory\n";
            break;
        default:
            cout << "Poor\n";
    }
    // Output: Excellent or Very Good (cases 1 and 2 both execute same code)
    
    // EXAMPLE 3: Accidental fall-through (demonstrates bug)
    int option = 2;
    switch (option) {
        case 1:
            cout << "Option 1 selected\n";
            // Missing break!
        case 2:
            cout << "Option 2 selected\n";
            // Missing break!
        case 3:
            cout << "Option 3 selected\n";
            break;
    }
    // Output: Option 2 selected, then Option 3 selected
    // This is usually a bug!
    
    // EXAMPLE 4: Modern [[fallthrough]] attribute (C++17)
    // Explicitly marks intentional fall-through
    switch (grade) {
        case 1:
            cout << "First\n";
            [[fallthrough]];  // Compiler warning about unintended fallthrough suppressed
        case 2:
            cout << "First or Second\n";
            break;
        default:
            cout << "Other\n";
    }
    
    // EXAMPLE 5: Using enum with switch
    enum Color { RED = 0, GREEN = 1, BLUE = 2 };
    Color color = GREEN;
    
    switch (color) {
        case RED:
            cout << "Red selected\n";
            break;
        case GREEN:
            cout << "Green selected\n";
            break;
        case BLUE:
            cout << "Blue selected\n";
            break;
    }
    // Output: Green selected
    
    return 0;
}

```

`switch` can be more efficient than `if-else if` chains because:

- The expression is evaluated only once (vs evaluating multiple conditions)
- Compiler can use jump tables for dense case values
- `if-else if` must evaluate each condition sequentially

| **Feature**     | **switch Statement**                                   | **if-else Chain**                                      |
| --------------- | ------------------------------------------------------ | ------------------------------------------------------ |
| **Logic**       | Tests a single variable against constants.             | Can test complex conditions and ranges.                |
| **Performance** | Usually faster for many cases (uses a **Jump Table**). | Slower for many cases (tests each condition linearly). |
| **Readability** | Clean and organized for many discrete values.          | Can become messy ("Arrow Code").                       |


2.3 Conditional (Ternary) Operator `?:`

**Theory**: The conditional operator `condition ? value_if_true : value_if_false` is a compact way to choose between two values based on a condition. It's an operator (not a statement), so it returns a value.

The Syntax = The operator uses a question mark (`?`) and a colon (`:`).

```c++
condition ? expression_if_true : expression_if_false;
```

working of this code :
1. **Condition:** A logic check (like `x > 5`).
2. **?**: "Is this true?"
3. **Expression 1:** The result if the answer is **Yes**.
4. **:** "Otherwise..."
5. **Expression 2:** The result if the answer is **No**.


The main advantage is **conciseness**. It allows you to assign a value to a variable based on a condition in a single line.

```c++
int a = 10, b = 20;
int max = (a > b) ? a : b; 
// Output: 20
```

Unlike an `if` statement, which is a "block of code," the ternary operator is an **expression**. This means it evaluates to a specific value that you can print or save to a variable.

```c++
#include <iostream>
using namespace std;

int main() {
    // EXAMPLE 1: Simple ternary
    int age = 20;
    string status = (age >= 18) ? "Adult" : "Minor";
    cout << status << "\n";  // Output: Adult
    
    // EXAMPLE 2: Nested ternary
    int score = 75;
    string grade = (score >= 90) ? "A" :
                   (score >= 80) ? "B" :
                   (score >= 70) ? "C" :
                   "F";
    cout << "Grade: " << grade << "\n";  // Output: Grade: C
    
    // EXAMPLE 3: Right-associativity
    int x = 5;
    int result = x > 0 ? 1 : x < 0 ? -1 : 0;
    // Parsed as: x > 0 ? 1 : (x < 0 ? -1 : 0)
    cout << "Result: " << result << "\n";  // Output: 1
    
    // EXAMPLE 4: Ternary vs if-else
    // Ternary: evaluates to a value, can be assigned
    int max_value = (5 > 3) ? 5 : 3;
    
    // if-else: executes statements, doesn't return a value
    int max_value2;
    if (5 > 3) {
        max_value2 = 5;
    } else {
        max_value2 = 3;
    }
    
    // EXAMPLE 5: Side effects with ternary
    int a = 1, b = 2;
    // Only one side executes
    int result2 = (a > b) ? (a++) : (b++);
    cout << "a = " << a << ", b = " << b << "\n";
    // Output: a = 1, b = 3 (only b++ executed)
    
    return 0;
}

```

Advance: 
- **lvalue (Left-value):** A thing that has a name and a specific spot in your computer's memory (like a variable). You can put it on the **left** side of an `=` sign.
- **rvalue (Right-value):** A temporary value or "data" (like the number `20`). You usually put it on the **right** side of an `=` sign.
In many languages, a ternary operator (`? :`) only returns **data**. But in C++, if both options are variables (lvalues), the operator returns the **variable itself**, not just the number inside it.

```c++
// In C++, the result of ?: can be an lvalue if both branches are lvalues
int x = 5, y = 10;
(x > y ? x : y) = 20;  // Assigns to the larger variable
cout << "x = " << x << ", y = " << y << "\n";  // y = 20

```

Breaking Down Your Code

Let's trace exactly what the computer does with `(x > y ? x : y) = 20;`

Step 1: The Question

The computer looks at `(x > y)`.
- Since `x = 5` and `y = 10`, the answer is **False**.
Step 2: Picking the Variable

Because the answer is False, the ternary operator picks the second option: **`y`**.

Step 3: The Secret Magic

Instead of just grabbing the number `10` from `y`, the ternary operator creates a "link" or a **reference** to the variable `y` itself. It essentially says: _"Okay, for this line of code, this whole bracket is just another name for `y`."_

Step 4: The Assignment

Now the code looks like this to the computer: `y = 20;`
Why is this cool?

Usually, you would have to write this: 
```c++
if (x > y) {
    x = 20;
} else {
    y = 20;
}
```
but here we can write ternary expression instead
## ITERATION / LOOPING CONSTRUCTS

3. ITERATION / LOOPING CONSTRUCTS

3.1 The for Loop 

The `for` loop is the most explicit loop construct. It combines three operations: initialization, condition checking, and increment/decrement.

```c++
for (initialization; condition; increment)
{
    // loop body
}

```

**Execution Order**:

1. Initialization executes once before the loop starts
2. Condition is evaluated; if false, exit loop
3. Loop body executes
4. Increment/decrement executes
5. Go to step 2

**Key Concepts**:

- **Infinite Loop**: `for(;;)` creates an infinite loop (condition always true)
- **Loop Variable Scope**: Variables declared in initialization are scoped to the loop
- **Multiple Variables**: Can initialize and increment multiple variables: `for (int i = 0, j = 10; i < 5; i++, j--)`
- **Loop Unrolling**: Manual technique where you execute multiple iterations in a single loop iteration to reduce loop overhead
- **Time Complexity**: A simple `for` loop with n iterations is O(n)
```c++
#include <iostream>
using namespace std;

int main()
{
    // EXAMPLE 1: Basic for loop
    cout << "Basic for loop:\n";
    for (int i = 0; i < 5; i++)
    {
        cout << "i = " << i << "\n";
    }
    // Output: i = 0, 1, 2, 3, 4
    
    // EXAMPLE 2: Loop variable scope
    cout << "\nLoop variable scope:\n";
    for (int i = 0; i < 3; i++) 
    {
        cout << "i = " << i << "\n";
    }
    // int x = i;  // ERROR: i doesn't exist outside loop
    
    // EXAMPLE 3: Multiple variables
    cout << "\nMultiple variables:\n";
    for (int i = 0, j = 10; i < 5; i++, j--) 
    {
        cout << "i = " << i << ", j = " << j << "\n";
    }
    // Output: i=0,j=10; i=1,j=9; i=2,j=8; i=3,j=7; i=4,j=6
    
    // EXAMPLE 4: Infinite loop (with break to exit)
    cout << "\nInfinite loop:\n";
    int count = 0;
    for (;;) 
    {
        cout << count << " ";
        count++;
        if (count == 5) break;
    }
    cout << "\n";
    
    // EXAMPLE 5: Nested loops
    cout << "\nNested loops (3x3 grid):\n";
    for (int i = 0; i < 3; i++) 
    {
        for (int j = 0; j < 3; j++) 
        {
            cout << "(" << i << "," << j << ") ";
        }
        cout << "\n";
    }
    
    // EXAMPLE 6: Loop unrolling (manually executing 2 iterations per loop)
    cout << "\nLoop unrolling:\n";
    // Normal loop
    cout << "Normal: ";
    for (int i = 0; i < 6; i++) 
    {
        cout << i << " ";
    }
    cout << "\n";
    
    // Unrolled loop (2 iterations per loop iteration)
    cout << "Unrolled: ";
    for (int i = 0; i < 6; i += 2) 
    {
        cout << i << " " << (i + 1) << " ";
    }
    cout << "\n";
    
    // EXAMPLE 7: Counting down
    cout << "\nCounting down:\n";
    for (int i = 5; i > 0; i--) 
    {
        cout << i << " ";
    }
    cout << "\n";
    
    return 0;
}

```


3.2 **The while Loop

**Theory**: The `while` loop is an entry-controlled loop. It checks the condition before executing the loop body. If the condition is false initially, the body never executes.


```c++
while (condition) 
{
    // loop body
}

```

**Use Cases**:

- Reading input until a sentinel value is encountered
- Processing data with unknown length
- Conditions that depend on state changes within the loop

```c++
#include <iostream>
using namespace std;

int main() 
{
    // EXAMPLE 1: Basic while loop
    cout << "Basic while loop:\n";
    int i = 0;
    while (i < 5) 
    {
        cout << "i = " << i << "\n";
        i++;
    }
    
    // EXAMPLE 2: Sentinel-controlled loop
    cout << "\nSentinel-controlled loop (enter -1 to stop):\n";
    int num;
    int sum = 0;
    while (cin >> num && num != -1) 
    {
        sum += num;
    }
    cout << "Sum: " << sum << "\n";
    
    // EXAMPLE 3: Infinite while loop (with break)
    cout << "\nInfinite while loop:\n";
    int count = 0;
    while (true) 
    {
        cout << count << " ";
        count++;
        if (count == 5) break;
    }
    cout << "\n";
    
    // EXAMPLE 4: Input-based loop
    cout << "\nInput-based loop (enter 0 to quit):\n";
    int choice;
    while (true) 
    {
        cout << "Enter choice (0 to quit): ";
        cin >> choice;
        if (choice == 0) break;
        cout << "You entered: " << choice << "\n";
    }
    
    return 0;
}

```

3.3 The do-while Loop

**Theory**: The `do-while` loop is an exit-controlled loop. The body executes first, then the condition is checked. This guarantees at least one execution of the body.

```c++
do
{
	//loop body
} while(condition); // Key Characteristic: Even if the condition is false initially, the body executes once.
```

```c++
#include <iostream>
using namespace std;

int main() {
    // EXAMPLE 1: Basic do-while
    cout << "Basic do-while:\n";
    int i = 0;
    do {
        cout << "i = " << i << "\n";
        i++;
    } while (i < 5);
    
    // EXAMPLE 2: Menu-driven program
    cout << "\nMenu-driven program:\n";
    int choice;
    do {
        cout << "\n1. Add\n2. Subtract\n3. Exit\n";
        cout << "Enter choice: ";
        cin >> choice;
        
        switch (choice) {
            case 1:
                cout << "Adding...\n";
                break;
            case 2:
                cout << "Subtracting...\n";
                break;
            case 3:
                cout << "Exiting...\n";
                break;
            default:
                cout << "Invalid choice\n";
        }
    } while (choice != 3);
    
    // EXAMPLE 3: Input validation loop
    cout << "\nInput validation (enter 1-5):\n";
    int num;
    do {
        cout << "Enter number (1-5): ";
        cin >> num;
        if (num < 1 || num > 5) {
            cout << "Invalid! Try again.\n";
        }
    } while (num < 1 || num > 5);
    cout << "You entered: " << num << "\n";
    
    // EXAMPLE 4: Guaranteed execution even if condition is false
    cout << "\nGuaranteed execution:\n";
    i = 10;
    do {
        cout << "This executes at least once\n";
    } while (i < 5);  // Condition is false, but body executed once
    
    return 0;
}

```

3.4 Range-Based for Loop (C++11+)

**Theory**: The range-based for loop simplifies iteration over containers. It automatically handles begin/end iteration without manual management.

**Mechanisms**:

- Works with arrays, vectors, and any container with `begin()` and `end()`
- `auto` type deduction simplifies syntax
- `auto&` creates a reference (avoid copying large objects)
- `const auto&` creates a const reference (read-only)

```c++
for (type variable : container) 
{
    // loop body
}

```


range based loop is easy for the writing the code like here is an example of how you can have two codes for same purpose and the range based is better 

Unlike a traditional `for` loop, you don't need a counter (`i`), you don't need to check the size, and you don't need to manually increment anything. It essentially says: **"For every item in this collection, do this."**


```c++
// traditional way of writing a loop
int scores[] = {85, 92, 78, 90};
for (int i = 0; i < 4; i++) 
{
    std::cout << scores[i] << " ";
}
// range based loop
int scores[] = {85, 92, 78, 90};
for (int s : scores) 
{
    std::cout << s << " ";
}
// Output: 85 92 78 90
```

 How It Works

The computer handles all the heavy lifting for you:

1. It looks at the `scores` array.
2. It automatically finds the start and the end.
3. In the first turn, it copies the first value into `s` and runs the code.
4. In the next turn, it moves to the second value, and so on.
5. It stops automatically when it runs out of items.

Important Feature: Using References (`&`)

By default, the range-based loop **copies** each item into your variable. If you have a large list of data, copying can be slow. Or, if you want to **change** the values in the array, a copy won't work.

To fix this, we use a **reference** (`&`):

```c++
int prices[] = {10, 20, 30};

// Use & to access the actual items in the array, not copies
for (int &p : prices) 
{
    p = p + 5; // Adds 5 to every price in the original array
}
```

|**Feature**|**Traditional for**|**Range-based for**|
|---|---|---|
|**Control**|High (can skip items, go backward)|Low (goes through every item once)|
|**Safety**|Medium (easy to mess up the index)|High (impossible to go out of bounds)|
|**Syntax**|Verbose|Simple and clean|

here are some examples of the different containers:
```c++
#include <iostream>
#include <vector>
using namespace std;

int main() {
    // EXAMPLE 1: Array iteration
    cout << "Array iteration:\n";
    int arr[] = {10, 20, 30, 40, 50};
    for (int x : arr) 
    {
        cout << x << " ";
    }
    cout << "\n";
    
    // EXAMPLE 2: Vector iteration
    cout << "\nVector iteration:\n";
    vector<int> vec = {1, 2, 3, 4, 5};
    for (int x : vec) 
    {
        cout << x << " ";
    }
    cout << "\n";
    
    // EXAMPLE 3: auto type deduction
    cout << "\nauto type deduction:\n";
    for (auto x : vec) 
    {  // auto deduces int, float or whatever it is.
        cout << x << " ";
    }
    cout << "\n";
    
    // EXAMPLE 4: Reference to avoid copying
    cout << "\nReference iteration (modify container):\n";
    for (auto& x : vec) 
    {  // Reference allows modification
        x *= 2;
    }
    for (int x : vec) 
    {
        cout << x << " ";
    }
    cout << "\n";
    
    // EXAMPLE 5: Const reference (read-only)
    cout << "\nConst reference iteration:\n";
    for (const auto& x : vec) 
    {
        cout << x << " ";
        // x *= 2;  // ERROR: const means read-only
    }
    cout << "\n";
    
    // EXAMPLE 6: String iteration
    cout << "\nString character iteration:\n";
    string str = "Hello";
    for (char c : str) 
    {
        cout << c << " ";
    }
    cout << "\n";
    
    return 0;
}

```

CODE EXPLANATION HERE:

Example 4: Reference to Avoid Copying (`auto& x`)

**The Basics:** This is the most important one to understand for performance.

- **Standard Loop (`auto x`):** The computer takes the value from the vector, **makes a photocopy**, and hands the copy to `x`. If you change `x`, you only changed the photocopy. The original vector stays the same.
    
- **Reference Loop (`auto& x`):** The `&` symbol means "Address/Reference." Instead of making a copy, `x` becomes a **direct nickname** for the actual item inside the vector.
    
- **Why use it?**
    
    1. **To Modify:** Like in your code (`x *= 2`), if you want to double the numbers _inside_ the vector, you must use a reference.
        
    2. **Speed:** If your vector contains something huge (like a high-resolution image or a long paragraph), making a "photocopy" every time the loop runs is very slow. A reference is nearly instant.
        
- **What to do with it:** Use it whenever you want to **edit** the contents of your list.

Example 5: Const Reference (`const auto& x`)

**The Basics:** This is the "Professional's Choice." It combines the speed of Example 4 with a safety lock.

- **Reference (`&`):** Fast, but allows you to change the data.
    
- **Const (`const`):** Puts a "Read-Only" lock on the data.
    
- **Why use it?**
    
    - **Safety:** Sometimes you have a huge piece of data (so you want a reference for speed), but you **don't** want to accidentally change it. If you try to do `x = 10` inside a `const` loop, the compiler will stop you with an error.
        
    - **Intent:** It tells other programmers: _"I am looking at this data for speed, but I promise not to touch or break anything."_
        
- **What to do with it:** This should be your **default** loop for large data sets unless you specifically need to change the values.



|**Code Style**|**Performance**|**Can modify original?**|**Rule of Thumb**|
|---|---|---|---|
|`auto x`|Slower (Copies)|**No**|Use for small types like `int` or `char`.|
|`auto& x`|Fastest (Direct)|**Yes**|Use when you **need** to change the values.|
|`const auto& x`|Fastest (Direct)|**No**|Use for large data when you **only need to read**.|

**Iterator Mechanism**: Internally, range-based for is equivalent to:

```C++
for (auto it = container.begin(); it != container.end(); ++it) 
{
    // body using *it
}

```

A Concrete Example
If you have a vector `vec = {10, 20, 30}`, here is how the iterator moves:
```C++
vector<int> vec = {10, 20, 30};

for (auto it = vec.begin(); it != vec.end(); ++it) 
{
    cout << *it << " "; 
}
```

|**Step**|**it is pointing to...**|***it (the value)**|**Is it != end()?**|
|---|---|---|---|
|**1**|The first element|10|Yes|
|**2**|The second element|20|Yes|
|**3**|The third element|30|Yes|
|**4**|**The empty space after 30**|(None)|**No** (Loop stops)|
Why use this instead of a Range-based loop?

You might wonder: _"Why write this long version if the range-based loop is shorter?"_ There are three main reasons:

1. **Deleting items:** You cannot safely delete an item from a vector while using a range-based loop. You **need** an iterator to tell the vector exactly which spot to erase.
    
    - _Example:_ `vec.erase(it);`
        
2. **Partial Loops:** If you only want to loop through the _second half_ of a container, you can start at `vec.begin() + 5`. Range-based loops always start at the beginning.
    
3. **Direction:** You can use "reverse iterators" (`rbegin()` and `rend()`) to walk through a container **backwards** very easily.

## LOOP CONTROL STATEMENTS

4.1 The break Statement

**Theory**: `break` terminates the innermost enclosing loop or switch statement. Control jumps to the statement immediately after the loop/switch.

**Key Points**:

- Only affects the innermost loop (nested loops: `break` exits inner loop only)
    
- Works in `for`, `while`, `do-while`, and `switch`
    
- No label-based breaking in C++ (unlike Java or Python)

```C++
#include <iostream>
using namespace std;

int main() {
    // EXAMPLE 1: Break in for loop
    cout << "Break in for loop:\n";
    for (int i = 0; i < 10; i++) {
        if (i == 5) break;
        cout << i << " ";
    }
    cout << "\n";  // Output: 0 1 2 3 4
    
    // EXAMPLE 2: Break in while loop
    cout << "\nBreak in while loop:\n";
    int x = 0;
    while (true) {
        cout << x << " ";
        x++;
        if (x == 5) break;
    }
    cout << "\n";
    
    // EXAMPLE 3: Break in switch
    cout << "\nBreak in switch:\n";
    int day = 2;
    switch (day) {
        case 1:
            cout << "Monday ";
            break;
        case 2:
            cout << "Tuesday ";
            break;
        default:
            cout << "Other ";
    }
    cout << "\n";
    
    // EXAMPLE 4: Nested loops - break only exits inner loop
    cout << "\nNested loops:\n";
    for (int i = 0; i < 3; i++) {
        for (int j = 0; j < 3; j++) {
            if (j == 1) break;  // Only breaks inner loop
            cout << "(" << i << "," << j << ") ";
        }
        cout << "| ";
    }
    cout << "\n";
    // Output: (0,0) | (1,0) | (2,0) |
    
    // EXAMPLE 5: Using break in search loops
    cout << "\nSearching for value:\n";
    int arr[] = {10, 20, 30, 40, 50};
    int target = 30;
    int index = -1;
    for (int i = 0; i < 5; i++) {
        if (arr[i] == target) {
            index = i;
            break;  // Found, exit immediately
        }
    }
    cout << "Found at index: " << index << "\n";
    
    return 0;
}

```

4.2 The continue Statement

**Theory**: `continue` skips the rest of the current iteration and jumps to the next iteration of the loop. The condition is re-evaluated.

**Behavior**:

- In `for` loops: jumps to the increment expression, then condition check
    
- In `while`/`do-while`: jumps to condition check
    
- Only valid inside loops (invalid in switch unless inside a loop)
```C++
#include <iostream>
using namespace std;

int main() {
    // EXAMPLE 1: Skip even numbers
    cout << "Skip even numbers:\n";
    for (int i = 0; i < 10; i++) {
        if (i % 2 == 0) continue;  // Skip even
        cout << i << " ";
    }
    cout << "\n";  // Output: 1 3 5 7 9
    
    // EXAMPLE 2: Continue in while loop
    cout << "\nContinue in while loop:\n";
    int x = 0;
    while (x < 5) {
        x++;
        if (x == 3) continue;  // Skip printing 3
        cout << x << " ";
    }
    cout << "\n";  // Output: 1 2 4 5
    
    // EXAMPLE 3: Continue in do-while
    cout << "\nContinue in do-while:\n";
    int y = 0;
    do {
        y++;
        if (y == 2) continue;
        cout << y << " ";
    } while (y < 5);
    cout << "\n";
    
    // EXAMPLE 4: Processing with continue
    cout << "\nProcessing valid data:\n";
    int data[] = {10, -5, 20, -10, 30};
    int sum = 0;
    for (int val : data) {
        if (val < 0) continue;  // Skip negative values
        sum += val;
        cout << "Added " << val << " ";
    }
    cout << "\nSum: " << sum << "\n";  // Output: 10 + 20 + 30 = 60
    
    // EXAMPLE 5: Common pitfall
    cout << "\nCommon pitfall with continue:\n";
    for (int i = 0; i < 5; i++) {
        if (i == 2) {
            cout << "Skipped " << i << " ";
            continue;  // This skips i++? NO - increment still happens
        }
        cout << i << " ";
    }
    cout << "\n";  // Output: 0 1 Skipped 2 3 4
    // The increment happens even with continue!
    
    return 0;
}

```

4.3 The goto Statement

**Theory**: `goto` transfers control unconditionally to a labeled statement. While powerful, it's generally considered harmful because it can create complex, hard-to-follow code paths.

```C++
goto label_name;

// ... code ...

label_name:
    // code executes here after goto

```

**Dangers**:

- Creates "spaghetti code" with hard-to-trace execution paths
    
- Violates structured programming principles
    
- Can cause scope violations (jumping over initialization)
    
- Makes code maintenance difficult
    

**Rare Valid Use Cases**:

- Breaking out of nested loops (though `break` with a label would be better)
    
- Error handling in C-style code (before exceptions)
    
- State machines with many states
```C++
int i = 0;
start_loop: // This is a label
    i++;
    cout << i << " ";
    if (i < 3) goto start_loop; // Teleport back to the label
    
#include <iostream>
using namespace std;

int main() 
{
    // EXAMPLE 1: Simple goto
    cout << "Simple goto:\n";
    int x = 0;
    loop:
    cout << x << " ";
    x++;
    if (x < 5) goto loop;
    cout << "\n";  // Output: 0 1 2 3 4
    
    // EXAMPLE 2: Breaking nested loops (not recommended)
    cout << "\nBreaking nested loops with goto:\n";
    for (int i = 0; i < 3; i++) {
        for (int j = 0; j < 3; j++) {
            if (i == 1 && j == 1) goto exit_loops;
            cout << "(" << i << "," << j << ") ";
        }
    }
    exit_loops:
    cout << "\nExited loops\n";
    
    // EXAMPLE 3: Simple error handling (old C style)
    cout << "\nError handling with goto:\n";
    FILE* file = nullptr;
    
    file = fopen("test.txt", "r");
    if (!file) {
        cout << "Error opening file\n";
        goto cleanup;
    }
    
    // ... use file ...
    
    cleanup:
    if (file) {
        fclose(file);
        cout << "File closed\n";
    }
    
    // MODERN approach: use try-catch or RAII instead!
    
    return 0;
}

```

|**Statement**|**What it does**|**Where the program goes next**|
|---|---|---|
|**`break`**|Stops the loop.|To the code **after** the loop block.|
|**`continue`**|Skips the current turn.|To the **top** of the loop for the next turn.|
|**`goto`**|Jumps to a label.|To the specific **labeled line**.|


## FUNCTION CONTROL FLOW
**Theory**: Functions are fundamental control flow mechanisms. They allow code reuse, modularity, and parameter abstraction.

Function Call Mechanics

**Call Stack**: When a function is called, a new stack frame is created containing:

- Return address (where to resume after function completes)
    
- Local variables
    
- Function parameters
    
- Saved registers
    

When the function returns, the frame is destroyed and control returns to the call site.
The return Statement

**Theory**: `return` terminates function execution and optionally returns a value to the caller.

```c++
// Return from void function (no value)
return;

// Return value from non-void function
return value;

// Return reference (dangerous if reference outlives function)
return reference_to_variable;

```

**Key Points**:

- Void functions can use `return;` to exit early
- Non-void functions must return a value of the appropriate type
- Control immediately returns to caller (no code after return executes in that call)
- Returning a reference to a local variable is undefined behavior (reference expires)

```c++
#include <iostream>
using namespace std;

// EXAMPLE 1: Simple return
int add(int a, int b) 
{
    return a + b;
}

// EXAMPLE 2: Early return in void function
void printPositive(int x) 
{
    if (x <= 0) {
        cout << "Not positive\n";
        return;  // Early exit
    }
    cout << "Positive: " << x << "\n";
}

// EXAMPLE 3: Multiple return statements
int absolute(int x) 
{
    if (x >= 0) 
    {
        return x;  // Return positive value
    }
    return -x;  // Return negative value
}

// EXAMPLE 4: Returning by reference
int global_x = 42;

int& getGlobalReference() 
{
    return global_x;  // Safe: returns reference to global
}

// DANGEROUS - DO NOT DO THIS
int& getDangerousReference() 
{
    int local = 10;
    return local;  // UNDEFINED BEHAVIOR: local expires after return!
}

int main() 
{
    cout << "add(3, 5) = " << add(3, 5) << "\n";
    
    printPositive(5);
    printPositive(-3);
    
    cout << "absolute(-7) = " << absolute(-7) << "\n";
    
    int& ref = getGlobalReference();
    cout << "Reference to global: " << ref << "\n";
    
    return 0;
}

```

Parameter Passing Modes

**By Value**: Function receives a copy of the argument. Changes inside function don't affect original.

**By Reference**: Function receives an alias to the original variable. Changes inside function affect original.

**By Pointer**: Function receives the address of the variable. Changes via pointer affect original.

```c++
#include <iostream>
using namespace std;

// EXAMPLE 1: Pass by value
void incrementByValue(int x) 
{
    x++;
}

// EXAMPLE 2: Pass by reference
void incrementByReference(int& x) 
{
    x++;
}

// EXAMPLE 3: Pass by pointer
void incrementByPointer(int* x) 
{
    (*x)++;
}

// EXAMPLE 4: Const reference (read-only, efficient)
void printByConstRef(const int& x) 
{
    cout << x << "\n";
    // x++;  // ERROR: const means read-only
}

int main() 
{
    int a = 5;
    incrementByValue(a);
    cout << "After by value: " << a << "\n";  // Still 5
    
    int b = 5;
    incrementByReference(b);
    cout << "After by reference: " << b << "\n";  // Now 6
    
    int c = 5;
    incrementByPointer(&c);
    cout << "After by pointer: " << c << "\n";  // Now 6
    
    printByConstRef(42);  // Efficient for large objects
    
    return 0;
}

```

Recursion as Control Flow

**Theory**: A function calls itself, creating a chain of function calls. Each call gets its own stack frame.

```c++
#include <iostream>
using namespace std;

// EXAMPLE 1: Simple recursion - factorial
int factorial(int n) 
{
    if (n <= 1) return 1;  // Base case
    return n * factorial(n - 1);  // Recursive case
}

// EXAMPLE 2: Count down
void countdown(int n) 
{
    if (n <= 0) return;  // Base case
    cout << n << " ";
    countdown(n - 1);  // Recursive call
}

// EXAMPLE 3: Sum of array
int sumArray(int arr[], int n) 
{
    if (n <= 0) return 0;  // Base case
    return arr[n - 1] + sumArray(arr, n - 1);  // Recursive case
}

int main() 
{
    cout << "5! = " << factorial(5) << "\n";  // Output: 120
    
    cout << "Countdown: ";
    countdown(5);
    cout << "\n";  // Output: 5 4 3 2 1
    
    int arr[] = {1, 2, 3, 4, 5};
    cout << "Sum: " << sumArray(arr, 5) << "\n";  // Output: 15
    
    return 0;
}

```

**Tail Recursion**: A recursive call is the last operation in the function. Some compilers can optimize tail recursion into a loop, avoiding stack growth.
```c++
// Tail recursive (compiler can optimize)
int factorialTail(int n, int acc = 1) 
{
    if (n <= 1) return acc;
    return factorialTail(n - 1, n * acc);  // Last operation is recursive call
}

// Not tail recursive
int factorialNotTail(int n) 
{
    if (n <= 1) return 1;
    return n * factorialNotTail(n - 1);  // Multiplication happens AFTER return
}

```

## RECURSION (Advanced Control Flow)
**Advanced Theory**: Recursion is a powerful control flow technique, but understanding stack mechanics, infinite recursion, and optimization is critical.

Stack Frames and Recursion Depth

Each recursive call creates a new stack frame. The stack is finite, so deep recursion causes stack overflow.

**Stack Overflow**: When the call stack exceeds available memory, the program crashes.

```c++
#include <iostream>
using namespace std;

// EXAMPLE 1: Stack overflow demonstration (BE CAREFUL!)
void infiniteRecursion() 
{
    cout << "Call\n";
    infiniteRecursion();  // Never returns!
}

// DON'T uncomment this:
// int main() { infiniteRecursion(); }  // Crash!

// EXAMPLE 2: Deep recursion with limit
int factorial(int n, int depth = 0) 
{
    cout << string(depth, ' ') << "factorial(" << n << ")\n";
    if (n <= 1) 
    {
        cout << string(depth, ' ') << "base case\n";
        return 1;
    }
    int result = n * factorial(n - 1, depth + 1);
    cout << string(depth, ' ') << "returning " << result << "\n";
    return result;
}

int main() 
{
    cout << "Factorial with call stack visualization:\n";
    cout << "Result: " << factorial(4) << "\n";
    
    // Stack trace:
    // factorial(4)
    //  factorial(3)
    //   factorial(2)
    //    factorial(1)
    //    base case
    //    returning 1
    //   returning 2
    //  returning 6
    // returning 24
    
    return 0;
}

```


Backtracking as Control Flow

Backtracking is a recursive technique where you explore possibilities, and if one fails, you "backtrack" to try another.

```c++
#include <iostream>
#include <vector>
using namespace std;

// EXAMPLE: N-Queens problem (simplified for 4 queens)
bool isSafe(vector<int>& board, int row, int col) 
{
    // Check if placing queen at (row, col) is safe
    for (int i = 0; i < row; i++) 
    {
        int prev_col = board[i];
        // Check column and diagonals
        if (prev_col == col || abs(i - row) == abs(prev_col - col)) 
        {
            return false;
        }
    }
    return true;
}

bool solveNQueens(vector<int>& board, int row) 
{
    int n = 4;
    if (row == n) 
    {
        // Found a solution
        cout << "Solution found: ";
        for (int col : board) cout << col << " ";
        cout << "\n";
        return true;
    }
    
    for (int col = 0; col < n; col++) 
    {
        if (isSafe(board, row, col)) 
        {
            board[row] = col;  // Place queen
            if (solveNQueens(board, row + 1)) 
            {
                return true;  // Solution found
            }
            // Backtrack: remove queen and try next position
            board[row] = -1;
        }
    }
    return false;  // No solution found in this branch
}

int main() 
{
    vector<int> board(4, -1);
    solveNQueens(board, 0);
    return 0;
}

```

Memoization (Optimization)

Memoization caches recursive results to avoid redundant computation.

```c++
#include <iostream>
#include <map>
using namespace std;

// EXAMPLE: Fibonacci with memoization
map<int, long long> memo;

long long fibWithMemo(int n) 
{
    if (n <= 1) return n;
    if (memo.find(n) != memo.end()) 
    {
        cout << "Cache hit for " << n << "\n";
        return memo[n];  // Return cached result
    }
    
    long long result = fibWithMemo(n - 1) + fibWithMemo(n - 2);
    memo[n] = result;  // Cache the result
    return result;
}

int main() 
{
    cout << "Fibonacci(10) with memoization:\n";
    cout << "Result: " << fibWithMemo(10) << "\n";
    
    return 0;
}

```

## EXCEPTION-BASED CONTROL FLOW
**Theory**: Exceptions provide a mechanism to handle errors by transferring control to exception handlers. Unlike return values, exceptions can propagate up the call stack automatically.

try-catch-throw Mechanism

**throw**: Raises an exception, interrupting normal control flow.

**try**: Marks a block of code that might throw exceptions.

**catch**: Handles exceptions thrown in the try block.

```c++
#include <iostream>
#include <stdexcept>
using namespace std;

// EXAMPLE 1: Basic try-catch
int divide(int a, int b) 
{
    if (b == 0) 
    {
        throw invalid_argument("Division by zero");
    }
    return a / b;
}

int main() 
{
    try 
    {
        cout << "10 / 2 = " << divide(10, 2) << "\n";
        cout << "10 / 0 = " << divide(10, 0) << "\n";  // Throws exception
        cout << "This line never executes\n";
    } catch (invalid_argument& e) 
    {
        cout << "Caught exception: " << e.what() << "\n";
    }
    
    cout << "Program continues\n";
    return 0;
}

```

OUTPUT:
```output
10 / 2 = 5
Caught exception: Division by zero
Program continues
```


Stack Unwinding
When an exception is thrown, the program searches for a matching catch block by unwinding the call stack.

```c++
#include <iostream>
#include <stdexcept>
using namespace std;

void functionC() 
{
    cout << "C: Throwing exception\n";
    throw runtime_error("Error in C");
    cout << "C: This doesn't execute\n";
}

void functionB() 
{
    cout << "B: Before call to C\n";
    functionC();
    cout << "B: After call to C (doesn't execute)\n";
}

void functionA() 
{
    cout << "A: Before try-catch\n";
    try 
    {
        functionB();
    } catch (runtime_error& e) 
    {
        cout << "A: Caught exception: " << e.what() << "\n";
    }
    cout << "A: After catch\n";
}

int main() 
{
    functionA();
    cout << "Main: Program continues\n";
    return 0;
}

```

OUTPUT:
```output
A: Before try-catch
B: Before call to C
C: Throwing exception
A: Caught exception: Error in C
A: After catch
Main: Program continues
```

**Stack unwinding process**:

1. `functionC()` throws exception
2. No try-catch in `functionC()`, so unwind
3. No try-catch in `functionB()`, so unwind
4. Try-catch in `functionA()` catches the exception
5. Resume execution after the catch block

```c++
#include <iostream>
#include <stdexcept>
using namespace std;

int main() {
    int choice = 2;
    
    try {
        if (choice == 1) 
        {
            throw invalid_argument("Invalid argument");
        } else if (choice == 2) {
            throw runtime_error("Runtime error");
        } else if (choice == 3) {
            throw 42;  // Throwing int
        }
    } catch (invalid_argument& e) {
        cout << "Caught invalid_argument: " << e.what() << "\n";
    } catch (runtime_error& e) {
        cout << "Caught runtime_error: " << e.what() << "\n";
    } catch (int e) {
        cout << "Caught integer: " << e << "\n";
    } catch (...) {  // Catch-all
        cout << "Caught unknown exception\n";
    }
    
    return 0;
}

```

**Exception Matching Order**: Exceptions are matched against catch blocks in order. The first matching catch executes. More specific exceptions should come before general ones.

```c++
try 
{
    // ...
} catch (invalid_argument& e) {  // More specific
    // ...
} catch (logic_error& e) {  // More general
    // ...
} catch (exception& e) {  // Most general
    // ...
} catch (...) {  // Catch absolutely everything
    // ...
}

```

Exception Safety Guarantees

**Strong Guarantee**: If an exception occurs, the program state is unchanged (all-or-nothing semantics).

**Basic Guarantee**: If an exception occurs, the program is in a valid but possibly modified state.

**No-Throw Guarantee**: The operation cannot throw an exception.


```c++
#include <iostream>
#include <vector>
using namespace std;

class Bank {
    vector<int> accounts = {1000, 2000, 3000};
    
public:
    // Strong guarantee: either transfer succeeds completely or state unchanged
    void transferWithStrongGuarantee(int from, int to, int amount) {
        if (from < 0 || from >= 3 || to < 0 || to >= 3) {
            throw invalid_argument("Invalid account");
        }
        if (accounts[from] < amount) {
            throw runtime_error("Insufficient funds");
        }
        
        // All checks passed before modifying state
        accounts[from] -= amount;
        accounts[to] += amount;
    }
    
    void printAccounts() {
        for (int i = 0; i < accounts.size(); i++) {
            cout << "Account " << i << ": " << accounts[i] << "\n";
        }
    }
};

int main() 
{
    Bank bank;
    
    try {
        bank.transferWithStrongGuarantee(0, 1, 500);  // Success
        bank.printAccounts();
        
        bank.transferWithStrongGuarantee(0, 1, 10000);  // Fails: insufficient funds
    } catch (exception& e) 
    {
        cout << "Exception: " << e.what() << "\n";
        cout << "State after exception:\n";
        bank.printAccounts();  // Unchanged due to strong guarantee
    }
    
    return 0;
}

```

Rethrowing Exceptions

```C++
#include <iostream>
#include <stdexcept>
using namespace std;

int main() {
    try {
        try {
            throw runtime_error("Inner exception");
        } catch (runtime_error& e) {
            cout << "Inner catch: " << e.what() << "\n";
            throw;  // Rethrow the same exception
        }
    } catch (runtime_error& e) {
        cout << "Outer catch: " << e.what() << "\n";
    }
    
    return 0;
}

```

##  JUMP 

**Theory**: Control transfer statements like `goto`, `return`, and exceptions can jump across scopes, potentially causing memory issues if not careful.

---
1. `break` (The Exit Jump)

The `break` statement transfers control **out** of the current loop or `switch` block.

- **Transfer Destination:** The first line of code immediately following the closing brace `}` of the loop.
- **Best for**: Stopping a loop early when a condition is met.
-------
2. `continue` (The Skip Jump)

The `continue` statement transfers control back to the **start** of the loop for the next iteration.

- **Transfer Destination:** * In a `while` loop: Back to the **condition** check.
    
    - In a `for` loop: To the **update** expression (e.g., `i++`), then to the condition.
        
- **Best for:** Filtering data you don't want to process without killing the whole loop.
----
   3. `return` (The Exit-Function Jump)

The `return` statement is the most powerful transfer tool. It doesn't just exit a loop; it exits the **entire function** and goes back to whoever called that function.

- **Transfer Destination:** Back to the line of code that called the function.
    
- **Value Transfer:** It can "carry" data back with it (e.g., `return 5;`).
-----

```C++
#include <iostream>
using namespace std;

int main() {
    // DANGEROUS: Jumping over variable initialization
    goto skip_init;
    int x = 42;  // This gets skipped!
    
    skip_init:
    cout << x << "\n";  // x might be uninitialized or garbage
    
    // BETTER approach:
    goto safe_point;
    int y;  // Declaration without initialization
    
    safe_point:
    y = 10;  // Initialize after the jump point
    cout << y << "\n";
    
    return 0;
}

```

Version 1: The `break` Statement

**Goal:** Stop a search as soon as we find the "Secret Key."
```c++
#include <iostream>
#include <vector>

int main() {
    std::vector<int> vault = {101, 202, 777, 404, 505};
    int secretKey = 777;

    for (int box : vault) {
        if (box == secretKey) {
            std::cout << "Found the key! Stopping search.\n";
            break; // Control jumps out of the for-loop immediately
        }
        std::cout << "Checking box: " << box << "...\n";
    }
    // Execution resumes here after break
    std::cout << "Search complete.";
    return 0;
}
```


Version 2: The `continue` Statement

**Goal:** Print only the "Odd" numbers from a list by skipping the "Even" ones.

```c++
#include <iostream>

int main() {
    for (int i = 1; i <= 6; i++) {
        if (i % 2 == 0) {
            continue; // Control jumps back to the top (increment i++)
        }
        std::cout << "Odd Number: " << i << "\n";
    }
    return 0;
} //**Why use it?** It’s cleaner than wrapping the whole loop body in an `if` statement. It acts as a "filter" at the very top of your loop.
```



Version 3: The `return` Statement

**Goal:** Check if a user is "Blacklisted" and stop the entire function if they are.
```c++
#include <iostream>
#include <string>

void processUser(std::string name) {
    if (name == "Hacker") {
        std::cout << "ALERT: Access Denied for " << name << "!\n";
        return; // Control jumps out of the function entirely
    }

    // This code only runs if the return above didn't happen
    std::cout << "Welcome, " << name << ". Loading your profile...\n";
}

int main() {
    processUser("Alice");
    processUser("Hacker");
    processUser("Bob");
    return 0;
} //**Why use it?** It’s an "Early Exit." It prevents the rest of the function's logic from running if a major condition (like a security breach) isn't met.
```

Version 4: The `goto` Statement (Breaking Nested Loops)
**Goal:** Find a specific number in a 2D grid and exit both loops at once.
```c++
#include <iostream>

int main() {
    int grid[2][2] = {{1, 2}, {3, 4}};
    int target = 3;

    for (int i = 0; i < 2; i++) {
        for (int j = 0; j < 2; j++) {
            if (grid[i][j] == target) {
                std::cout << "Target " << target << " found at " << i << "," << j << "\n";
                goto end_search; // "Teleports" out of both loops
            }
        }
    }

    end_search: // The label target for the jump
    std::cout << "Exited the search grid.";
    return 0;
} //**Why use it?** While `goto` is generally avoided, this is one of the few places it is actually useful. A `break` would only exit the inner `j` loop, leaving you still stuck in the outer `i` loop.
```

|**Statement**|**Scope of Jump**|**Best Visualized As...**|
|---|---|---|
|**`break`**|Out of the current loop.|An **Emergency Exit** door.|
|**`continue`**|To the next loop turn.|A **Skip Track** button on a playlist.|
|**`return`**|Out of the whole function.|**Hanging up the phone** during a call.|
|**`goto`**|To a specific label.|A **Teleportation** pad.|


8.2  CONCURRENCY-AFFECTED CONTROL FLOW (Advanced)

what is this shit ?
 In standard C++, code runs **sequentially** (one line after another). In **Concurrency**, multiple parts of your code run at the **same time** on different CPU cores.This creates "Concurrency-Affected Control Flow," where the order of execution is no longer guaranteed by the text of your code, but by the timing of the operating system.

The Theory: Race Conditions and Synchronization

When two "threads" (independent paths of execution) try to access the same variable at once, you get a **Race Condition**.

Imagine two people trying to withdraw $100 from the same bank account at the exact same millisecond. If the control flow isn't managed, they both might see a balance of $100, both withdraw it, and the account ends up at -$100.

To control this flow, we use **Synchronization Primitives**:

- **Mutex (Mutual Exclusion):** A "lock" that ensures only one thread can enter a section of code at a time.
- **Atomic Operations:** Special variables that can be updated in a single, unbreakable step.
- **Join/Detach:** Controlling when a thread merges back into the main program.

Version 1: The "Uncontrolled" Flow (Dangerous)

In this version, we start two threads that both try to increment the same integer. Because the control flow is overlapping, the result is unpredictable.

```c++
#include <iostream>
#include <thread>
#include <vector>

int counter = 0;

void increase() {
    for (int i = 0; i < 100000; ++i) {
        counter++; // This is NOT safe. Two threads might do this at once.
    }
}

int main() {
    std::thread t1(increase);
    std::thread t2(increase);

    t1.join(); // Wait for t1 to finish
    t2.join(); // Wait for t2 to finish

    // Expected: 200,000 | Actual: Likely a random smaller number
    std::cout << "Final counter: " << counter << std::endl;
    return 0;
}
```

**Why it fails:** The control flow of `t1` and `t2` is "interleaved." One thread reads the value, and before it can write it back, the other thread changes it. The "Transfer of Control" happens at the hardware level, outside your code's visibility.


Version 2: Controlled Flow with `std::mutex`

Here, we use a **Mutex**. It acts like a "talking stick" or a bathroom key. If you don't have the key, you must wait (your control flow stops) until the key is returned.

```c++
#include <iostream>
#include <thread>
#include <mutex>

int counter = 0;
std::mutex mtx; // The "Lock"

void safe_increase() {
    for (int i = 0; i < 100000; ++i) {
        mtx.lock();   // Control flow STOPS here if another thread has the lock
        counter++;    // Critical Section: Only one thread at a time
        mtx.unlock(); // Control flow releases the lock for others
    }
}

int main() {
    std::thread t1(safe_increase);
    std::thread t2(safe_increase);

    t1.join();
    t2.join();

    std::cout << "Final counter: " << counter << std::endl; // Always 200,000
    return 0;
}
```

Version 3: Modern "Atomic" Control Flow

C++ provides `std::atomic`, which tells the CPU: "Do not let any other thread interrupt this specific math operation." This is much faster than a Mutex because it happens at the hardware level.

```c++
#include <iostream>
#include <thread>
#include <atomic>

std::atomic<int> counter(0); // Atomic variable

void atomic_increase() {
    for (int i = 0; i < 100000; ++i) {
        counter++; // The CPU ensures this happens as one "unbreakable" step
    }
}

int main() {
    std::thread t1(atomic_increase);
    std::thread t2(atomic_increase);

    t1.join();
    t2.join();

    std::cout << "Final counter: " << counter << std::endl; // Always 200,000
    return 0;
}
```


| **Tool**          | **Control Effect**                               | **Best Used For...**                                     |
| ----------------- | ------------------------------------------------ | -------------------------------------------------------- |
| **`std::thread`** | Creates a new, parallel path of control.         | Running heavy tasks in the background.                   |
| **`join()`**      | Stops the current thread until another finishes. | Ensuring data is ready before proceeding.                |
| **`mutex`**       | Forces threads to take turns.                    | Protecting complex objects (like a `vector` or `class`). |
| **`atomic`**      | Prevents interruption of a single variable.      | Simple counters or flags shared between threads.         |

In concurrency, **Undefined Behavior** (remember that?) returns. If you have a race condition, your program might work perfectly 999 times and fail on the 1000th time because of a tiny change in CPU temperature or background apps. This makes concurrent control flow the hardest part of C++ to debug.

`std::async` is a "higher-level" way to handle concurrency. While `std::thread` requires you to manually manage locks, joins, and data transfers, `std::async` handles the "plumbing" for you.

Think of `std::thread` like **hiring an employee** (you have to manage them), while `std::async` is like **ordering a pizza** (you place the order, and it arrives whenever it's ready).

The Theory: Futures and Promises

When you use `std::async`, it returns a **`std::future`**.

- **The Future:** This is a placeholder for a value that hasn't been calculated yet. It’s like a "claim check" at a coat rack.
    
- **The Control Flow:** Your main program keeps running. Only when you call `.get()` does the program "wait" (block) until the result is ready.
EXAMPLE: 
The "Pizza Order" (Simple Background Task)
In this version, we start a "slow" task and let it run while we continue doing something else.
```c++
#include <iostream>
#include <future> // Needed for async and future

int main() {
    // 1. "Order" the result. The task starts in the background.
    // std::async returns a "future" (your receipt).
    std::future<int> pizza_order = std::async(std::launch::async, []{
        // Simulate a slow task (like baking a pizza)
        return 42; 
    });

    std::cout << "Doing other stuff while waiting..." << std::endl;

    // 2. "Pick up" the result. 
    // This will pause the program until the task is done.
    int result = pizza_order.get(); 

    std::cout << "Result is: " << result << std::endl;
    return 0;
}
```

The "Two Workers" (Simple Parallel Task)
In this version, we have two workers doing math at the same time to get the answer faster.
```C++
#include <iostream>
#include <future>

int main() {
    // Start two tasks at the same time
    auto task1 = std::async(std::launch::async, []{ return 10 + 10; });
    auto task2 = std::async(std::launch::async, []{ return 20 + 20; });

    // Combine the answers
    // .get() waits for each task to finish
    int final_answer = task1.get() + task2.get();

    std::cout << "Final Answer: " << final_answer << std::endl;
    return 0;
}
```

Deadlock Scenarios
A deadlock occurs when threads wait for each other indefinitely.
```C++
#include <iostream>
#include <thread>
#include <mutex>
using namespace std;

mutex lock1, lock2;

void thread1Func() {
    lock1.lock();
    cout << "Thread 1 acquired lock1\n";
    this_thread::sleep_for(chrono::milliseconds(100));
    
    lock2.lock();  // Thread 2 might hold lock2, waiting for lock1
    cout << "Thread 1 acquired lock2\n";
    lock2.unlock();
    lock1.unlock();
}

void thread2Func() {
    lock2.lock();
    cout << "Thread 2 acquired lock2\n";
    this_thread::sleep_for(chrono::milliseconds(100));
    
    lock1.lock();  // Thread 1 might hold lock1, waiting for lock2
    cout << "Thread 2 acquired lock1\n";
    lock1.unlock();
    lock2.unlock();
}

// DEADLOCK SCENARIO:
// int main() {
//     thread t1(thread1Func);
//     thread t2(thread2Func);
//     t1.join();  // Hangs indefinitely
//     t2.join();
//     return 0;
// }

// SOLUTION: Acquire locks in the same order
void thread1FuncFixed() {
    lock1.lock();
    lock2.lock();
    cout << "Thread 1 has both locks\n";
    lock2.unlock();
    lock1.unlock();
}

void thread2FuncFixed() {
    lock1.lock();  // Acquire in same order
    lock2.lock();
    cout << "Thread 2 has both locks\n";
    lock2.unlock();
    lock1.unlock();
}

int main() {
    thread t1(thread1FuncFixed);
    thread t2(thread2FuncFixed);
    t1.join();
    t2.join();
    cout << "No deadlock!\n";
    return 0;
}

```

Condition Variables
Condition variables allow threads to wait for specific conditions.
```C++
#include <iostream>
#include <thread>
#include <mutex>
#include <condition_variable>
using namespace std;

mutex mtx;
condition_variable cv;
bool ready = false;

void waiter() {
    unique_lock<mutex> lock(mtx);
    cv.wait(lock, []{ return ready; });  // Wait until ready is true
    cout << "Waiter: Condition met!\n";
}

void signaler() {
    this_thread::sleep_for(chrono::seconds(1));
    {
        unique_lock<mutex> lock(mtx);
        ready = true;
    }
    cv.notify_all();  // Wake up waiting threads
}

int main() {
    thread t1(waiter);
    thread t2(signaler);
    
    t1.join();
    t2.join();
    
    cout << "Done\n";
    return 0;
}

```

## COMPILE-TIME CONTROL FLOW (Meta)



**Theory**: Preprocessor directives allow decisions about which code is compiled before runtime. This enables conditional compilation based on platform, debug mode, or feature flags.
Preprocessor Directives
```c++
#include <iostream>
using namespace std;

// EXAMPLE 1: Conditional compilation
#define DEBUG

#ifdef DEBUG
    #define PRINT(x) cout << "[DEBUG] " << x << "\n"
#else
    #define PRINT(x)  // In release mode, PRINT does nothing
#endif

int main() {
    PRINT("This message appears in debug mode");
    cout << "This always appears\n";
    
    return 0;
}

// EXAMPLE 2: Platform-specific code
#ifdef _WIN32
    #include <windows.h>
    // Windows-specific code
#elif defined(__linux__)
    #include <unistd.h>
    // Linux-specific code
#else
    // Other platforms
#endif

// EXAMPLE 3: Feature flags
#define ENABLE_LOGGING 1

int getValue() {
    int value = 42;
    
    #if ENABLE_LOGGING
        cout << "getValue returning: " << value << "\n";
    #endif
    
    return value;
}

```

constexpr and if constexpr

**constexpr**: Evaluates at compile-time if possible, enabling compile-time computations.

**if constexpr**: Compile-time conditional branching. Only the true branch is compiled.

```c++
#include <iostream>
using namespace std;

// EXAMPLE 1: constexpr function
constexpr int fibonacci(int n) {
    if (n <= 1) return n;
    return fibonacci(n - 1) + fibonacci(n - 2);
}

int main() {
    constexpr int result = fibonacci(10);  // Computed at compile-time!
    cout << "Fib(10) = " << result << "\n";
    
    return 0;
}

// EXAMPLE 2: if constexpr (C++17)
template <typename T>
void printType(T value) {
    if constexpr (is_integral_v<T>) {
        cout << "Integral type: " << value << "\n";
    } else if constexpr (is_floating_point_v<T>) {
        cout << "Floating-point type: " << value << "\n";
    } else {
        cout << "Other type\n";
    }
}

int main() {
    printType(42);       // Calls integral branch
    printType(3.14);     // Calls floating-point branch
    
    return 0;
}

```

**Performance Benefit**: Code eliminated by compile-time conditions doesn't appear in the final executable, reducing binary size and enabling zero-cost abstractions.

`static_assert` (The Compile-Time Stop)

`static_assert` is the compile-time version of an error. It checks a condition and, if it fails, the **compilation stops** and displays a message.

```c++
#include <type_traits>

template <typename T>
void only_integers(T val) {
    // If someone tries to pass a 'string' or 'float', the code won't even build
    static_assert(std::is_integral_v<T>, "This function only accepts integers!");
}
```

| **Feature**         | **When is it decided?**   | **Result in Binary**                        |
| ------------------- | ------------------------- | ------------------------------------------- |
| **Normal `if`**     | While program is running. | Both branches are in the binary.            |
| **`if constexpr`**  | While code is compiling.  | Only the "True" branch is in the binary.    |
| **`constexpr` fn**  | While code is compiling.  | Replaced by the final answer (e.g., `100`). |
| **`static_assert`** | While code is compiling.  | No binary is created (Compilation fails).   |
Why should you use it:

1. **Performance:** Your program does zero work for these checks at runtime.
2. **Size:** The final `.exe` or app is smaller because "dead code" is deleted.
3. **Safety:** `static_assert` catches bugs before you ever run the program.


##  CONTROL FLOW IN STL & ALGORITHMS

**Theory**: STL algorithms use function pointers, functors, and lambdas to control behavior based on predicates and comparison functions.

1. Internalizing the Loop: `std::for_each`

In a standard loop, the "jump" and "increment" are visible. In `std::for_each`, the control flow is hidden inside the algorithm.

```c++
#include <iostream>
#include <vector>
#include <algorithm>

int main() {
    std::vector<int> nums = {1, 2, 3};

    // Control flow is managed by the STL
    std::for_each(nums.begin(), nums.end(), [](int n) {
        std::cout << n * n << " ";
    });
} //**The Shift:** You no longer use `break` or `continue` here. To "skip" an item, you simply use a return statement inside the lambda.
```

2. Conditional Control Flow: `std::find_if`

Many STL algorithms have built-in "short-circuit" control flow. For example, `std::find_if` will stop iterating the moment it finds a match. This is the STL equivalent of a `break` statement.

```c++
auto it = std::find_if(nums.begin(), nums.end(), [](int n) {
    return n > 10; // The moment this is true, the loop "breaks" internally
});
```

3. Predicate-Driven Flow
Instead of `if/else` blocks inside your loop, you use **Predicates** (functions that return true/false). The STL uses these to decide which path the data takes.

| **Algorithm**        | **Control Flow Logic**                                                  |
| -------------------- | ----------------------------------------------------------------------- |
| **`std::all_of`**    | Returns `false` and stops (breaks) as soon as one element fails.        |
| **`std::any_of`**    | Returns `true` and stops (breaks) as soon as one element passes.        |
| **`std::copy_if`**   | Iterates through all, but only "transfers" data that meets a condition. |
| **`std::partition`** | Reorganizes the control flow of data, moving "true" items to the front. |
4. Execution Policies (Parallel Control Flow)
Since C++17, you can change the entire "Execution Flow" of an algorithm by passing a single parameter. This tells the STL to use the **Concurrency** we discussed earlier.

```c++
#include <execution> // Needed for execution policies

// Sequential (Standard one-by-one flow)
std::sort(std::execution::seq, v.begin(), v.end());

// Parallel (Multi-threaded flow)
std::sort(std::execution::par, v.begin(), v.end());
```

**Why this is powerful:** You don't have to write any `std::thread` or `std::async` code. You just tell the STL, "Run this in parallel," and it manages the complex thread control flow for you.


| **Feature**       | **Manual Loop (for)**         | **STL Algorithm**                             |
| ----------------- | ----------------------------- | --------------------------------------------- |
| **Control**       | You manage indices/iterators. | Algorithm manages them.                       |
| **Short-circuit** | Explicit `break`.             | Automatic (in `find`, `any_of`, etc).         |
| **Optimization**  | Hard to optimize.             | Often optimized by compiler/library vendors.  |
| **Parallelism**   | Very hard to implement.       | One parameter change (`std::execution::par`). |
Predicate-Based Control Flow
```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    vector<int> data = {10, 25, 30, 15, 40, 20};
    
    // EXAMPLE 1: find_if with lambda
    auto it = find_if(data.begin(), data.end(), 
                      [](int x) { return x > 25; });
    if (it != data.end()) {
        cout << "Found: " << *it << "\n";  // Output: 30
    }
    
    // EXAMPLE 2: count_if with predicate
    int count = count_if(data.begin(), data.end(),
                        [](int x) { return x < 25; });
    cout << "Count < 25: " << count << "\n";  // Output: 4
    
    // EXAMPLE 3: sort with custom comparator
    sort(data.begin(), data.end(),
         [](int a, int b) { return a > b; });  // Sort descending
    
    cout << "Sorted: ";
    for (int x : data) cout << x << " ";
    cout << "\n";  // Output: 40 30 25 20 15 10
    
    return 0;
}

```

Short-Circuiting Algorithms
Some algorithms stop early if a condition is met.

```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    vector<int> data = {1, 2, 3, 4, 5};
    
    // EXAMPLE 1: all_of (short-circuits on first false)
    bool allPositive = all_of(data.begin(), data.end(),
                             [](int x) { return x > 0; });
    cout << "All positive: " << (allPositive ? "yes" : "no") << "\n";
    
    // EXAMPLE 2: any_of (short-circuits on first true)
    bool anyGreaterThan3 = any_of(data.begin(), data.end(),
                                  [](int x) { return x > 3; });
    cout << "Any > 3: " << (anyGreaterThan3 ? "yes" : "no") << "\n";
    
    // EXAMPLE 3: find (short-circuits on find)
    auto it = find(data.begin(), data.end(), 3);
    if (it != data.end()) {
        cout << "Found 3 at index: " << (it - data.begin()) << "\n";
    }
    
    return 0;
}

```

Iterator-Driven Flow
Algorithms iterate through containers based on iterator type (forward, bidirectional, random access).

```c++
#include <iostream>
#include <vector>
#include <list>
#include <algorithm>
using namespace std;

int main() {
    vector<int> vec = {10, 20, 30, 40, 50};
    list<int> lst = {10, 20, 30, 40, 50};
    
    // EXAMPLE 1: Algorithms on vectors (random access)
    reverse(vec.begin(), vec.end());  // Efficient: O(n)
    cout << "Reversed vector: ";
    for (int x : vec) cout << x << " ";
    cout << "\n";
    
    // EXAMPLE 2: Algorithms on lists (bidirectional)
    reverse(lst.begin(), lst.end());  // Works, same complexity
    cout << "Reversed list: ";
    for (int x : lst) cout << x << " ";
    cout << "\n";
    
    // EXAMPLE 3: distance() - different efficiency
    auto it = find(vec.begin(), vec.end(), 30);
    int index = distance(vec.begin(), it);  // O(1) for vectors
    cout << "Index in vector: " << index << "\n";
    
    auto it2 = find(lst.begin(), lst.end(), 30);
    int index2 = distance(lst.begin(), it2);  // O(n) for lists
    cout << "Index in list: " << index2 << "\n";
    
    return 0;
}

```

Lazy Evaluation
Some libraries use lazy evaluation where operations are computed only when results are needed.

```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    vector<int> data = {1, 2, 3, 4, 5};
    
    // EXAMPLE: Traditional eager evaluation
    vector<int> doubled;
    transform(data.begin(), data.end(),
              back_inserter(doubled),
              [](int x) { return x * 2; });
    
    // All elements doubled immediately
    cout << "Doubled: ";
    for (int x : doubled) cout << x << " ";
    cout << "\n";
    
    // Lazy evaluation would delay computation until accessed
    // (not directly available in standard C++, but possible with libraries like ranges-v3)
    
    return 0;
}

```

[[CHAPTER 3]]
