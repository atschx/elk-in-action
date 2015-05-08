# Tips

* 了解你所要收集的数据，合理规划（多思考）
* 删除信息冗余的字段(eg:`grok`插件中的`messages`)
* 推荐基于`broker`模式进行中心化的日志收集

java异常信息
============

````
2015-05-07 19:40:48,103│ß│uplus.error│ß│main│ß│ERROR│ß│websock│ß│cn.youja.uplus.logging.UplusLoggerTest$TalkShow.run(UplusLoggerTest.java:43)│ß│除数不能为0.│ß│
java.lang.ArithmeticException: / by zero
        at cn.youja.uplus.logging.UplusLoggerTest.ah(UplusLoggerTest.java:29)
        at cn.youja.uplus.logging.UplusLoggerTest.test(UplusLoggerTest.java:20)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:39)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25)
        at java.lang.reflect.Method.invoke(Method.java:597)
        at junit.framework.TestCase.runTest(TestCase.java:164)
        at junit.framework.TestCase.runBare(TestCase.java:130)
        at junit.framework.TestResult$1.protect(TestResult.java:106)
        at junit.framework.TestResult.runProtected(TestResult.java:124)
        at junit.framework.TestResult.run(TestResult.java:109)
        at junit.framework.TestCase.run(TestCase.java:120)
        at junit.framework.TestSuite.runTest(TestSuite.java:230)
        at junit.framework.TestSuite.run(TestSuite.java:225)
````
基于filter插件中的`multiline`和`grok`进行数据提取
```
input {stdin {}}
filter {
 multiline {
   pattern => "^%{TIMESTAMP_ISO8601}"
   negate => true
   what => "previous"
 }
 grok{
  match => {"message" =>["%{TIMESTAMP_ISO8601:rec_time}│ß│%{NOTSPACE:logger}│ß│%{NOTSPACE:thread_name}│ß│%{NOTSPACE:loglevel}│ß│%{NOTSPACE:module}│ß│%{NOTSPACE:class_method}│ß│%{DATA:msg_info}│ß│\n%{GREEDYDATA:first_stack}"]}
   remove_field => [ "message" ]
 }
}
output {
  stdout { codec => rubydebug }
}
````

> 日志收集起来的目的是为了可视化的统计程序报错，用于持续改善。真正的定位问题和解决问题，属于另一个范畴。`message`字段是`multiline`插件的产物，成功`grok`之后，建议移除message字段。

````
{
         "message" => "2015-05-07 19:40:48,103│ß│uplus.error│ß│main│ß│ERROR│ß│websock│ß│cn.youja.uplus.logging.UplusLoggerTest$TalkShow.run(UplusLoggerTest.java:43)│ß│除数不能为0.│ß│\njava.lang.ArithmeticException: / by zero\n\tat cn.youja.uplus.logging.UplusLoggerTest.ah(UplusLoggerTest.java:29)\n\tat cn.youja.uplus.logging.UplusLoggerTest.test(UplusLoggerTest.java:20)\n\tat sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)\n\tat sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:39)\n\tat sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25)\n\tat java.lang.reflect.Method.invoke(Method.java:597)\n\tat junit.framework.TestCase.runTest(TestCase.java:164)\n\tat junit.framework.TestCase.runBare(TestCase.java:130)\n\tat junit.framework.TestResult$1.protect(TestResult.java:106)\n\tat junit.framework.TestResult.runProtected(TestResult.java:124)\n\tat junit.framework.TestResult.run(TestResult.java:109)\n\tat junit.framework.TestCase.run(TestCase.java:120)\n\tat junit.framework.TestSuite.runTest(TestSuite.java:230)\n\tat junit.framework.TestSuite.run(TestSuite.java:225)\n\tat org.eclipse.jdt.internal.junit.runner.junit3.JUnit3TestReference.run(JUnit3TestReference.java:131)\n\tat org.eclipse.jdt.internal.junit.runner.TestExecution.run(TestExecution.java:38)\n\tat org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.runTests(RemoteTestRunner.java:459)\n\tat org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.runTests(RemoteTestRunner.java:675)\n\tat org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.run(RemoteTestRunner.java:382)\n\tat org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.main(RemoteTestRunner.java:192)",
        "@version" => "1",
      "@timestamp" => "2015-05-08T15:39:29.830Z",
            "host" => "vm_50_76.youja.cn",
            "tags" => [
        [0] "multiline"
    ],
        "rec_time" => "2015-05-07 19:40:48,103",
          "logger" => "uplus.error",
     "thread_name" => "main",
        "loglevel" => "ERROR",
          "module" => "websock",
    "class_method" => "cn.youja.uplus.logging.UplusLoggerTest$TalkShow.run(UplusLoggerTest.java:43)",
        "msg_info" => "除数不能为0.",
           "stack" => "java.lang.ArithmeticException: / by zero"
}
````