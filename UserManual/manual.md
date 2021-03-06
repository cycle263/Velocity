* 1. 转义和反转义
  
  - 1.1 转义

  #set($a="<a>link</a>") => <a>link</a>
  #set($a='<a>link</a>') => <a>link</a>
  单引号和双引号都对代码进行了转义即"<"被转义<

  #set($b="$a") => &lt;a&gt;link&lt;/a&gt;
  #set($b='$a') => $a
  单引号会将其当作字符串；但双引号还是会执行里面的代码，所以&进一步被转义成& 这种情况下代码是无法被反转义回去的，因此要避免双引号的滥用。

  注：代码做了两次及两次以上的转义之后，是无法被反转义成最初的代码。

  #set($a="&") => &amp;
  #set($a='&') => &amp;
  不管是单引号还是双引号，set的时候&都会被转义

  - 1.2 反转义

  #SHTML和#SLITERAL（#SLITERAL其实是literal，按照字面意思）都可以对代码进行反转义，但是#SHTML同时执行 xss的过滤动作，过滤掉非法的脚本(其实是过滤了所有的脚本)，具体可以参考：[SofaMVC安全编码规范](http://doc.alipay.net/pages/viewpage.action?pageId=11255676)

  #SHTML和#SLITERAL都不能进行赋值，也不能嵌套引用。

  #set($c=#SLITERAL($a)) => 报错
  #SLITERAL(#SLITERAL($a)) => 报错

  #set($c = $stringUtil.defaultIfNull("#SLITERAL($a)")) => 可
  因此无法进行多次反转义，所以被多次转义的代码无法反转义成原来的代码。

  注：#set($c="#SLITERAL($a)") 这样的代码是没有意义的，同#set($c=$a)效果是相同。

* 2. 不常见的用法介绍

  - 2.1 array

  #set($a=[1,2,3])
  可以通过 $a.get(0)来获取第一个值。

  - 2.2 map类

  #set($obj={"a":1, "b":2})
  可以通过 $obj.get(“a”)来获取key为a的值。键值的获取方式还有以下几种：

  所有键值对 — obj.entrySet() => [a=1, b=2]
  所有键 — obj.keySet()  => [a, b]
  所有值 — obj.values()  => [1, 2]
  通过循环遍历所有的值：

  #foreach($item in $obj.entrySet())
  $item.key : $item.value
  #end
  #foreach($key in $obj.keySet())
  $key :$obj.get($key)
  #end
  #foreach($value in $obj.values())
  $value
  #end
  - 2.3 字符串数组

  $stringUtil.split返回的是字符串数组String[]，该数组无法通过$aa[0]或$aa.get(0)调用，只能通过#foreach遍历。

* 3. #parse, $tile

  #parse和$tile的区别：

  - #parse是先插入后解析，$tile是先解析后插入。
  - #parse和被#parse的页面变量相互共享，$tile变量需要传入。
  - #parse是代码块无须controller，$tile有逻辑时需要类似controller的渲染类或者说处理类。
  - #parse需要配置系统的默认path，推荐能用#parse就不要用$tile
  
  因此，建议优先考虑#parse，能用#pares不用$tile。

  补充：#parse路径都是相对于系统的模板根目录。系统的模板根目录的路径参看auto-config.xml（auto-config.xml文件的路径，/系统/assembly/template/src/main/resources/config/autoconf/auto-config.xml）。

  貌似服务器上的antx.properties也是根据auto-config.xml生产的。

* 4. vm中的组件，宏，变量

  - 4.1 vm中的组件

  vm中可以用的组件包括$tile, $template, uri-broker，具体可以参考：[SofaMVC使用文档-视图组件-v2|http://doc.alipay.net/pages/viewpage.action?pageId=10293362 ]
  * $tile

  tile是专门用于处理公用页面的组件。例如个人版下的页面侧边栏都是调用同一个vm文件：

  $tile.setTemplate(”home:aside.vm”).setParameter(”isExpandAside”,$isExpandAside)
  远程tile, 可以实现跨系统公共页面调用，例如在安全中心下的页面调用个人版的侧边栏：

  $tile.setTemplate(”$personalRemoteTile.setTarget(’home:aside.tile’)”).setParameter(”isExpandAside”,$isExpandAside)
  $template
  $template是uisvr存放在velocity context的中的实例变量。

  template和tile类似，也是调用页面的公用部分。例如layout中的default.vm:

  $template.head$screen_placeholder$template.foot
  template支持的所有指令有：

  1. 容器类，除了自身内容外，还可以再嵌套其它$template指令，会查找uisvr上对应的文件，并渲染。
    1.  $template.head
    2.  $template.foot
    3.  $template.page

  2. 引用具体的内容，不嵌套其它$tempalte指令，会查找uisvr上对应的文件，并渲染。
    1.  $template.page.js
    2.  $template.page.msg //使用场景未出现
    3.  $template.page.css

  3. 只返回内容，不会查找渲染uisvr上的文件。
    1.  $template.cssRef
    2.  $template.jsRef

  4. 只传递参数，不进行渲染
    1.  $template.foot.setParameter(”foo”,”bar”)
    2.  $template.page.js.setParameter(”foo”,”bar”)

  template不存在远程调用，因为template所引用的资源都是uisvr中的，uisvr在所有的系统中都是公用的。
  tile和template唯一的区别就是，tile调用时需要向系统发送请求，而template不需要。

  uri-broker
  每个系统都有uri-broker.xml配置文件，具体的路径是/系统/app/web/home/src/main/resources/uri-brokers.xml 或者/系统/系统/web/home/src/main/resources/uri-brokers.xml

  具体静态资源的<content-uri>和针对动态请求的<component-uri>
  ```xml
  <content-uri name="homeServer" expose="true">
    <serverURI>
      ${core_protocol}://securitycenter.${core_domainName}:${core_web_port}
    </serverURI>
    <custom-handler class="com.alipay.sofa.runtime.web.smvc.util.uribroker.handler.DomainReplaceHandler" parameter="taobao" />
  </content-uri>
  <component-uri name="certModule" expose="true" extends="homeServer">
    <contextPath>/</contextPath>
    <carPath>cert</carPath>
    <convertTargetCaseAndUnderscore>false</convertTargetCaseAndUnderscore>
  </component-uri>
  ```
  用法如下：
  ```js
  $homeServer.getURI("index.htm")
  $certModule.setTarget('index.htm')
  ```
  注：
  
  ```
  #set($i=$homeServer.getURI("index.htm"))         
  $i        => http://securitycenter.alipay.net/index.htm    
  #set($j="$i")或者#set($j=$i)         
  $j        => http://securitycenter.alipay.net
  ```
  $i变量只要执行过一次，后面的参数就会去除。因为$homeServer.getURI("index.htm")或者$certModule.setTarget('index.htm')输出的都是对象，但是这种对象和$homeServer，$certModule不同，未被put页面中。建议设置变量的时候直接输出字符

  #set($i=$homeServer.getURI("index.htm").render())

  或者
  
  `#set($i="$homeServer.getURI("index.htm")")`

  - 4.2 vm中的宏

  vm中有三处可以写宏：

  系统的home下的macros-default.vm文件，所有页面可调用；
  系统其他频道里的macros.vm文件，频道下所有页面可调用；
  页面中，本页面可调用；
  如果遇到重名的，三者的优先级为2 > 1 > 3。

  注：修改macros-default.vm和macros.vm都需要重新部署，修改页面中的宏则不需要。

  4.3 vm中的变量

  vm中的有哪些可用的变量？除了在vm中定义的以及通过#parse引入的变量，绝大多数变量是后台modelMap到页面中的，需要和后台开发人员明确变量的类型，有利于变量的使用。

5. 关于工具类

  - 5.1    工具类的版本

  在/系统/pom.xml中标识了本系统所引用jar名称以及版本，其中StringUtil, ArrayUtil都在toolkit-common-lang-1.0.jar包里，现在系统一般使用的都是1.0版本，用法差别不是很大。
  ```xml
  <dependency>
      <groupId>com.alibaba.toolkit.common</groupId>
      <artifactId>toolkit-common-lang</artifactId>
      <version>1.0</version>
  </dependency>
  ```
  
  - 5.2    工具类的种类

  在系统中有哪些工具类可以使用呢？merge-smvc-scheme-pull.xml表明了系统注入了哪些工具类。

  merge-smvc-scheme-pull.xml路径如下：

  /系统/app/web/home/src/main/resources/MERGE-INF/merge-smvc-scheme-pull.xml 或者 /系统/系统/web/home/src/main/resources/MERGE-INF/merge-smvc-scheme-pull.xml

  一些常用的工具类框架会注入, 例如StringUtil等，其版本还是依据系统pom.xml里的版本，但是merge-smvc-scheme-pull.xml会覆盖框架注入的。

  如何增加系统的工具类呢？
  先配置一个bean：

  <bean id="dateUtil"/>

  然后在bean2VeclocityContextTool这个bean下添加一个一个entry：

  <entry key="dateUtil" value-ref="dateUtil"/>

  页面上就是通过这个key去调用的。

  注意配置的类只能是单例，调用他的静态方法，不可实例化。

  如何在eclipse中查看工具类呢？
  虽然[velocity工具类介绍|http://doc.alipay.net/pages/viewpage.action?pageId=13605311]中附件中上传了所有工具类的源码，但是查看不方便，而且各个系统使用工具类的版本可能不同，最好在eclipse中查看本系统的工具类。

  通过mvn eclipse:eclipse将代码编译成工程导入到eclipse中，就可以用Shift+Command+T(mac下)搜索工具类。

  具体参考：[Personal项目导入eclipse手册](http://doc.alipay.net/pages/viewpage.action?pageId=13600451)
