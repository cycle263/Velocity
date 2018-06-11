本文主要介绍提供了十个工具类，stringUtil, arrayUtil, array, objectUtil, dateUtil, requestUtil, requestFormUtil, systemUtil,
alipaystringUtil, operationContext。
1. stringUtil
stringUtil是最常用到的工具类，它所提供的方法也是最多的，共有67种方法，包括判空函数、默认值函数、去空 白（或指定字符）函数、比较函数、字符串类型判定函数、大小写转换，字符串分割、字符串连接、字符串查找、取子串、删除字符、替换字符、重复/对齐字符、 反转字符、取得字符串的缩略、比较两个字符串的异同等。以下列举一些比较常用的函数：

1. isEmpty

/**
* 检查字符串是否为null或空字符串。
* stringUtil.isEmpty(null) = true
* stringUtil.isEmpty("")  = true
* stringUtil.isEmpty(" ")  = false
* stringUtil.isEmpty("bob")= false
* stringUtil.isEmpty("  bob  ") = false
* @param str 要检查的字符串
* @return 如果为空, 则返回true
*/
isNotEmpty反之。

2. isBlank ^ isNotBlank

/**
*检查字符串是否是空白：null、空字符串或只有空白字符
* stringUtil.isBlank(null) = true
* stringUtil.isBlank("")   = true
* stringUtil.isBlank(" ")  = true
* stringUtil.isBlank("bob")= false
* stringUtil.isBlank("  bob  ") = false
* @param str 要检查的字符串
* @return 如果为空白, 则返回true
*/
isNotBlank则反之。

3. defaultIfNull

/**
*如果字符串是null，则返回指定默认字符串，否则返回字符串本身。
* stringUtil.defaultIfNull(null, "default")  = "default"
* stringUtil.defaultIfNull("", "default")    = ""
* stringUtil.defaultIfNull("  ", "default")  = "  "
* stringUtil.defaultIfNull("bat", "default") = "bat"
* @param str 要转换的字符串
* @param defaultStr 默认字符串
* @return 字符串本身或指定的默认字符串
*/
相关的函数：defaultIfEmpty, defaultIfBlank.

4. trim

/**
* 除去字符串头尾部的空白，如果字符串是null，依然返回null。
* 因而可以除去英文字符集之外的其它空白，如中文空格。
* stringUtil.trim(null)= null
* stringUtil.trim("")  = ""
* stringUtil.trim(" ")  = ""
* stringUtil.trim("abc")    = "abc"
* stringUtil.trim("    abc    ") = "abc"
* @param str 要处理的字符串
* @return 除去空白的字符串，如果原字串为null，则返回null
*/
相关函数：trimStart, trimEnd, trimToNull, trimToEmpty.

5. equals

/**
* 比较两个字符串（大小写敏感）。
* stringUtil.equals(null, null)   = true
* stringUtil.equals(null, "abc")  = false
* stringUtil.equals("abc", null)  = false
* stringUtil.equals("abc", "abc") = true
* stringUtil.equals("abc", "ABC") = false
* @param str1 要比较的字符串1
* @param str2 要比较的字符串2
* @return 如果两个字符串相同，或者都是null，则返回true
*/
equalsIgnoreCase则是不区分大小写。

6. split

/**
* 将字符串按空白字符分割。
* 分隔符不会出现在目标数组中，连续的分隔符就被看作一个。如果字符串为null，则返回null。
* stringUtil.split(null)       = null
* stringUtil.split("")         = []
* stringUtil.split("abc def")  = ["abc", "def"]
* stringUtil.split("abc  def") = ["abc", "def"]
* stringUtil.split(" abc ")    = ["abc"]
* @param str 要分割的字符串
* @return 分割后的字符串数组，如果原字符串为null，则返回null
*/
7. join

/**
* 将数组中的元素连接成一个字符串。
* stringUtil.join(null)            = null
* stringUtil.join([])              = ""
* stringUtil.join([null])          = ""* stringUtil.join(["a", "b", "c"]) = “abc”
* stringUtil.join([null, "", "a"]) = "a"
* @param array 要连接的数组
* @return 连接后的字符串，如果原数组为null，则返回null
*/
8 . indexOf

/**
* 在字符串中查找指定字符，并返回第一个匹配的索引值。如果字符串为null或未找到，则返回-1。
* stringUtil.indexOf(null, *)         = -1
* stringUtil.indexOf("", *)           = -1
* stringUtil.indexOf("aabaabaa", 'a') = 0
* stringUtil.indexOf("aabaabaa", 'b') = 2
* @param str 要扫描的字符串
* @param searchChar/searchStr 要查找的字符或字符串
* @return 第一个匹配的索引值。如果字符串为null或未找到，则返回-1
*/
lastIndexOf则是从字符串尾部开始查找。

相关函数：indexOfAny, indexOfAnyBut, lastIndexOfAny.

9. contains

/**
* 检查字符串中是否包含指定的字符。如果字符串为null，将返回false。
* stringUtil.contains(null, *)    = false
* stringUtil.contains("", *)      = false
* stringUtil.contains("abc", 'a') = true
* stringUtil.contains("abc", 'z') = false
* @param str 要扫描的字符串
* @param searchChar/searchStr 要查找的字符或字符串
* @return 如果找到，则返回true
*/
相关函数：containsOnly, containsNone.

10. substring

/**
* 取指定字符串的子串。
* 负的索引代表从尾部开始计算。如果字符串为null，则返回null。
* stringUtil.substring(null, *)   = null
* stringUtil.substring("", *)     = ""
* stringUtil.substring("abc", 0)  = "abc"
* stringUtil.substring("abc", 2)  = "c"
* stringUtil.substring("abc", 4)  = ""
* stringUtil.substring("abc", -2) = "bc"
* stringUtil.substring("abc", -4) = "abc"
* @param str 字符串
* @param start 起始索引，如果为负数，表示从尾部查找
* @return 子串，如果原始串为null，则返回null
*/
相关函数：substringBefore, substringBeforeLast, substringAfter, substringAfterLast, substringBetween.

11. replace

/**
* 替换指定的子串，替换所有出现的子串。
* 如果字符串为null则返回null，如果指定子串为null，则返回原字符串。
* stringUtil.replace(null, *, *)        = null
* stringUtil.replace("", *, *)          = ""
* stringUtil.replace("aba", null, null) = "aba"
* stringUtil.replace("aba", "a", null)  = "aba"
* stringUtil.replace("aba", "a", "")    = “b”
* stringUtil.replace("aba", "a", "z")   = “zbz”
* @param text 要扫描的字符串
* @param repl 要搜索的子串
* @param with 替换字符串
* @return 被替换后的字符串，如果原始字符串为null，则返回null
*/
相关函数：replaceOnce, replaceChars.

12. reverse

/**
* 反转字符串中的字符顺序。
* 如果字符串为null，则返回null。
* stringUtil.reverse(null)  = null
* stringUtil.reverse("")    = ""
* stringUtil.reverse("bat") = "tab"
* @param str 要反转的字符串
* @return 反转后的字符串，如果原字符串为null，则返回null
*/
2. arrayUtil

常用的方法：

  1. isEmpty, isNotEmpty, isBlank, isNotBlank  
  2. defaultIfNull, defualtIfEmpty, defaultIfBlank  
  3. equals
  4. reverse
  5. indexOf, lastIndexOf
  6. toString
同stringUtil类似，不多介绍了。

3. array (不常用)

4. objectUtil

  1. defaultIfNull
  2. equals
  3. hashCode
  同stringUtil类似，不多介绍了。

5. dateUtil

dateUtil处理Date对象的工具类，最常用的方法就是format，让时间对象按指定的格式输出，例如：
$dateUtil.format(“Mon Aug 12 00:00:00 CST 2013”, “yyyy年MM月dd日”)
//输出的结果就是2013年08月12日

6. systemUtil

systemUtil是取得有关系统信息的工具类。在vm中最常用的是获取服务器的名字$!systemUtil.hostInfo.namehost。

7. requestUtil

requestUtil只有一个方法，$requestUtil.getPreviousURL($request)，用于获取上次请求URL。

8. requestFormUtil

requestFormUtil也有一个方法$requestFormUtil.isRequestFromAIR($request)，主要用于判断请求的来源是不是航空版支付宝，现在好像也不常用。

9. alipaystringUtil (不常用)

10.  operationContext (不常用)

11. math 

  add()	Addition
  sub()	Subtraction
  mul()	Multiplication
  div()	Division
  pow()	Power of
  max()	Maximum of two numbers
  min()	Minimum of two numbers
  abs()	Absolute value of a number
  toDouble()	Converts a number into a double.
  toInteger()	Converts a number into an integer
  roundTo()	Rounds a number to the specified number of decimal places
  roundToInt()	Rounds a number to the nearest whole Integer
  getRandom()	Returns a pseudo-random number
  random()	Returns a pseudo-random number in a configurable range
  使用方法，例如除法：$math.div($val1, $val2)，相当于$val1 / $val2
