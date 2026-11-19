# 前后端分离脚手架

> 个人自用的开箱即用工程模板：**Spring Boot 4 + MyBatis + MySQL + MQTT** 后端，**Vue 3 + Element Plus + ECharts** 前端。
> 常用依赖已预装、工程结构已铺好，拉下来改配置就能写业务，省掉每次新建项目重复搭环境的时间。

![backend](https://img.shields.io/badge/backend-Spring%20Boot%204.0.9-6DB33F)
![java](https://img.shields.io/badge/Java-21-orange)
![frontend](https://img.shields.io/badge/frontend-Vue%203-42B983)
![ui](https://img.shields.io/badge/UI-Element%20Plus%202.14-409EFF)

---

## 一、技术栈

### 后端 `backend/`

| 依赖 | 版本 | 用途 |
| --- | --- | --- |
| Spring Boot | 4.0.9 | 基础框架（`spring-boot-starter-webmvc`） |
| Java | 21 | 运行环境 |
| MyBatis Spring Boot Starter | 4.0.1 | 持久层，XML / 注解双模式 |
| MySQL Connector/J | 随 Boot 管理 | 数据库驱动 |
| spring-boot-starter-validation | 随 Boot 管理 | 参数校验（`@Valid` / `@NotBlank`） |
| spring-boot-starter-actuator | 随 Boot 管理 | 健康检查、运行时指标 |
| spring-boot-devtools | 随 Boot 管理 | 改代码自动重启 |
| Eclipse Paho MQTT Client | 1.2.5 | 对接 EMQX / Mosquitto 等 Broker |

- 构建工具：Maven（自带 Wrapper 3.3.4，无需本机装 Maven）
- 包名：`com.hao.smart_plant`

### 前端 `frontend/`

| 依赖 | 版本 | 用途 | 状态 |
| --- | --- | --- | --- |
| Vue | ^3.2.13 | 核心框架（Composition API） | 已启用 |
| Vue Router | ^4.0.3 | 路由，History 模式 | 已启用 |
| Vuex | ^4.0.0 | 全局状态管理 | 已启用（空壳） |
| Element Plus | ^2.14.8 | 桌面端 UI 组件库 | **已安装，待注册** |
| ECharts | ^6.1.0 | 图表可视化 | **已安装，待接入** |
| Axios | ^1.21.1 | HTTP 请求 | **已安装，待封装** |
| @vue/cli-service | ~5.0.0 | 构建与开发服务器 | 已启用 |
| ESLint + eslint-plugin-vue | 7.32 / 8.0.3 | 代码规范（vue3-essential） | 已启用 |
| Babel | @vue/cli-plugin-babel ~5.0.0 | 语法降级 | 已启用 |

- 路径别名：`@` → `src`
- 浏览器目标：`> 1%, last 2 versions, not dead, not ie 11`

---

## 二、目录结构

```
新文件夹/
├── backend/                          # Spring Boot 服务端
│   ├── .mvn/wrapper/                 # Maven Wrapper（only-script 模式）
│   ├── mvnw / mvnw.cmd               # 免安装 Maven 启动脚本
│   ├── pom.xml                       # 依赖与构建配置
│   └── src/
│       ├── main/java/com/hao/smart_plant/
│       │   └── SmartPlantApplication.java   # 启动类
│       ├── main/resources/
│       │   └── application.properties       # 配置文件（目前仅应用名）
│       └── test/java/com/hao/smart_plant/
│           └── SmartPlantApplicationTests.java
│
└── frontend/                         # Vue 3 客户端
    ├── public/
    │   ├── index.html                # HTML 模板
    │   └── favicon.ico
    ├── src/
    │   ├── assets/                   # 静态资源
    │   ├── components/               # 通用组件（HelloWorld.vue 示例）
    │   ├── views/                    # 页面级组件（Home / About）
    │   ├── router/index.js           # 路由表
    │   ├── store/index.js            # Vuex 仓库
    │   ├── App.vue                   # 根组件
    │   └── main.js                   # 入口
    ├── .eslintrc.js                  # 代码规范
    ├── .browserslistrc               # 兼容目标
    ├── jsconfig.json                 # 编辑器智能提示 + @ 别名
    ├── vue.config.js                 # Vue CLI 配置
    └── package.json
```

---

## 三、环境要求

| 项目 | 版本要求 |
| --- | --- |
| JDK | 21（Spring Boot 4 最低 17） |
| Maven | 3.10+，或用自带 `mvnw` |
| Node.js | 18 / 20 LTS 推荐 |
| npm | 9+ |
| MySQL | 8.x |
| MQTT Broker | 可选，EMQX / Mosquitto（默认 1883） |

---

## 四、快速开始

### 1. 后端

先建库（示例名 `smart_plant`）：

```sql
CREATE DATABASE smart_plant DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
```

> ⚠️ **第一次启动前必须先配数据源**。当前 `application.properties` 只有应用名，`mybatis-spring-boot-starter` 会拉起 JDBC 自动配置，没有 `url` 会直接报 `Failed to configure a DataSource`。

补上以下配置：

```properties
spring.application.name=smart_plant

# ---------- 服务端口（避免与前端 8080 冲突，见「常见问题」） ----------
server.port=8080

# ---------- 数据源 ----------
spring.datasource.url=jdbc:mysql://localhost:3306/smart_plant?useUnicode=true&characterEncoding=utf8&serverTimezone=Asia/Shanghai&useSSL=false&allowPublicKeyRetrieval=true
spring.datasource.username=root
spring.datasource.password=你的密码
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# ---------- MyBatis ----------
mybatis.mapper-locations=classpath*:mapper/**/*.xml
mybatis.type-aliases-package=com.hao.smart_plant.entity
mybatis.configuration.map-underscore-to-camel-case=true

# ---------- MQTT（按需） ----------
mqtt.broker=tcp://127.0.0.1:1883
mqtt.client-id=smart-plant-server
mqtt.username=
mqtt.password=
```

启动：

```bash
cd backend
./mvnw spring-boot:run          # Windows: mvnw.cmd spring-boot:run
```

打包：

```bash
./mvnw clean package -DskipTests
java -jar target/smart_plant-0.0.1-SNAPSHOT.jar
```

验证：访问 `http://localhost:8080/actuator/health` 应返回 `{"status":"UP"}`。

### 2. 前端

```bash
cd frontend
npm install
npm run serve                   # 默认 http://localhost:8080
```

打包产物在 `dist/`：

```bash
npm run build
```

---

## 五、预装插件接入指南

依赖装好了但没接线，属于"备好的零件"。下面四段按需粘，粘完就能用。

### 1. Element Plus（UI 组件库）

```js
// src/main.js
import { createApp } from 'vue'
import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'
import zhCn from 'element-plus/es/locale/lang/zh-cn'
import App from './App.vue'
import router from './router'
import store from './store'

const app = createApp(App)
app.use(store)
app.use(router)
app.use(ElementPlus, { locale: zhCn })   // 中文语言包
app.mount('#app')
```

> 要用 `<el-icon>` 图标需另装图标包：`npm i @element-plus/icons-vue`，再在 `main.js` 里循环 `app.component(key, comp)` 全局注册。
> 体积敏感的话可改用 `unplugin-vue-components` + `unplugin-auto-import` 做按需引入。

### 2. ECharts（图表）

推荐的按需引入封装，避免整包进 bundle：

```js
// src/utils/echarts.js
import * as echarts from 'echarts/core'
import { LineChart, BarChart, PieChart } from 'echarts/charts'
import {
  GridComponent, TooltipComponent, LegendComponent, TitleComponent
} from 'echarts/components'
import { CanvasRenderer } from 'echarts/renderers'

echarts.use([
  LineChart, BarChart, PieChart,
  GridComponent, TooltipComponent, LegendComponent, TitleComponent,
  CanvasRenderer
])

export default echarts
```

组件里用：

```js
import echarts from '@/utils/echarts'

const chart = echarts.init(this.$refs.chartDom)
chart.setOption({ xAxis: { type: 'category', data: ['A', 'B'] }, yAxis: {}, series: [{ type: 'line', data: [1, 2] }] })
// 容器需要有明确宽高；窗口 resize 时记得 chart.resize()
```

### 3. Axios（请求封装）

`vue.config.js` 里加代理解决跨域：

```js
const { defineConfig } = require('@vue/cli-service')

module.exports = defineConfig({
  transpileDependencies: true,
  devServer: {
    port: 8081,                       // 前端跑 8081，避开后端 8080
    proxy: {
      '/api': {
        target: 'http://localhost:8080',
        changeOrigin: true
        // 后端接口本身带 /api 前缀就别加 pathRewrite
      }
    }
  }
})
```

统一请求实例：

```js
// src/utils/request.js
import axios from 'axios'
import { ElMessage } from 'element-plus'

const service = axios.create({
  baseURL: '/api',
  timeout: 10000
})

service.interceptors.request.use(
  config => {
    const token = localStorage.getItem('token')
    if (token) config.headers.Authorization = `Bearer ${token}`
    return config
  },
  error => Promise.reject(error)
)

service.interceptors.response.use(
  response => {
    const res = response.data
    if (res.code !== 200) {
      ElMessage.error(res.message || '请求失败')
      return Promise.reject(new Error(res.message))
    }
    return res.data
  },
  error => {
    ElMessage.error(error.message || '网络异常')
    return Promise.reject(error)
  }
)

export default service
```

### 4. MQTT（后端设备接入）

```java
// com.hao.smart_plant.config.MqttConfig
package com.hao.smart_plant.config;

import org.eclipse.paho.client.mqttv3.MqttClient;
import org.eclipse.paho.client.mqttv3.MqttConnectOptions;
import org.eclipse.paho.client.mqttv3.persist.MemoryPersistence;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class MqttConfig {

    @Value("${mqtt.broker}")
    private String broker;

    @Value("${mqtt.client-id}")
    private String clientId;

    @Bean(destroyMethod = "disconnect")
    public MqttClient mqttClient() throws Exception {
        MqttClient client = new MqttClient(broker, clientId, new MemoryPersistence());
        MqttConnectOptions options = new MqttConnectOptions();
        options.setAutomaticReconnect(true);   // 断线自动重连
        options.setCleanSession(true);
        options.setConnectionTimeout(10);
        options.setKeepAliveInterval(30);
        client.connect(options);
        return client;
    }
}
```

订阅示例：

```java
mqttClient.subscribe("plant/+/telemetry", (topic, msg) ->
    System.out.println(topic + " -> " + new String(msg.getPayload())));
```

> 生产环境建议加 `@Scheduled` 保活 + 重连退避，并把报文落到时序表。

---

## 六、常用命令

| 场景 | 命令 |
| --- | --- |
| 后端启动 | `cd backend && ./mvnw spring-boot:run` |
| 后端打包 | `./mvnw clean package -DskipTests` |
| 后端测试 | `./mvnw test` |
| 前端安装依赖 | `cd frontend && npm install` |
| 前端开发 | `npm run serve` |
| 前端构建 | `npm run build` |
| 前端检查 | `npm run lint` |

---

## 七、常见问题

**1. 启动报 `Failed to configure a DataSource: 'url' attribute is not specified`**
数据源没配。按「快速开始」补 `spring.datasource.*`。暂时不想连库可以先注释掉 `mybatis-spring-boot-starter` 依赖，或在启动类加
`@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class, MybatisAutoConfiguration.class})`。

**2. 前后端都默认 8080，端口打架**
前端 `vue.config.js` 里把 `devServer.port` 改成 8081（已在上文示例中处理），或者后端 `server.port=9090`，代理 `target` 同步改。

**3. 跨域被浏览器拦**
开发期走 `devServer.proxy`（同源，无需后端改）；生产期用 Nginx 反代，别在后端开 `@CrossOrigin("*")` 了事。

**4. Spring Boot 4 的 starter 名字变了**
4.x 把 web 相关 starter 拆细了，本工程用的是 `spring-boot-starter-webmvc`（对应测试依赖 `spring-boot-starter-webmvc-test`）。老教程里的 `spring-boot-starter-web` 在 4.x 里已推荐换成新名字，照抄老配置容易引错包。

**5. Vue 3 别装 `vue-template-compiler`**
那是 Vue 2 的编译器，装了会和 `vue` 3 版本校验报错。Vue 3 用 `@vue/compiler-sfc`（CLI 已自带）。

**6. Element Plus 组件样式丢失**
只 `app.use(ElementPlus)` 不够，必须额外 `import 'element-plus/dist/index.css'`。

**7. 修改后端代码没生效**
`devtools` 只对 classpath 变化生效，且会重启上下文（session 丢失）。改 `pom.xml` 或配置后需手动重启。IDEA 里要开 `Build project automatically` + 允许自动 make。

**8. Node 版本过高导致构建报 `ERR_OSSL_EVP_UNSUPPORTED`**
Vue CLI 5 一般不受影响；若遇到，临时用 `set NODE_OPTIONS=--openssl-legacy-provider`（Windows）或降级到 Node 18 LTS。

**9. `npm install` 卡住 / 依赖冲突**
换镜像：`npm config set registry https://registry.npmmirror.com`。本仓库已带 `package-lock.json`，建议用 `npm ci` 保证可复现。

---

## 八、Git 约定

两个子项目各自带 `.gitignore`：

- `frontend/.gitignore`：忽略 `node_modules`、`/dist`、`.env.local`、编辑器目录
- `backend/.gitignore`：忽略 `target/`、`.mvn/wrapper/maven-wrapper.jar`、IDE 配置

---

## 说明

个人自用脚手架，随项目演进持续迭代。域名 `smart_plant` 沿用自智慧种植方向的原型，实际作为通用前后端起点使用。
