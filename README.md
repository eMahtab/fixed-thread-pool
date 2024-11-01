# fixed-thread-pool

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
