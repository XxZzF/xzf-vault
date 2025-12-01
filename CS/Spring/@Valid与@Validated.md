
@Validated 是Spring框架的注解，支持JSR-303规范和Spring的自定义验证注解。支持**分组校验**

@Valid 是标准的Java Bean Validation API的一部分，通常与JSR-303或JSR-349标准一起使用。支持**嵌套校验**。

需要确保在类路径中包含Bean Validation API的实现（例如Hibernate Validator）。



如果方法的入参是平铺的参数，或者接口的传参方式是request param，那么需要在类上加@Validated来开启校验。

```java
@Validated
@RestController
public class Controller {
    @GetMapping("/removeRawPaper")
    public void remove(@RequestParam @NotBlank(message = "编号不能为空") String code)...
}
```
```java
@Validated(Default.class)
public interface HelloService {
    Object hello(@NotNull @Min(10) Integer id, @NotNull String name);
}
public class HelloServiceImpl implements HelloService {
    @Override
    public Object hello(Integer id,String name){
        return null;
    }
}
```

### 4. 全局异常处理 (最佳实践)

当校验失败时，Spring 会抛出异常。为了给前端返回友好的 JSON 格式错误，建议使用全局异常处理器。

- **`MethodArgumentNotValidException`**: 处理 `@RequestBody` 对象校验失败。
    
- **`ConstraintViolationException`**: 处理单参数 (`@RequestParam`/`@PathVariable`) 校验失败。
    



``` Java
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;
import jakarta.validation.ConstraintViolationException;
import java.util.HashMap;
import java.util.Map;

@RestControllerAdvice
public class GlobalExceptionHandler {

    // 处理对象校验失败 (@RequestBody)
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public Map<String, String> handleValidationExceptions(MethodArgumentNotValidException ex) {
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getFieldErrors().forEach(error -> 
            errors.put(error.getField(), error.getDefaultMessage())
        );
        return errors;
    }

    // 处理单参数校验失败 (@RequestParam / @PathVariable)
    @ExceptionHandler(ConstraintViolationException.class)
    public Map<String, String> handleConstraintViolationException(ConstraintViolationException ex) {
        Map<String, String> errors = new HashMap<>();
        ex.getConstraintViolations().forEach(violation -> {
            // 获取参数名
            String path = violation.getPropertyPath().toString();
            String field = path.substring(path.lastIndexOf('.') + 1);
            errors.put(field, violation.getMessage());
        });
        return errors;
    }
}
```

原理：[https://blog.csdn.net/qq\_22845447/article/details/105107882](https://blog.csdn.net/qq_22845447/article/details/105107882)