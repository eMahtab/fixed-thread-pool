# Thread Pool (fixed thread pool)
**ExecutorService Creation:**

Executors.newFixedThreadPool(4) creates a thread pool with 4 threads. This means up to 4 tasks can run concurrently.

**Submitting Tasks:**

A loop is used to create and submit 10 Task instances to the executor. Each Task object represents a unit of work to be performed by a thread.

**Task Execution:**

The Task class implements Runnable. Each task sleeps for a random period of up to 1 second to simulate a workload, and then it prints its start and end status.
```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

// Task class that implements Runnable
class Task implements Runnable {
   private final int taskId;
   public Task(int taskId) {
      this.taskId = taskId;
   }

   @Override
   public void run() {
      System.out.println("Task " + taskId + " started on thread " + Thread.currentThread().getName());
      try {
         // Simulate task duration
         Thread.sleep((long) (Math.random() * 1000));
      } catch (InterruptedException e) {
         System.out.println("Task " + taskId + " was interrupted");
      }
      System.out.println("Task " + taskId + " completed on thread " + Thread.currentThread().getName());
   }
}

public class Main {

   public static void main(String[] args) {
      // Create a fixed thread pool with 4 threads
      ExecutorService executor = Executors.newFixedThreadPool(4);
      // Submit 10 tasks to the executor
      for (int i = 1; i <= 10; i++) {
         Runnable task = new Task(i);
         executor.submit(task);
      }

      // Shut down the executor and wait for the tasks to complete
      executor.shutdown();
      try {
         // Wait for all tasks to finish, with a maximum wait time
         if (!executor.awaitTermination(1, TimeUnit.MINUTES)) {
            executor.shutdownNow();
         }
      } catch (InterruptedException e) {
         executor.shutdownNow();
      }
      System.out.println("All tasks completed.");
   }
}

```

## Execution Output :
```
Task 2 started on thread pool-1-thread-2
Task 1 started on thread pool-1-thread-1
Task 4 started on thread pool-1-thread-4
Task 3 started on thread pool-1-thread-3
Task 1 completed on thread pool-1-thread-1
Task 5 started on thread pool-1-thread-1
Task 5 completed on thread pool-1-thread-1
Task 6 started on thread pool-1-thread-1
Task 2 completed on thread pool-1-thread-2
Task 7 started on thread pool-1-thread-2
Task 4 completed on thread pool-1-thread-4
Task 8 started on thread pool-1-thread-4
Task 3 completed on thread pool-1-thread-3
Task 9 started on thread pool-1-thread-3
Task 6 completed on thread pool-1-thread-1
Task 10 started on thread pool-1-thread-1
Task 7 completed on thread pool-1-thread-2
Task 8 completed on thread pool-1-thread-4
Task 10 completed on thread pool-1-thread-1
Task 9 completed on thread pool-1-thread-3
All tasks completed.
```

**Key Points About shutdown()**

**No New Tasks:** After calling shutdown(), the executor will reject any new tasks submitted to it. Any attempt to submit a new task will result in a RejectedExecutionException.

**Continues Running Submitted Tasks:** All tasks submitted before shutdown() was called will continue to run until they are finished.

**Does Not Block:** The shutdown() method itself does not block; it simply initiates the shutdown process and returns immediately.

**Waiting for Task Completion: Using awaitTermination()**
If you want to wait for all tasks to finish after calling shutdown(), you should use awaitTermination() in combination with shutdown(). Here’s how:

```java
executor.shutdown(); // Initiates shutdown, allowing submitted tasks to complete
try {
    // Wait for tasks to complete or time out after a specific period
    if (!executor.awaitTermination(1, TimeUnit.MINUTES)) {
        executor.shutdownNow(); // Forces shutdown if tasks are still running after timeout
    }
} catch (InterruptedException e) {
    executor.shutdownNow(); // If interrupted, force shutdown
}
```
`awaitTermination(long timeout, TimeUnit unit):` This method blocks until either:
1. All tasks complete execution.
2. The specified timeout period expires.
3. The current thread is interrupted.

If `awaitTermination()` times out, you can decide to force a shutdown with shutdownNow(), which attempts to stop all running tasks immediately.

## What if, shutdown is handled properly:

If shutdown() is not called, the threads in the thread pool will continue to exist and consume system resources, even after they finish executing the submitted tasks. This can lead to resource leaks, particularly in applications that create and discard thread pools frequently.
