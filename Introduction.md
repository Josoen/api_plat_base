# 项目结构介绍

本项目是一个基于 Spring Boot 和 Spring Cloud 的 API 开放平台。项目采用模块化开发，主要分为以下几个核心模块：

- **yuapi-backend**: 后端主服务，负责核心业务逻辑，如用户管理、接口管理等。
- **yuapi-common**: 通用模块，提供数据模型、工具类等公共组件。
- **yuapi-gateway**: API 网关，负责请求路由、权限校验、流量控制等。
- **yuapi-interface**: 接口模块，提供具体的 API 接口服务。
- **yuapi-client-sdk**: 客户端 SDK，方便开发者调用平台 API。

## 总体项目结构

```
yuapi-backend-master
├── yuapi-backend (主模块)
│   ├── src
│   │   ├── main
│   │   │   ├── java/com/yupi/project
│   │   │   │   ├── annotation      // 自定义注解
│   │   │   │   ├── aop             // 切面，用于日志和权限校验
│   │   │   │   ├── common          // 通用返回类和错误码
│   │   │   │   ├── config          // 项目配置
│   │   │   │   ├── constant        // 常量
│   │   │   │   ├── controller      // 控制器
│   │   │   │   ├── exception       // 异常处理
│   │   │   │   ├── mapper          // MyBatis Mapper 接口
│   │   │   │   ├── model           // 数据模型 (DTO, Entity, VO)
│   │   │   │   ├── provider        // Dubbo 服务提供者
│   │   │   │   └── service         // 业务逻辑服务
│   │   │   └── resources
│   │   │       ├── mapper          // MyBatis Mapper XML
│   │   │       └── application.yml // 配置文件
│   │   └── test                    // 测试代码
│   └── pom.xml                     // Maven 配置文件
├── yuapi-common
│   ├── src/main/java/com/yupi/yuapicommon
│   │   ├── model                   // 通用数据模型
│   │   └── service                 // Dubbo 服务接口
│   └── pom.xml
├── yuapi-gateway
│   ├── src/main/java/com/yupi/yuapigateway
│   │   ├── CustomGlobalFilter.java // 全局过滤器
│   │   └── YuapiGatewayApplication.java // 启动类
│   └── pom.xml
└── yuapi-interface
    ├── src/main/java/com/yupi/yuapiinterface
    │   ├── aop                     // 接口调用切面
    │   ├── controller              // 接口控制器
    │   └── YuapiInterfaceApplication.java // 启动类
    └── pom.xml
└── yuapi-client-sdk
    ├── src/main/java/com/yupi/yuapiclientsdk
    │   ├── client                // 客户端调用类
    │   ├── model                 // 数据模型
    │   └── utils                 // 工具类
    └── pom.xml
```

---

## 各模块详细介绍

### 1. yuapi-backend (主模块)

这是项目的核心后端服务，负责处理主要的业务逻辑。

#### 模块结构

```
yuapi-backend
└── src
    └── main
        ├── java/com/yupi/project
        │   ├── annotation      // @AuthCheck 自定义注解，用于权限校验
        │   ├── aop             // AuthInterceptor 实现权限校验，LogInterceptor 记录请求日志
        │   ├── common          // BaseResponse, ErrorCode 等通用返回结构
        │   ├── config          // CorsConfig 跨域配置, Knife4jConfig API文档配置
        │   ├── constant        // CommonConstant, UserConstant 定义常量
        │   ├── controller      // UserController, InterfaceInfoController 等控制器
        │   ├── exception       // BusinessException 自定义业务异常, GlobalExceptionHandler 全局异常处理
        │   ├── mapper          // UserMapper, InterfaceInfoMapper 等数据访问接口
        │   ├── model           // DTO, Entity, VO 等数据模型
        │   ├── provider        // Dubbo 服务实现
        │   └── service         // UserService, InterfaceInfoService 等业务服务接口及实现
        └── resources
            ├── mapper          // MyBatis 的 XML 映射文件
            └── application.yml // Spring Boot 配置文件
```

#### 模块介绍

- **Annotation**: 定义了 `@AuthCheck` 注解，用于标记需要权限校验的接口方法。
- **AOP**:
    - `AuthInterceptor`: 权限校验切面，拦截带有 `@AuthCheck` 注解的方法，进行用户权限验证。
    - `LogInterceptor`: 日志切面，记录接口请求的日志信息，方便问题排查。
- **Common**: 包含通用的返回对象 `BaseResponse`、错误码 `ErrorCode` 和分页请求 `PageRequest` 等，用于规范接口的返回格式。
- **Config**: 存放项目的配置类，如跨域配置 `CorsConfig` 和 API 文档配置 `Knife4jConfig`。
- **Constant**: 定义了项目中使用的常量，如排序字段、用户角色等。
- **Controller**: 接收前端请求，调用 `Service` 层处理业务逻辑，并返回响应。
- **Exception**: 统一的异常处理机制，`GlobalExceptionHandler` 捕获全局异常并返回统一格式的错误信息。
- **Mapper**: MyBatis 的 Mapper 接口，用于与数据库进行交互。
- **Model**:
    - `dto`: 数据传输对象，用于接收前端请求参数。
    - `entity`: 数据库实体类。
    - `vo`: 视图对象，用于向前端返回数据。
- **Provider**: Dubbo 服务的具体实现，供其他模块远程调用。
- **Service**: 业务逻辑的核心，处理复杂的业务流程，调用 `Mapper` 层操作数据库。

---

### 2. yuapi-common

通用模块，包含了项目中多个模块共享的类。

#### 模块结构

```
yuapi-common
└── src
    └── main
        └── java/com/yupi/yuapicommon
            ├── model       // 共享的数据模型 (Entity, VO)
            └── service     // Dubbo 服务接口定义
```

#### 模块介绍

- **Model**: 定义了跨模块共享的数据实体，如 `User`, `InterfaceInfo` 等，避免了在不同模块中重复定义。
- **Service**: 定义了 Dubbo 服务的接口，如 `InnerUserService`, `InnerInterfaceInfoService` 等，供其他模块（如网关）进行 RPC 调用。

---

### 3. yuapi-gateway

API 网关服务，是所有请求的入口。

#### 模块结构

```
yuapi-gateway
└── src
    └── main
        └── java/com/yupi/yuapigateway
            ├── CustomGlobalFilter.java      // 自定义全局过滤器
            └── YuapiGatewayApplication.java // 网关启动类
```

#### 模块介绍

- **CustomGlobalFilter**: 自定义的全局过滤器，在请求被路由到具体服务之前执行。它负责：
    - **统一鉴权**: 校验请求的合法性，如检查 ak/sk。
    - **请求日志**: 记录所有进入网关的请求信息。
    - **接口调用统计**: 统计接口的调用次数。
- **YuapiGatewayApplication**: Spring Cloud Gateway 的启动类。

---

### 4. yuapi-interface

提供具体的 API 接口服务。

#### 模块结构

```
yuapi-interface
└── src
    └── main
        └── java/com/yupi/yuapiinterface
            ├── aop             // InvokeCountAOP，用于统计接口调用次数
            ├── controller      // NameController，提供具体的 API 接口
            └── YuapiInterfaceApplication.java // 接口服务启动类
```

#### 模块介绍

- **AOP**:
    - `InvokeCountAOP`: 通过切面统计每个接口的调用次数，可以用于后续的计费或分析。
- **Controller**:
    - `NameController`: 提供一个简单的示例接口，接收参数并返回结果。实际项目中可以扩展为多个提供不同功能的 Controller。

---

### 5. yuapi-client-sdk

客户端 SDK，封装了与后端 API 的交互逻辑，方便第三方开发者快速集成和调用。

#### 模块结构

```
yuapi-client-sdk
└── src
    └── main
        └── java/com/yupi/yuapiclientsdk
            ├── client      // YuApiClient 客户端，封装了 API 请求和签名逻辑
            ├── model       // 包含了请求和响应的数据模型
            └── utils       // SignUtils 签名工具类
```

#### 模块介绍

- **client**:
    - `YuApiClient`: 核心客户端类，开发者通过该类调用平台的各种 API。它封装了 HTTP 请求的发送、参数的封装以及请求签名的生成。
- **model**: 定义了调用 API 时需要用到的各种请求参数对象和响应结果对象。
- **utils**:
    - `SignUtils`: 签名工具类，用于生成符合平台安全规范的请求签名，保证 API 调用的安全性。
