# @JsonFormat和@DateTimeFormat.md
@DateTimeFormat:

主要用于请求的方法参数里的日期转换

```java
@PostMapping("/param")
public JsonResult param(@RequestParam @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss") Date date) {
    System.out.println("--------------------" + new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(date));
    return JsonResult.success();
}
```
@JsonFormat:

1. 用于RequestBody转换为对象中的日期格式转换
2. 返回对象转换为json时日期转换为指定格式字符串

```java
@PostMapping("/in")
public JsonResult in(@RequestBody TestDTO testDTO) {
    System.out.println("--------------------" + new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(testDTO.getDate()));
    return JsonResult.success();
}

@PostMapping("/out")
public TestDTO out() {
    TestDTO testDTO = new TestDTO();
    testDTO.setDate(new Date(System.currentTimeMillis()));
    return testDTO;
}
```
