---
layout: post
title: Java Multi-thread 적용하기
---

```java
package kr.co.wips.cluster.common;

import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

	public class ThreadTest {

		// MAIN
	    public static void main(String[] args) throws InterruptedException {

	    	List<Integer> result = threadStart();

	    };

	    // THREAD START
	    public static List<Integer> threadStart() throws InterruptedException {

	    	// THREAD TASK
	    	class task implements Callable<Integer> {

	            @Override
	            public Integer call() throws Exception {
	                int sum = 0;
	                for (int i = 1; i <= 10; i++) {
	                    sum += i;
	                }

	                return sum;
	            }

	        };

	        // EXECUTOR
	    	ExecutorService executorService = Executors.newFixedThreadPool(250);

		        List<task> tasks = new ArrayList<task>();

		        task task = new task();

				tasks.add(task);
				tasks.add(task);
				tasks.add(task);
				tasks.add(task);
				tasks.add(task);
				tasks.add(task);
				tasks.add(task);
				tasks.add(task);
				tasks.add(task);
				tasks.add(task);

				List<Future<Integer>> futureList = executorService.invokeAll(tasks);

				List<Integer> resultList = new ArrayList<>();

				for(Future<Integer> future: futureList){
					try {
						Integer taskClusterParamtList = future.get();

						resultList.add(taskClusterParamtList);
					}
					catch (Exception e){
						e.printStackTrace();
					}
				}

		        executorService.shutdown();

		        return resultList;
	    }

	}
```
