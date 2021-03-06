# spring boot_swagger_demo

# 依赖

    <dependency>
                <groupId>io.springfox</groupId>
                <artifactId>springfox-swagger2</artifactId>
                <version>2.2.2</version>
            </dependency>
            <dependency>
                <groupId>io.springfox</groupId>
                <artifactId>springfox-swagger-ui</artifactId>
                <version>2.2.2</version>
    </dependency>

# Swagger2.java：

      @Configuration
      @EnableSwagger2
      public class Swagger2 {
      
          @Bean
          public Docket createRestApi(){
              return new Docket(DocumentationType.SWAGGER_2)
                      .apiInfo(apiInfo())
                      .select()
                      .apis(RequestHandlerSelectors.basePackage("org.wingstudio.swagger.controller"))
                      //basePackage要精确到controller包下
                      .paths(PathSelectors.any())
                      .build();
          }
      
          private ApiInfo apiInfo(){
              return new ApiInfo(
                      "Swagger Test Title",
                      "Swagger Test Desc",
                      "1.0.0",
                      "http:localhost",
                      "1280334378@qq.com",
                      "",
                      ""
              );
          }
      
      }


# Example Controller

    @RestController
    @RequestMapping("/users")
    public class UserController {
    
        static Map<Long, User> users = Collections.synchronizedMap(new HashMap<>());
    
        @ApiOperation(value = "获取用户列表", notes = "notes")
        @GetMapping
        public List<User> getUserList() {
            ArrayList<User> users = new ArrayList<>(UserController.users.values());
            return users;
        }
    
        @ApiOperation(value = "创建用户", notes = "根据User对象创建用户")
        @ApiImplicitParam(name = "user", value = "用户详细实体user", required = true, dataType = "User")
        @PostMapping
        public String postUser(@RequestBody User user) {
            users.put(user.getId(), user);
            return "success";
        }
    
        @ApiOperation(value = "更新用户信息")
        @ApiImplicitParams({
                @ApiImplicitParam(name = "id", value = "userId", required = true, dataType = "Long"),
                @ApiImplicitParam(name = "user", value = "user", required = true, dataType = "User")
        })
        @PutMapping("/{id}")
        public String putUser(@PathVariable("id") Long id, @RequestBody User user) {
            User u = users.get(id);
            u.setName(user.getName());
            users.put(id, u);
            return "success";
        }
    
        @ApiOperation(value = "删除用户", notes = "根据url的id来指定删除对象")
        @ApiImplicitParam(name = "id", value = "用户ID", required = true, dataType = "Long")
        @RequestMapping(value = "/{id}", method = RequestMethod.DELETE)
        public String deleteUser(@PathVariable Long id) {
            users.remove(id);
            return "success";
        }
    }
    
启动项目后访问 /swagger-ui.html