title: 一些有用的Java语言特性: 静态导入 / try-with-resources /Stream API/ lambda表达式

date: 2017/03/01

category: java

tags:
 - 语言特性
 - java

---

# 静态导入

&nbsp;&nbsp;&nbsp;&nbsp;在JDK1.5中引入了静态导入这一特性，其语法为：``import static package.class.*``，作用为引入类中的静态方法。这种引入方式可以针对单个静态方法或使用通配符*引入全部静态方或静态成员，在引入之后就可以直接调用方法名了。

<!--more-->

&nbsp;&nbsp;&nbsp;&nbsp;比如：


```Java
import static java.lang.System.out;
import static java.lang.Integer.*;

public class TestStaticImport { 
    public static void main(String[] args) { 
        out.println(MAX_VALUE); 
        out.println(toHexString(42)); 
    } 
}
```

&nbsp;&nbsp;&nbsp;&nbsp;在这个例子中out为System类的一个静态对象成员，可以通过out.xxx直接调用out对象的成员方法。而Integer类的使用则更为明显，可以直接使用``MAX_VALUE``这样的静态成员或是``toHexString()``这样的静态方法。

作为对比，如果不使用静态导入的话，就需要这样写：

```Java
import java.lang.System.out;
import java.lang.Integer.*;

public class TestStatic { 
    public static void main(String[] args) { 
        System.out.println(Integer.MAX_VALUE); 
        System.out.println(Integer.toHexString(42)); 
    } 
}

```

·可以看出静态导入可以减少一些无意义的代码。

# try-with-resources

&nbsp;&nbsp;&nbsp;&nbsp;try-with-resources是JDK1.7引入的语法糖，相当于Python里的with语句，会自动调用资源的close()函数，自动回收资源，从而使得代码更加优雅。举一个栗子：

```Java
    static String readFirstLineFromFile(String path) throws IOException {  
        try (BufferedReader br = new BufferedReader(new FileReader(path))) {  
            return br.readLine();  
        }  
    }  
```

&nbsp;&nbsp;&nbsp;&nbsp;相对于标准的try语句，try-with -resources语法多出了一个扩号，这种在扩号中初始资源的try块方法就是try-with-resources。

&nbsp;&nbsp;&nbsp;&nbsp;try-with-resources语法要求资源实现了java.lang.AutoCloseable接口或者java.io.Closeable接口才能自动调用close()函数。其中java.io.Closeable接口要求close()函数必须可以重复调用。

```Java
package com.trywith;

import java.io.Closeable;

import static java.lang.System.out;
/**
 * Created by aak1247 on 2017/3/1.
 */
public class Test implements Closeable {
    @Override
    public void close() {
        out.println("call close");
    }
    public static void main(String args[]){
        try(Test test = new Test()){
            out.println("try block");
        }
    }

}
```
&nbsp;&nbsp;&nbsp;&nbsp;程序的输出为：
<pre>
try block
call close
</pre>

# multi-catch

&nbsp;&nbsp;&nbsp;&nbsp;对于可能产生多个异常的块儿的catch操作可以使用 multi-catch语句进行捕获，只需要在catch后的扩号中填入多个类型并使用``|``分隔开即可，如``catch(NoSuchAlgorithmException | IOException e){}``，需要注意的是，异常的类型之间不能具有相互包含关系(继承关系)否则编译器会报错。

# Stream API

&nbsp;&nbsp;&nbsp;&nbsp;JDK1.8中引入了Stream API，它的作用是处理集合类对象。

# lambda表达式