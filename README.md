# PhiLang(ΦLang)

*PhiLang(ΦLang)* is a programming language targeting stable and multi-threaded programs or tutorial purpose for newbie.

### Examples


## Following is the simeplest example of PhiLang(ΦLang).
```clj
  // Simplest Example
  use standard_memory;  // Memory configuration
  use standard_thread;  // Thread configuration
  
  std_thread main_thread()
  {
    int[0] = 10 + 2;  // Dummy calculation
  }
```
Simplese Example doesn't have output.

## Alias example
```clj
  // Define Alias
  use standard_memory;
  use standard_thread;
  use standard_consoleio; // Standard console I/O library
  
  std_thread main_thread()
  {   
    alias int[0] X; // Now you can access int[0] using alias 'X'
    
    X = 10;
    X = X + 1;  // This is same as 'int[0] = int[0] + 1;'
    printf('%d',int[0]);
  }
  
```
Output will be '11'.

## Daemon example
```clj
  // Simple daemon Example
  use standard_memory;
  use standard_thread;
  use standard_consoleio; // Standard console I/O library
  
  std_thread thread1()  // You may use any name to thread
  {
    printf('Tick');
    
    rerun(0); // Rerun as soon as possible
  }
```
Output will be 'TickTickTickTickTickTickTickTick....'.

## Multi-thread example
Following example is simple multi-thread program.
```clj
  // Simple multi-thread example
  use standard_memory;
  use standard_thread;
  use standard_consoleio; // Standard console I/O library
  
  std_thread thread0()
  {
    printf('Tick');
    rerun(0); // Rerun as soon as possible
  }
  
  std_thread thread1()
  {    
    printf('Tock');
    rerun(0); // Rerun as soon as possible
  }
  
```
The output could be any combination of 'Tick' and 'Tock'.
ex) 'TickTockTiTocckk...'.


## Function call example
Following example is simple function call.
```clj
  // Simple function call
  use standard_memory;
  use standard_thread;
  use standard_consoleio; // Standard console I/O library
  
  function function_A(char[5] arg0)  // arg0 is char * 5 length memory. You may use char[6] or char[999] for this. But char[4] or less will cause 'Access Violation Error'.
  {
    printf(arg0);
    return;   // null return
  }
  
  std_thread thread1()
  {    
    function_A('Tick'); // 'Tick' is char[5] memory. ['T'], ['i'], ['c'], ['k'], ['\0']
    rerun(0); // Rerun as soon as possible
  }
```
The output is 'TickTickTickTickTickTickTickTickTickTickTickTickTickTick....'.


## Scope rule example 1
Following example shows scope rule.
```clj
  // Scope rule
  use standard_memory;
  use standard_thread;
  use standard_consoleio; // Standard console I/O library
  
  function function_A(int[0] arg0)
  {
    int[0] = 1; // local scope
    printf('%d\n' , arg0+int[0] );
    return;   // null return
  }
  
  int[0]=10;
  std_thread thread1()
  {    
    @int[0]= @int[0] + 1; // operand @ means parent's scope
    function_A( @int[0] );
    rerun(0); // Rerun as soon as possible
  }
```
The output is '12\n13\n14\n....'.

## Lock
Following example shows locking memory.
You may lock memory with lock() function.
You cannot place anything before lock() function except lock() function.
And all thread release locked memory when it returns.

```clj
  // Lock example
  use standard_memory;
  use standard_thread;
  use standard_consoleio;
  
  int[0]=0;
  std_thread thread1()
  {
    // You cannot place anything here
    @int[0].lock(); // wait until lock succeed
    printf('t1');
    rerun(0); // Rerun as soon as possible
  } // if thread1 returns, @int[0] will be unlocked

  std_thread thread1()
  {
    // You cannot place anything here
    @int[0].lock(); // wait until lock succeed
    printf('t2');
    rerun(0); // Rerun as soon as possible
  } // if thread1 returns, @int[0] will be unlocked
```
The output is combination of 't1' and 't2'. ex) 't1t1t2t2t1t2....'.

## Deadlock detecting
Following examples will generate 'Deadlock Violation Error'.
lock() function order should be same.
```clj
  // Deadlock 1
  use standard_memory;
  use standard_thread;
  use standard_consoleio;
  
  int[0]=0;
  int[1]=0;
  std_thread thread1()
  {
    @int[0].lock();
    @int[1].lock();
    printf('t1');
    rerun(0);
  }

  std_thread thread1()
  {
    @int[1].lock();
    @int[0].lock();
    printf('t2');
    rerun(0);
  }
```

```clj
  // Deadlock 2
  use standard_memory;
  use standard_thread;
  use standard_consoleio;
  
  int[0]=0;
  int[1]=0;
  int[2]=0;
  std_thread thread1()
  {
    @int[0].lock();
    @int[1].lock();
    printf('t1');
    rerun(0);
  }

  std_thread thread2()
  {
    @int[0].lock();
    @int[2].lock(); // This lock will cause deadlock! Compiler will raise 'Deadlock Violation Error'.
    printf('t2');
    rerun(0);
  }

  std_thread thread3()
  {
    @int[2].lock();
    @int[1].lock();  // This lock will cause deadlock! Compiler will raise 'Deadlock Violation Error'.
    printf('t3');
    rerun(0);
  } 
```

But there could be undetectable deadlocks as following example.
```clj
  // Deadlock undetectable
  ...
  std_thread thread1()
  {
    @int[0].lock();
    @int[@int[0]].lock(); // Variables in lock variable
    printf('t1');
    rerun(0);
  }

  std_thread thread1()
  {
    @int[1].lock();
    @int[@int[1]].lock(); // Variables in lock variable
    printf('t2');
    rerun(0);
  }
```
If variable exists in locking variable, deadlock is undetectable.

