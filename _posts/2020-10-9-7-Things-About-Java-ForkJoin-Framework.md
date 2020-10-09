---
layout: post
title: 7 Things About Java Fork/Join Framework
---

#### - What:
1. Since Java 7, in order to make it easier to write parallel programs, the implementation of the ExecutorService interface.
2. Using divide and conquer approach to helps you take advantage of multiple processors
3. Worker threads that run out of things to do can steal tasks from other threads that are still busy.
4. Abstract `ForkJoinTask` has 2 subclasses: `RecursiveAction` and `RecursiveTask` to use, which run with the `ForkJoinPool`.


#### - When:
1. You have a big task and want to split it to run in parallel in several sub tasks.



#### - Notes: 
1. [RecursiveTask is] A recursive result-bearing ForkJoinTask.
[RecursiveAction is] A recursive resultless ForkJoinTask.
2. `ForkJoinPool#commonPool()` is a static thread-pool, which is lazily initialized when it's actually needed and is the default pool.
3. `Future` and `<Collection>.parallelStream()` can both use `ForkJoinPool`.
4. `ForkJoinTask` is a task that can be submitted to a ForkJoinPool, while `CompletableFuture` is a promise that can work with "any Executor".
5. `CompletableFutures` are more appropriate for a Reactive programming model.
6. Its implementation restricts the maximum number of running threads to 32767.
7. ForkJoinPool fjp = new ForkJoinPool(N); // N can configure the number of threads in the pool.


#### - Examples:
* ##### RecursiveTask: - [[test it online]](http://jdoodle.com/ia/2p9)
```java
public class Sum extends RecursiveTask<Double> { 
    final int seqThreshold = 500; 
    double[] data; 
    int start, end; 
  
    Sum(double[] data, int start, int end) 
    { 
        this.data = data; 
        this.start = start; 
        this.end = end; 
    } 
  
    @Override
    protected Double compute() 
    { 
        double sum = 0; 
        if ((end - start) < seqThreshold) { 
            for (int i = start; i < end; i++) 
                sum += data[i]; 
        } 
        else { 
            int middle = (start + end) / 2; 
  
            Sum subtaskA = new Sum(data, start, middle); 
            Sum subtaskB = new Sum(data, middle, end); 
  
            subtaskA.fork(); 
            subtaskB.fork(); 
  
            sum += subtaskA.join() + subtaskB.join(); 
        } 
        return sum; 
    } 

    public static void main(String[] args) 
    { 
        ForkJoinPool fjp = new ForkJoinPool(); 
  
        double[] nums = new double[5000]; 
        for (int i = 0; i < nums.length; i++) { 
            nums[i] = i+1; 
        } 
        Sum task = new Sum(nums, 0, nums.length); 
        double summation = fjp.invoke(task); 
        System.out.printf("summation: %.0f\n", summation);
    } 
}
```

Outputs:
```bash
summation: 12502500
```
Output is the sum of 1 to 5000 = 12502500, 
this is done by divide and conquer method,
split the subtask again and again until they are less than 500.

* ##### RecursiveAction - [[test it online]](https://www.jdoodle.com/iembed/v0/2pa)
```java
public class SqrtTransform extends RecursiveAction { 
    final int seqThreshold = 1000; 
  
    double[] data; 
  
    // Determines what part of data to process 
    int start, end; 
  
    SqrtTransform(double[] data, int start, int end) 
    { 
        this.data = data; 
        this.start = start; 
        this.end = end; 
    } 
  
    // The method where parallel computation will occur 
    @Override
    protected void compute() 
    { 
        // If the number of elements are less 
        // than the sequential threshold 
        if ((end - start) < seqThreshold) { 
            for (int i = start; i < end; i++) { 
                data[i] = Math.sqrt(data[i]); 
            } 
        } 
        else { 
            // Otherwise, continue to break the data into smaller pieces 
            // Find the midpoint 
            int middle = (start + end) / 2; 
  
            // Invoke new tasks, using the subdivided tasks. 
            invokeAll(new SqrtTransform(data, start, middle), 
                      new SqrtTransform(data, middle, end)); 
        } 
    } 
    
    public static void main(String[] args) 
    { 
        // Create a pool of threads. 
        ForkJoinPool fjp = new ForkJoinPool(); 
        double[] nums = new double[100000]; 
  
        // Give nums some values 
        for (int i = 0; i < nums.length; i++) { 
            nums[i] = (double)i; 
        } 
        System.out.println("A portion of the original sequence"); 
        for (int i = 0; i < 9; i++) { 
            System.out.print(nums[i] + " "); 
        } 
        System.out.println(); 
        SqrtTransform task 
            = new SqrtTransform(nums, 0, nums.length); 
  
        // Start the task 
        fjp.invoke(task); 
        System.out.println("A portion of the transformed sequence"
                           + " (to four decimal places): "); 
        for (int i = 0; i < 9; i++) { 
            System.out.printf("%.4f ", nums[i]); 
        } 
    } 
} 
```
Notes:
1. RecursiveAction does return a value, it's just always null, so unlike `RecursiveTask`, it doesn't carry the result, usually used on changing the property/item directly.



---

##### - References:
[https://docs.oracle.com/javase/tutorial/essential/concurrency/forkjoin.html](https://docs.oracle.com/javase/tutorial/essential/concurrency/forkjoin.html)
[https://dzone.com/articles/be-aware-of-forkjoinpoolcommonpool](https://dzone.com/articles/be-aware-of-forkjoinpoolcommonpool)
[https://stackoverflow.com/questions/33944471/forkjointask-vs-completablefuture](https://stackoverflow.com/questions/33944471/forkjointask-vs-completablefuture)
[https://www.geeksforgeeks.org/java-util-concurrent-recursivetask-class-in-java-with-examples/](https://www.geeksforgeeks.org/java-util-concurrent-recursivetask-class-in-java-with-examples/)
[https://www.geeksforgeeks.org/java-util-concurrent-recursiveaction-class-in-java-with-examples/?ref=rp](https://www.geeksforgeeks.org/java-util-concurrent-recursiveaction-class-in-java-with-examples/?ref=rp)
[https://stackoverflow.com/questions/50817843/difference-between-recursive-task-and-recursive-action-in-forkjoinpool](https://stackoverflow.com/questions/50817843/difference-between-recursive-task-and-recursive-action-in-forkjoinpool)