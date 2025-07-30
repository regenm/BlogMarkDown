---
title: SpringBoot+vue+mysql项目实训
date: 2024-07-03 09:57:04
tags: [java, html,javascript,spring, springboot,vue,mysql, 前端, 后端, 笔记]
categories: [软件技术]
---



## 常见的项目类型以及优缺点和应用范围

1. **Spring Boot + Vue + MySQL**

    #### 优点

    1. **强大的后端支持**：
        - **Spring Boot** 提供了成熟的后端框架和丰富的生态系统，包括依赖注入、数据访问、事务管理等功能，适合构建复杂的企业级应用。
    2. **灵活的前端开发**：
        - **Vue** 是一个轻量级的前端框架，易于学习和上手，具有响应式数据绑定和组件化开发的优势，适合构建动态且高效的用户界面。
    3. **可靠的数据库**：
        - **MySQL** 是一个广泛使用的开源关系型数据库管理系统，具有稳定性高、性能优越、支持大规模并发访问等特点，适合各种规模的应用。
    4. **全栈开发一体化**：
        - Spring Boot + Vue 的组合使得开发人员可以使用相似的语言和工具栈进行全栈开发，提高了开发效率和团队协作。
    5. **社区支持和文档丰富**：
        - Spring Boot 和 Vue 都拥有庞大的社区和丰富的文档资源，开发过程中能够快速获取到解决方案和技术支持。

    #### 缺点

    1. **复杂的配置和学习曲线**：
        - Spring Boot 的配置和依赖管理相对复杂，需要一定的学习成本和经验来优化和调整项目。
        - Vue 虽然易于上手，但在复杂的单页应用中，需要深入理解其组件通信和状态管理等高级特性。
    2. **前后端分离的管理挑战**：
        - 前后端分离架构需要额外的工作来管理跨团队和跨技术栈的协作，需要定义清晰的接口和数据传输方案。
    3. **数据库扩展性限制**：
        - 虽然 MySQL 适合大多数应用场景，但在需要处理非结构化数据或需要高度扩展性的大规模应用中，可能需要考虑其他数据库解决方案。

    #### 应用范围

    - **企业级应用**：适用于需要高性能、高可扩展性和稳定性的企业级应用开发，如ERP系统、电子商务平台等。
    - **信息管理系统**：能够处理大量结构化数据的信息管理系统，如客户关系管理（CRM）、人力资源管理（HRM）等。
    - **数据驱动型应用**：适用于需要大量数据处理和复杂查询的应用，如数据分析平台、报表生成系统等。
    - **中小型项目**：对于中小型项目来说，Spring Boot + Vue 提供了良好的开发体验和成本效益，能够快速构建并部署应用。

2. Spring Boot + Angular + PostgreSQL

    #### 优点

    - **强大的后端框架**：Spring Boot 提供了成熟且强大的企业级开发支持，尤其适用于大型项目。
    - **全面的前端框架**：Angular 提供了完整的解决方案，适合构建复杂的单页应用，具有双向数据绑定和依赖注入等特性。
    - **强大的数据库**：PostgreSQL 是一个功能强大的开源关系型数据库，支持复杂的查询和大数据处理。

    #### 缺点

    - **学习曲线**：Angular 相比其他前端框架更为复杂，学习和掌握需要较长时间。
    - **配置复杂**：Spring Boot 和 PostgreSQL 的配置和优化需要一定的经验和技术。

    #### 应用范围

    - 适用于需要高性能和高可扩展性的企业级应用。
    - 适用于需要复杂业务逻辑和数据处理的后台系统。
    - 适用于需要构建复杂单页应用（SPA）的项目。

3. Node.js + React + MongoDB

    #### 优点

    - **高性能的后端**：Node.js 基于事件驱动和非阻塞I/O模型，适合高并发和实时应用。
    - **灵活的前端框架**：React 具有组件化开发模式和虚拟DOM，适合构建复杂和高性能的用户界面。
    - **灵活的数据库**：MongoDB 是一种NoSQL数据库，适合处理大规模数据和快速开发。

    #### 缺点

    - **回调地狱**：Node.js 的异步编程模型可能导致“回调地狱”，需要使用Promise或async/await进行优化。
    - **数据一致性问题**：MongoDB 在某些情况下可能会遇到数据一致性问题，需要注意数据的管理和维护。

    #### 应用范围

    - 适用于需要高并发和实时性要求的应用，如聊天系统和实时通知。
    - 适用于需要快速迭代和开发的Web应用和移动应用后端。
    - 适用于处理大规模数据和非结构化数据的应用。

4. Django + Angular + PostgreSQL

    #### 优点

    - **成熟的后端框架**：Django 提供了快速开发和高效管理的优势，内置了管理后台、ORM等功能，适合快速构建Web应用。
    - **全面的前端框架**：Angular 提供了完整的解决方案，适合构建复杂的单页应用，具有双向数据绑定和依赖注入等特性。
    - **强大的数据库**：PostgreSQL 是一个功能强大的开源关系型数据库，支持复杂的查询和大数据处理。

    #### 缺点

    - **学习曲线**：Angular 相比其他前端框架更为复杂，学习和掌握需要较长时间。
    - **配置复杂**：Django 的配置和优化需要一定的经验和技术。

    #### 应用范围

    - 适用于需要快速开发和部署的Web应用。
    - 适用于需要复杂业务逻辑和数据处理的后台系统。
    - 适用于需要构建复杂单页应用（SPA）的项目。

5. Flask + Vue + SQLite

    #### 优点

    - **轻量级后端框架**：Flask 是一个轻量级的Web框架，灵活且易于上手，适合小型项目和原型开发。
    - **灵活的前端框架**：Vue.js 易于上手，具有灵活的组件化开发模式，适合快速开发和构建响应式用户界面。
    - **轻量级数据库**：SQLite 是一个嵌入式数据库，适合小型应用和测试环境。

    #### 缺点

    - **功能有限**：Flask 相比Django等框架功能较少，适合简单和中小型项目。
    - **数据库限制**：SQLite 不适合大规模和高并发的应用，性能和功能相对有限。

    #### 应用范围

    - 适用于小型Web应用和原型开发。
    - 适用于需要快速迭代和开发的项目。
    - 适用于资源有限的应用和测试环境。

> ### 1. Angular
>
> **Angular** 是由Google开发和维护的一款前端框架，用于构建单页应用（SPA）和动态Web应用。它采用了TypeScript语言进行开发，提供了强大的组件化架构、数据绑定、依赖注入等功能，帮助开发人员更高效地构建复杂的用户界面和交互逻辑。
>
> ### 2. PostgreSQL
>
> **PostgreSQL** 是一个强大的开源关系型数据库管理系统（RDBMS），具有高度的可扩展性、可靠性和丰富的功能集。它支持复杂的SQL查询、事务处理、触发器、视图等数据库特性，适合于处理大规模数据和复杂的数据操作。
>
> ### 3. Node.js
>
> **Node.js** 是一个基于Chrome V8引擎的JavaScript运行时环境，用于构建快速、可扩展的网络应用。Node.js采用事件驱动、非阻塞I/O模型，适合于处理大量并发请求和实时应用，如Web服务器、API服务器等。
>
> ### 4. React
>
> **React** 是由Facebook开发和维护的一款用于构建用户界面的JavaScript库。它采用组件化开发模式和虚拟DOM技术，使得开发人员能够高效地构建复杂的用户界面，实现数据与视图的高效同步更新。
>
> ### 5. MongoDB
>
> **MongoDB** 是一个基于分布式文件存储的开源NoSQL数据库，采用文档存储方式，适合处理非结构化和大数据量的数据。它具有高性能、高可用性和易扩展等特点，常用于Web应用和大数据处理中。
>
> ### 6. Flask
>
> **Flask** 是一个轻量级的Python Web框架，设计简单且易于扩展，适合快速开发原型和小型Web应用。Flask提供了基本的路由、模板引擎和WSGI支持，同时允许开发者根据需求选择扩展和库，以便构建特定需求的应用。
>
> ### 7. Django
>
> **Django** 是一个由Python编写的开源Web应用框架，设计用于快速开发和复杂的Web应用。Django提供了完整的开发工具集，包括ORM、管理界面、表单处理、认证系统等，使得开发人员能够快速构建安全、可维护的Web应用。
>
> ### 应用场景
>
> - **Angular** 和 **React** 适合构建复杂的单页应用和动态用户界面，具有较高的交互性和可扩展性。
> - **PostgreSQL** 和 **MongoDB** 分别适合处理关系型和非关系型数据存储，根据数据模型和应用需求选择合适的数据库系统。
> - **Node.js** 和 **Django** / **Flask** 可以用于构建服务器端应用，提供API服务或渲染动态内容，具有不同的开发风格和适用场景。

## 快速搭建项目的选择

​	对Java和Spring技术栈比较熟悉，并且项目需要稳定性和扩展性，Spring Boot + Vue + MySQL 是一个很好的选择。如果项目需求相对简单且对响应速度要求较高，Flask + Vue + SQLite 可能更适合快速完成项目。

# SpringBoot( 后端部分 )

## 框架介绍

`src/main/java/com/example/project/Application.java`：Spring Boot 启动类。



`src/main/java/com/example/project/controller`：控制器层，处理 HTTP 请求。

​	是一个用于处理用户请求并返回响应的关键组件。

> **处理HTTP请求**：
>
> - 控制器负责接收来自客户端的HTTP请求，通过映射的URL路径（如`/home`）或者其他标识符来确定具体要调用的处理方法。
>
> **处理业务逻辑**：
>
> - 控制器包含了应用程序的业务逻辑，它可以调用Service层或者其他组件来完成数据处理、业务计算等操作。
>
> **返回响应**：
>
> - 一旦业务逻辑处理完成，控制器负责将结果打包成响应对象（如JSON数据、HTML页面等）并返回给客户端。



`src/main/java/com/example/project/service`：服务层，包含业务逻辑。

用于实现业务逻辑的组件，它通常位于应用的服务层，负责处理业务逻辑、数据处理和协调不同的数据访问对象（DAO）来完成特定的业务需求。

> **实现业务逻辑**：
>
> - Service层负责实现应用程序的业务逻辑，例如计算、数据处理、数据校验等。它包含了应用程序的核心功能，与具体的数据存取逻辑（如数据库操作）分离。
>
> **事务管理**：
>
> - Service层通常是事务的边界，负责管理事务的开始和结束。在Spring中，可以通过注解（如 `@Transactional`）来声明事务边界，确保业务方法执行的一致性和完整性。
>
> **协调DAO**：
>
> - Service层通常会调用一个或多个DAO（数据访问对象）来访问数据存储（如数据库）。它负责协调多个DAO的操作，将数据访问的细节隐藏在业务逻辑之后。
>
> **业务逻辑的组织**：
>
> - Service层帮助组织应用程序的业务逻辑，使得控制器（Controller）层可以专注于处理用户请求和响应，而不必处理复杂的业务逻辑。
>
> **解耦和重用**：
>
> - 将业务逻辑封装在Service层中有助于解耦，使得不同的模块可以独立开发和测试。Service层中的方法也可以被多个控制器复用，提高了代码的重用性。

`src/main/java/com/example/project/dao`：数据访问层，包含与数据库交互的代码。

在传统的Java EE 或者 Spring 中，通常将数据访问层（DAO，Data Access Object）的实现与配置与数据库交互的语句分开。在这种情况下，DAO 层的实现可能是直接使用 SQL 语句（通常存储在 XML 文件中）与数据库进行交互。在 MyBatis 框架中，这些 SQL 语句存储在 XML 文件中，并且可以使用一组规则来映射 Java 对象和数据库记录





`src/main/java/com/example/project/entity`：数据模型，包含实体类。

指代表应用程序中的数据模型或持久化数据的类

> **数据模型定义**：
>
> - Entity 在应用程序中用于定义数据模型的结构。它们通常反映了数据库中表的结构，包括表的字段、关联关系等。
>
> **持久化**：
>
> - Entity 对象与数据库中的表（或文档、集合等）进行映射，使得应用程序能够方便地读取和存储数据。通过持久化操作，Entity 可以保存到数据库中，并能够从数据库中检索数据。
>
> **使用注解标识**：
>
> - 在Spring中，可以使用注解（如 `@Entity`、`@Table`、`@Id`、`@Column` 等）来标识一个类为Entity，并指定与之关联的数据库表、主键等信息。
>
> **ORM框架支持**：
>
> - Spring框架通常与ORM（对象关系映射）框架（如 Hibernate、Spring Data JPA 等）集成使用。ORM框架负责将Entity对象与数据库记录进行映射，简化了数据访问和操作的过程。
>
> **业务逻辑的基础**：
>
> - Entity对象作为应用程序中的基本数据单位，它们通常与业务逻辑紧密相关。通过操作Entity对象，可以实现应用程序的各种业务逻辑需求。

`src/main/resources/application.properties`：配置文件，包含应用程序的配置项。

例如：

```properties
spring.application.name=his
server.servlet.contextPath=/his
server.port=8082

spring.datasource.driverClassName=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://192.168.98.130:3306/his?serverTimezone=GMT%2B8&useSSL=false&allowPublicKeyRetrieval=true
spring.datasource.username=root
spring.datasource.password=123456

mybatis.typeAliasesPackage=com.sample.his.entity
mybatis.mapperLocations=classpath:mapper/*.xml
mybatis.configuration.logImpl=org.apache.ibatis.logging.stdout.StdOutImpl

logging.level.com.sample.his=debug

#spring.resources.staticLocations=file:C:/temp/
spring.servlet.multipart.maxFileSize=9MB
spring.servlet.multipart.maxRequestSize=18MB
```







# VUE（ 前端部分 ）

## 框架介绍

### 特点：

**渐进式**：

- Vue.js 被称为渐进式框架，可以逐步应用到项目中。你可以只将 Vue.js 用作某个页面的一部分，也可以完整地构建整个单页面应用。

**响应式数据绑定**：

- Vue.js 提供了简单而强大的响应式数据绑定系统。通过使用指令（Directives）和双向数据绑定机制，可以使页面上的数据和DOM保持同步。

**组件化开发**：

- Vue.js 支持组件化开发，将页面划分为独立的、可复用的组件。每个组件都有自己的模板、逻辑和样式，可以相互嵌套和组合，使得代码更加模块化和可维护。

**轻量和高效**：

- Vue.js 的核心库很轻量，文件体积小，加载速度快。同时，它的性能优化也很好，对复杂页面和大型应用也能够提供良好的性能表现。

**生态系统丰富**：

- Vue.js 拥有一个活跃的生态系统，包括官方维护的路由器（Vue Router）、状态管理库（Vuex）、测试工具等，以及大量的第三方库和组件，能够满足各种复杂应用的需求。

### 文件树

```csharp
my-vue-app/
├── public/               # 公共资源目录
│   ├── index.html        # 入口 HTML 文件
│   └── ...
├── src/                  # 源代码目录
│   ├── assets/           # 静态资源文件夹（如图片、字体等）
│   ├── components/       # 组件文件夹
│   │   ├── HelloWorld.vue    # 示例组件
│   │   └── ...
│   ├── views/            # 视图组件文件夹
│   │   ├── Home.vue      # 示例视图组件
│   │   └── ...
│   ├── App.vue           # 根组件 所有组件的父级容器。
│   └── main.js           # 主入口文件
├── node_modules/         # npm依赖模块
├── package.json          # 项目配置文件
└── ...
```



## 视图与组件

### 视图（View）

视图是用户界面的一部分，通常指的是用户直接看到和交互的页面或页面的一部分。在Vue.js中，视图可以是一个单独的组件，也可以是多个组件的组合。

- 特点

    ：

    - 视图通常对应应用程序中的一个路由，例如在单页面应用（SPA）中，不同的视图对应不同的URL路径。
    - 视图可以包含多个组件，通过组合不同的组件来构建复杂的用户界面。
    - 视图负责组织和展示数据，响应用户的操作，并与用户交互。

### 组件（Component）

组件是Vue.js中可复用的、独立的UI单元，它封装了特定的功能和样式，并可以在应用中多次使用。

- 特点

    ：

    - 组件是Vue.js应用的基本构建块，它们可以包含自己的模板、脚本和样式，通常以 `.vue` 文件的形式组织。
    - 组件具有自己的状态和行为，可以接收数据作为输入，并且可以通过事件来与父组件或其他组件通信。
    - 组件之间可以相互嵌套和组合，形成复杂的界面结构，同时保持代码的模块化和可维护性。

在Vue.js应用中，通常一个视图对应一个或多个组件。视图负责页面的结构和布局，通过引入和组合多个组件来构建完整的页面。





# VUE和后端的连接





1. **HTTP请求**：

    - Vue.js通过内置的`axios`、`fetch`或者`Vue Resource`等HTTP库，向后端发送HTTP请求。这些请求可以是GET、POST、PUT或DELETE等，用来获取数据、提交表单或执行其他操作。

2. **异步数据获取**：

    - 在Vue组件中，使用`axios`或其他HTTP库，发起异步请求获取数据。例如，在`mounted`生命周期钩子中发送请求，或者在点击事件中处理。

    ```javascript
    import axios from 'axios';
    
    export default {
        data() {
            return {
                users: []
            };
        },
        mounted() {
            axios.get('/api/users')
                .then(response => {
                    this.users = response.data;
                })
                .catch(error => {
                    console.error('Error fetching users', error);
                });
        }
    };
    ```

3. **处理响应**：

    - 前端接收后端返回的JSON数据（或其他格式），并在页面上展示或进一步处理。可以在Vue组件中通过数据绑定、列表渲染等技术，将数据动态显示在页面上。

### 后端（Spring Boot）

1. **RESTful API**：

    - Spring Boot通过Controller类来处理前端的请求，返回JSON或其他格式的数据。通常使用`@RestController`注解标记Controller类，并使用`@RequestMapping`或其他注解映射URL路径。

    ```java
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.web.bind.annotation.*;
    import java.util.List;
    
    @RestController
    @RequestMapping("/api")
    public class UserController {
    
        @Autowired
        private UserRepository userRepository;
    
        @GetMapping("/users")
        public List<User> getAllUsers() {
            return userRepository.findAll();
        }
    
        @PostMapping("/users")
        public User createUser(@RequestBody User user) {
            return userRepository.save(user);
        }
    
        // 其他操作如更新和删除等...
    }
    ```

2. **数据库交互**：

    - 使用Spring Boot的数据访问技术（如Spring Data JPA），通过Repository类访问MySQL数据库。Repository类提供了CRUD操作的方法，简化了数据访问层的开发。

    ```java
    import org.springframework.data.jpa.repository.JpaRepository;
    import org.springframework.stereotype.Repository;
    
    @Repository
    public interface UserRepository extends JpaRepository<User, Long> {
        // 可以定义自定义的查询方法，Spring Data JPA 会根据方法名自动生成查询语句
    }
    ```

3. **处理请求和响应**：

    - 后端接收前端的请求，处理业务逻辑并访问数据库，将查询结果或操作结果封装为JSON格式返回给前端。Spring Boot提供了强大的注解和类库来简化RESTful API的开发和数据交互过程。

通过这种方式，Vue.js前端通过HTTP请求与Spring Boot后端进行通信，实现数据的获取、提交和展示，完成了前后端的分离和协作。这种架构不仅能够提高开发效率，还能够使得前端和后端团队能够独立开发、测试和部署各自的功能模块。





# MySql( 数据库部分 )









