# Thread Pool (fixed thread pool)
ExecutorService Creation:

Executors.newFixedThreadPool(4) creates a thread pool with 4 threads. This means up to 4 tasks can run concurrently.
Submitting Tasks:

A loop is used to create and submit 10 Task instances to the executor. Each Task object represents a unit of work to be performed by a thread.
Task Execution:

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
