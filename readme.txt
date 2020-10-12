1:先给pom文件注入各种依赖，springMVC的依赖。
2：新建页面，发起请求
3：该请求必须通过Servlet来处理，框架中的Servlet是通过@controller的注解来实现的，所以必须在controller包下新建一个类，并在类上添加@controller
注解，表示这是一个Servlet。
4：在web.xml文件中配置该Servlet的路径和别名，框架中的Servlet请求路径<url-pattern>可以用*.do..(通配符)--》表示所有以do
结尾的请求路径都是通过该Servlet来处理。（该Servlet是DispatcherServlet，其他Servlet由他来指派任务）

5：以前的Servlet只有doget和dopost方法来处理一个请求，现在你可以在Servlet中创建多个方法，并用@RequestMapping注解的value值表示
你要处理的请求名，这个方法就是专门处理这个请求的。

6：该Servlet处理完请求时还要返回结果，以前是用response或者jsp文件直接返回，现在要用ModelAndView来返回处理的结果，因为该Servlet的
每一个方法的返回值都是ModalAndView，该mv中也有方法可以将mv的结果返回到指定页面。

7：由于需要用到视图解析器，所以在springmvc的配置文件里面就必须声明视图解析器：
    <!--声明 springmvc框架中的视图解析器， 帮助开发人员设置视图文件的路径-->
    <bean  class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <!--前缀：视图文件的路径-->
        <property name="prefix" value="/WEB-INF/view/" />
        <!--后缀：视图文件的扩展名-->
        <property name="suffix" value=".jsp" />
    </bean>
    ---》在处理结果返回的页面只需要写上该页面的名字即可，因为有前后缀拼接。

8：我们要用到controller对象的方法，就要确定controller对象所在的位置，在springmvc配置文件可以配置该路径信息：
    <!--声明组件扫描器-->
    <context:component-scan base-package="com.cooler.controller" />

9：前面这些操作的前提都是因为在Tomcat启动时就创建了一个DispatcherServlet对象，这个对象其实也是一个Servlet
        因为DispatcherServlet在他的创建过程中， 会同时创建springmvc容器对象，
        读取springmvc的配置文件，把这个配置文件中的对象都创建好， 当用户发起
        请求时就可以直接使用对象了,DispatcherServlet把请求分派给指定的controller。

        servlet的初始化会执行init（）方法。 DispatcherServlet在init（）中{
           //创建容器，读取配置文件
           WebApplicationContext ctx = new ClassPathXmlApplicationContext("springmvc.xml");
           //把容器对象放入到ServletContext中
           getServletContext().setAttribute(key, ctx);
        }

   所以我们必须在web.xml告知springmvc的配置文件的位置，以及springmvc所指定的包中类处理哪些请求：
       <servlet>
           <servlet-name>myweb</servlet-name>
           <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>

           <!--自定义springmvc读取的配置文件的位置-->
           <init-param>
               <!--springmvc的配置文件的位置的属性-->
               <param-name>contextConfigLocation</param-name>
               <!--指定自定义文件的位置-->
               <param-value>classpath:springmvc.xml</param-value>
           </init-param>
           <!--在tomcat启动后，创建Servlet对象>
           <load-on-startup>1</load-on-startup>
       </servlet>
       <servlet-mapping>
           <servlet-name>myweb</servlet-name>
           <url-pattern>*.do</url-pattern>
       </servlet-mapping>