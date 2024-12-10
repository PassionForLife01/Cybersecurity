**JAR（Java Archive）** 和 **WAR（Web Application Archive）** 是 Java 应用中常见的两种打包格式，分别用于打包普通 Java 应用和 Web 应用。以下是打包的基础知识和操作步骤，并结合实例说明。

---

## **JAR 打包**

### **适用场景**

- 用于普通的 Java 应用程序（通常是桌面或控制台程序）。
- 包含 `.class` 文件、资源文件（如配置文件、图片等）、以及可选的 `META-INF/MANIFEST.MF` 文件。

---

### **实例：创建并打包一个 JAR 文件**

#### **项目结构**

plaintext

Copy code

`MyApp/ ├── src/ │   └── Main.java └── resources/     └── config.properties`

#### **代码示例**

**`src/Main.java`**

java

Copy code

`public class Main {     public static void main(String[] args) {         System.out.println("Hello, JAR!");     } }`

#### **步骤**

1. **编译 Java 文件** 使用 `javac` 编译代码：
    
    bash
    
    Copy code
    
    `javac -d out src/Main.java`
    
    - `-d out` 表示编译输出到 `out` 文件夹。
2. **创建 JAR 包**
    
    - 创建 `MANIFEST.MF` 文件（可选）：
        
        plaintext
        
        Copy code
        
        `Manifest-Version: 1.0 Main-Class: Main`
        
    - 打包 JAR 文件：
        
        bash
        
        Copy code
        
        `jar cvfm MyApp.jar MANIFEST.MF -C out .`
        
        参数说明：
        - `c`: 创建 JAR 包。
        - `v`: 显示详细信息。
        - `f`: 指定输出文件名。
        - `m`: 使用自定义 `MANIFEST.MF` 文件。
        - `-C`: 指定目录并打包。
3. **运行 JAR 包** 使用以下命令运行：
    
    bash
    
    Copy code
    
    `java -jar MyApp.jar`
    
    输出：
    
    plaintext
    
    Copy code
    
    `Hello, JAR!`
    

---

## **WAR 打包**

### **适用场景**

- 用于 Java Web 应用，部署在 **Servlet 容器**（如 Tomcat、Jetty）中。
- 包含 `.class` 文件、`WEB-INF` 目录（含 `web.xml`）、以及静态资源（HTML、CSS、JS）。

---

### **实例：创建并打包一个 WAR 文件**

#### **项目结构**

plaintext

Copy code

`MyWebApp/ ├── src/ │   └── HelloServlet.java ├── WebContent/ │   ├── index.html │   ├── css/ │   │   └── style.css │   └── WEB-INF/ │       ├── web.xml │       └── classes/`

#### **代码示例**

**`src/HelloServlet.java`**

java

Copy code

`import java.io.IOException; import javax.servlet.ServletException; import javax.servlet.annotation.WebServlet; import javax.servlet.http.HttpServlet; import javax.servlet.http.HttpServletRequest; import javax.servlet.http.HttpServletResponse;  @WebServlet("/hello") public class HelloServlet extends HttpServlet {     protected void doGet(HttpServletRequest request, HttpServletResponse response)             throws ServletException, IOException {         response.getWriter().write("Hello, WAR!");     } }`

**`WebContent/WEB-INF/web.xml`**

xml

Copy code

`<web-app>     <servlet>         <servlet-name>HelloServlet</servlet-name>         <servlet-class>HelloServlet</servlet-class>     </servlet>     <servlet-mapping>         <servlet-name>HelloServlet</servlet-name>         <url-pattern>/hello</url-pattern>     </servlet-mapping> </web-app>`

#### **步骤**

1. **编译 Java 文件**
    
    bash
    
    Copy code
    
    `javac -d WebContent/WEB-INF/classes src/HelloServlet.java`
    
2. **创建 WAR 包**
    
    bash
    
    Copy code
    
    `jar cvf MyWebApp.war -C WebContent .`
    
    参数说明：
    
    - `c`: 创建 WAR 包。
    - `v`: 显示详细信息。
    - `f`: 指定输出文件名。
    - `-C`: 指定目录并打包。
3. **部署 WAR 包**
    
    - 将 `MyWebApp.war` 放置在 Tomcat 的 `webapps` 目录下。
    - 启动 Tomcat，访问：
        
        plaintext
        
        Copy code
        
        `http://localhost:8080/MyWebApp/hello`
        
    
    输出：
    
    plaintext
    
    Copy code
    
    `Hello, WAR!`
    

---

## **总结**

- **JAR** 用于打包普通的 Java 程序，强调包含 `.class` 文件和主类的定义。
- **WAR** 用于 Java Web 应用，需包含 `WEB-INF` 和相关的 Web 资源。
- 两者都使用 `jar` 命令创建，但目录结构和内容要求不同。

如果有其他具体需求，欢迎继续提问！