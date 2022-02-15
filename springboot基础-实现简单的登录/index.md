# Springboot基础 实现简单的登录


yml的数据绑定需要使用@ConfigurationProperties注解进行绑定，然后在使用的时候可以使用@Autowired自动注入。

使用Interceptor可以实现登录鉴权功能，首先写一个LoginInterceptor类实现HandlerInterceptor类，实现preHandler方法，在此方法中进行鉴权操作，在写一个LoginConfiguration类实现WebMvcConfigure类，实现addInterceptor方法，在这个方法中配置拦截规则，给这个类添加一个@configuration注解，自动注入配置，一个简单的鉴权拦截功能就完成了，后面再写一个controller进行登录操作，自此，一个简单的登录鉴权的功能就完成了。

下面是代码

config/LoginConfiguration.java

```java
@Configuration
public class LoginConfiguration implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginInterceptor()).addPathPatterns("/");
    }
}

```

config/LoginInterceptor.java

```java
public class LoginInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

        User user = (User) request.getSession().getAttribute("USER");
        if (user == null){
            response.sendError(403,"权限不足");
//            response.setStatus(403);

            return false;
        }
        return true;
    }
}


```

controller/LoginController.java

```java
@RestController
public class LoginController {
    @Autowired
    private User user;

    @PostMapping("/login")
    public ResBean login(HttpServletRequest req, @RequestBody LoginParam loginParam){

        if(loginParam.getUsername().equals(user.getUsername())&&loginParam.getPassword().equals(user.getPassword())){
            req.getSession().setAttribute("USER",new User()
                    .setUsername(loginParam.getUsername())
                    .setPassword(loginParam.getPassword())
                    .setPermission(1)
                    .setDesc("普通用户")
            );
            return new ResBean("登录成功",200,null);
        }else{
            return new ResBean("登录失败，用户名或者密码错误",200,null);
        }
    }
}
```

controller/HelloController.java

```java
@RestController
public class HelloController {
    @GetMapping("/")
    public String HelloWorld(){
        return "Hello World";
    }
}
```

pojo/LoginParam.java

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Accessors(chain = true)
public class LoginParam {
    private String username;
    private String password;
}

```



此处使用了Lombok插件

pojo/User.java

```java
@Component
@Data
@AllArgsConstructor
@NoArgsConstructor
@Accessors(chain = true)
@ConfigurationProperties(prefix = "user1")
public class User {
    private String username;
    private String password;
    private int permission;
    private String desc;
}

```

utils/ResBean.java

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@Accessors(chain = true)
public class ResBean {
    private String msg;
    private int code;
    private Object obj;
}
```

application.yml

```yaml
user1:
    username: chenyi
    password: pwd123456
    permission: 1
    desc: 普通用户
```


