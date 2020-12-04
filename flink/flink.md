# 异常

``` xml
1.could not find implicit value for evidence parameter of type TypeInformation[String]
解决方法
import org.apache.flink.streaming.api.scala. _
```

```xml
2.IllegalStateException: No operators defined in streaming topology. Cannot execute.
解决方法
print()加上这个行动算子，流拓扑才会工作
```

