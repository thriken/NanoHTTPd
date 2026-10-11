# NanoHTTPd（单文件嵌入式 HTTP 服务器）

一个简单、小巧、可嵌入的 Java HTTP 1.0 服务器，整个实现只有一个文件 `NanoHTTPd.java`。
原始版本 1.22，版权归 Jarno Elonen 与 Konstantinos Togias；本副本已升级为 **Java 17 兼容**。

A tiny, embeddable Java HTTP 1.0 server in a single file. Upgraded for **Java 17 compatibility**.

---

## 环境要求 / Requirements

- **Java 11 或更高版本的运行环境**（JRE 或 JDK 均可）。
  推荐 **JDK 17**，因为 `java NanoHTTPd.java` 这种"单文件源码直接运行"模式从 Java 11 才开始支持。
- 本仓库默认用 JDK 17 编译，生成的 `.class` 也需 Java 17+ 才能运行（如需兼容更老的运行时，可用 `javac --release 11 NanoHTTPd.java` 编译）。

> 注意：如果你的机器 `PATH` 里同时存在老版本 JRE（如 Java 7），`java` 可能指向旧版而报
> "找不到或无法加载主类"。请把 JDK 17 的 `bin` 目录放到 `PATH` 最前面，或直接使用完整路径：
> `"C:\Program Files\Java\jdk-17\bin\java.exe" NanoHTTPd.java`

---

## 编译与运行 / Build & Run

### 方式一：先编译再运行

```cmd
cd /d c:\Users\22872\Desktop\NanoHttpd
javac NanoHTTPd.java
java NanoHTTPd 8080
```

### 方式二：单文件源码直接运行（需 Java 11+）

```cmd
cd /d c:\Users\22872\Desktop\NanoHttpd
java NanoHTTPd.java 8080
```

启动后控制台会输出：

```
NanoHTTPd 1.22 ...
正在端口 8080 提供文件服务，根目录："..."
```

---

## 命令行参数 / Command Line Options

| 参数 | 说明 |
|------|------|
| `[port]` | 监听端口，默认 `80`。例如 `8080` |
| `--licence` | 打印 BSD 许可协议全文后退出 |

示例：

```cmd
java NanoHTTPd.java 8080 --licence
```

---

## 使用方式 / Usage

1. 启动后，用浏览器访问 `http://localhost:8080/`，服务器会把**当前目录**作为 Web 根目录提供文件下载。
2. 访问一个目录时：
   - 若目录含 `index.html` / `index.htm`，自动返回该文件；
   - 否则列出目录内容（默认开启目录列表）；
   - 目录 URL 缺少结尾 `/` 时会做 301 重定向。
3. 支持的功能：
   - GET / POST 参数解析
   - 文件上传（`multipart/form-data`）
   - 简单断点续传（`Range` 请求头）
   - 大文件流式传输，不占内存

### 退出服务器

在浏览器访问 `http://localhost:8080/?exit` 即可让服务器进程退出。

---

## 自定义 / Customize

继承 `NanoHTTPd` 并重写 `serve()` 方法即可返回动态内容，例如：

```java
public class MyServer extends NanoHTTPd {
    public MyServer(int port) throws IOException { super(port); }

    @Override
    public Response serve(String uri, String method,
                          Properties header, Properties parms, Properties files) {
        return new Response(HTTP_OK, MIME_HTML, "<h1>Hello</h1>");
    }

    public static void main(String[] args) throws IOException {
        new MyServer(8080);
    }
}
```

---

## 已知说明 / Notes

- HTTP 1.0 服务器，单文件、无配置文件、无日志框架、无鉴权（如需请自行实现）。
- 源码已做 JDK 17 兼容清理：使用 `URLEncoder.encode(String, Charset)`、`StringBuilder`，
  并为 `Hashtable`/`Vector` 补充泛型，编译时无 `unchecked` 告警。
