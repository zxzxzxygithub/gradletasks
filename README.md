# gradletasks

``` 
//Copy类型
task makeJar(type: Copy) {
    //删除存在的
    delete 'build/libs/mysdk.jar'
    //设置拷贝的文件
    from('build/intermediates/bundles/release/')
    //打进jar包后的文件目录
    into('build/libs/')
    //将classes.jar放入build/libs/目录下
    //include ,exclude参数来设置过滤
    //（我们只关心classes.jar这个文件）
    include('classes.jar')
    //重命名
    rename ('classes.jar', 'mysdk.jar')
}

makeJar.dependsOn(build)
//在终端执行生成JAR包
// gradlew makeJar
```
```
删除不需要的任务
tasks.whenTaskAdded { task ->
        if (task.name.equals("lint")
                ||task.name.equals("clean")
                ||task.name.equals("lintVitalRelease")
                ||task.name.equals("testDebugUnitTest")
                ||task.name.equals("testReleaseUnitTest")
        ) {
            System.out.println("dotask")
            task.enabled = false
        }
    }
```
```
//监听gradle任务以及耗时
class TimingsListener implements TaskExecutionListener, BuildListener {
    private Clock clock
    private timings = []

    @Override
    void beforeExecute(Task task) {
        clock = new org.gradle.util.Clock()
    }

    @Override
    void afterExecute(Task task, TaskState taskState) {
        def ms = clock.timeInMs
        timings.add([ms, task.path])
        task.project.logger.warn "${task.path} took ${ms}ms"
    }

    @Override
    void buildFinished(BuildResult result) {
        println "Task timings:"
        for (timing in timings) {
            if (timing[0] >= 50) {
                printf "%7sms  %s\n", timing
            }
        }
    }

    @Override
    void buildStarted(Gradle gradle) {}

    @Override
    void projectsEvaluated(Gradle gradle) {}

    @Override
    void projectsLoaded(Gradle gradle) {}

    @Override
    void settingsEvaluated(Settings settings) {}
}

gradle.addListener new TimingsListener()

```
