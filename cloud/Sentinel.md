# Sentinel

https://github.com/alibaba/Sentinel/wiki

通过并发线程数进行限制，没有线程切换的损耗

通过响应时间对资源降级

滑动窗口

SphU.entry(resource)





# @SentinelResource 

`@SentinelResource` 用于定义资源，并提供可选的异常处理和 fallback 配置项。 `@SentinelResource` 注解包含以下属性：

- `value`: 资源名称，必需项（不能为空）
- `entryType`: 入口类型，可选项: EntryType.IN和EntryType.OUT（默认为 `EntryType.OUT`）
- `blockHandler `/ `blockHandlerClass`: `blockHandler `对应处理 `BlockException` 的函数名称，可选项。若未配置，则将 `BlockException` 直接抛出。blockHandler 函数访问范围需要是 `public`，返回类型需要与原方法相匹配，参数类型需要和原方法相匹配并且最后加一个额外的参数，类型为 `BlockException`。blockHandler 函数默认需要和原方法在同一个类中。若希望使用其他类的函数，则可以指定 `blockHandlerClass` 为对应的类的 `Class` 对象，注意对应的函数必需为 static 函数，否则无法解析。
- `fallback`: fallback 函数名称，可选项，仅针对降级功能生效（`DegradeException`）。fallback 函数的访问范围需要是 `public`，参数类型和返回类型都需要与原方法相匹配，并且需要和原方法在同一个类中。业务异常不会进入 fallback 逻辑。

