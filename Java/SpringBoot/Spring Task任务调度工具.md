Spring Task 是 Spring 提供的轻量级定时任务工具，也就意味着**不需要再添加第三方依赖了**

# **cron表达式**

在线cron生成器https://cron.qqe2.com/

通过cron表达式可以**定义任务触发的时间**

**构成规则：**&#x5206;为6或7个域，由空格分隔开，每个域代表一个含义

每个域的含义分别为：秒、分钟、小时、日、月、周、年(可选)

![](https://img2024.cnblogs.com/blog/3471133/202510/3471133-20251025231526509-1486039555.png)

![](https://img2024.cnblogs.com/blog/3471133/202510/3471133-20251025231531614-199374662.png)

# &#x20;@EnableScheduling注解开启 Spring Task

# @Scheduled 注解注册 Cron 表达式执行定时任务

默认情况下，@Scheduled 创建的线程池大小为 1，如果想增加线程池大小的话，可以让 SpringTaskConfig 类实现 SchedulingConfigurer 接口，通过 setPoolSize 增加线程池大小。

```javascript
@Configuration
@EnableScheduling
public class SpringTaskConfiguration implements SchedulingConfigurer {

    @Override
    public void configureTasks(ScheduledTaskRegistrar taskRegistrar) {
        // 创建一个线程池任务调度器
        ThreadPoolTaskScheduler threadPoolTaskScheduler = new ThreadPoolTaskScheduler();

        // 设置线程池大小
        threadPoolTaskScheduler.setPoolSize(10);
        // 设置线程池名称前缀
        threadPoolTaskScheduler.setThreadNamePrefix("my-scheduled-task-pool-");
        // 初始化线程池
        threadPoolTaskScheduler.initialize();
        // 设置任务调度器
        taskRegistrar.setTaskScheduler(threadPoolTaskScheduler);
    }
}
```

Spring Task 除了支持 Cron 表达式，还有 **fixedRate（固定速率执行）、fixedDelay（固定延迟执行）、initialDelay（初始延迟）**&#x4E09;种用法

**`initialDelay` 不能单独使用，必须和 `fixedDelay`、`fixedRate` 或 `cron` 配合，相当于给任务的 “首次执行” 加了一个 “启动缓冲期”。**

![](https://img2024.cnblogs.com/blog/3471133/202510/3471133-20251025231534650-1533294191.png)

