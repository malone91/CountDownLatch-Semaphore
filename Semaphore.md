package com.melo.semaphore;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Semaphore;

/**
 * 使用信号量控制线程并发的数量，比如有十万个读写，但只有10个数据库连接，
 * 所以要求必有10个线程并发，假设该demo的总共线程数量是30个
 * @author 76009
 * @date 2018/8/4
 */
 
public class SemaphoreDemo {

    /**
     * 循环数
     */
     
    private static final int THREAD_COUNT = 30;

    /**
     * 线程最大并发数量
     */
     
    private static Semaphore semaphore = new Semaphore(10);

    private static ExecutorService executorService = Executors.newFixedThreadPool(THREAD_COUNT);

    public static void main(String[] args) {
    
        for (int i = 0; i < THREAD_COUNT; i++) {
        
            executorService.execute(new Runnable() {
            
                @Override
                public void run() {
                
                    try {
                    
                        semaphore.acquire();
                        
                        System.out.println("i");
                        
                        semaphore.release();
                        
                    } catch (InterruptedException e) {
                    
                        e.printStackTrace();
                    }
                }
            });
        }
        
        executorService.shutdown();
    }
}
