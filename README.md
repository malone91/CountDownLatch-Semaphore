# latch
delay the progress of thread until it is terminated

import java.util.concurrent.CountDownLatch;

/**
 * 使用闭锁统计多个并发线程运行所需要的时间总和
 * Created by Ablert
 * on 2018/7/9.
 */
public class ConcurrentThreadTimeWent {

    public static void main(String[] args) {
        Runnable task = () -> {
            System.out.println("hello java latch 闭锁， it is like a door");
        };
        try {
            long time = timeTask(Runtime.getRuntime().availableProcessors() * 4, task);
            System.out.println("concurrent threads spend " + time + " nanos in finishing task");
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }

    /**
     * 返回线程执行的时间
     * @param nThreads
     * @param task
     * @return
     * @throws InterruptedException
     */
     
    public static long timeTask(int nThreads, final Runnable task) throws InterruptedException {
        final CountDownLatch startGate = new CountDownLatch(1);
        final CountDownLatch endGate = new CountDownLatch(nThreads);
        for (int i = 0; i < nThreads; i++) {
            Thread t = new Thread() {
                @Override
                public void run() {
                    try {
                        startGate.await();
                        try {
                            task.run();
                        } finally {
                            endGate.countDown();
                        }
                    } catch (InterruptedException e) {
                        //不仅仅是要抛出异常，还要进行处理，保存中断的证据
                        Thread.currentThread().interrupt();
                    }
                }
            };
            t.start();
        }

        long startTime = System.nanoTime();
        startGate.countDown();
        endGate.await();
        return System.nanoTime() - startTime;
    }
}
