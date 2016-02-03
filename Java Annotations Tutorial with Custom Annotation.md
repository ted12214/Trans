Java注解教程及自定义注解
============================
Java注解提供了关于代码的一些信息但并不直接作用与它所注解的代码内容。在这个教程当中，我们将学习Java的注解，如何定制注解，注解的使用以及如何通过反射解析注解。

Java1.5引入了注解，当前许多java框架中大量使用注解，如Hebernate、Jersey、Spring。注解作为程序的元数据嵌入到程序当中。注解可以被一些解析工具或者是编译工具进行解析。我们也可以声明注解在编译过程或执行时产生作用。

在使用注解之前，程序源数据只是通过java注释和javadoc，但是注解提供的功能要远远超过这些。注解不仅包含了元数据它还可以作用于程序运行过程中、注解解释器可以通过注解决定程序的执行顺序。例如，在Jersey webservice 我们为方法添加URI字符串的形式的**PATH**注解，那么在程序运行过程中jerser解释程序将决定该方法去调用所给的URI。

###创建Java自定义注解###
创建自定义注解和创建一个接口相似，但是注解的interface关键字需要以@符号开头。我们可以为注解声明方法。我们先来看看注解的例子，然后我们将讨论他的一些特性。

	package com.journaldev.annotations;
	
	import java.lang.annotation.Documented; 
	import java.lang.annotation.ElementType;
	import java.lang.annotation.Inherited;
	import java.lang.annotation.Retention;
	import java.lang.annotation.RetentionPolicy;
	import java.lang.annotation.Target;

	@Documented
	@Target(ElementType.METHOD)
	@Inherited
	@Retention(RetentionPolicy.RUNTIME)
	public @interface MethodInfo{
		String author() default 'Pankaj';
		String date();
		int revision() default 1;
		String comments();
	}

* 注解方法不能带有参数；
* 注解方法返回值类型限定为：基本类型、String、Enums、Annotation或者是这些类型的数组；
* 注解方法可以有默认值；
* 注解本身能够包含元注解，元注解被用来注解其它注解。

这里有四种类型的元注解：

1. **@Documented** —— 指明拥有这个注解的元素可以被javadoc此类的工具文档化。这种类型应该用于注解那些影响客户使用带注释的元素声明的类型。如果一种声明使用**Documented**进行注解，这种类型的注解被作为被标注的程序成员的公共API。

2. **@Target**——指明该类型的注解可以注解的程序元素的范围。该元注解的取值可以为TYPE,METHOD,CONSTRUCTOR,FIELD等。如果**Target**元注解没有出现，那么定义的注解可以应用于程序的任何元素。

3. **@Inherited**——指明该注解类型被自动继承。如果用户在当前类中查询这个元注解类型并且当前类的声明中不包含这个元注解类型，那么也将自动查询当前类的父类是否存在**Inherited**元注解，这个动作将被重复执行知道这个标注类型被找到，或者是查询到顶层的父类。

4. **@Retention**——指明了该Annotation被保留的时间长短。RetentionPolicy取值为SOURCE,CLASS,RUNTIME。

###Java内建注解###

Java提供了三种内建注解。

1. **@Override**——当我们想要复写父类中的方法时，我们需要使用该注解去告知编译器我们想要复写这个方法。这样一来当父类中的方法移除或者发生更改时编译器将提示错误信息。  

2. **@Deprecated**——当我们希望编译器知道某一方法不建议使用时，我们应该使用这个注解。Java在javadoc 中推荐使用该注解，我们应该提供为什么该方法不推荐使用以及替代的方法。

3. **@SuppressWarnings**——这个仅仅是告诉编译器忽略特定的警告信息，例如在泛型中使用原生数据类型。它的保留策略是SOURCE（译者注：在源文件中有效）并且被编译器丢弃。

我们来看一个java内建注解的例子参照上边提到的自定义注解。

    package com.journaldev.annotations;

	import java.io.FileNotFoundException;
	import java.util.ArrayList;
	import java.util.List;

	public class AnnotationExample {

	public static void main(String[] args) {
	}

	@Override
	@MethodInfo(author = 'Pankaj', comments = 'Main method', date = 'Nov 17 2012', revision = 1)
	public String toString() {
		return 'Overriden toString method';
	}

	@Deprecated
	@MethodInfo(comments = 'deprecated method', date = 'Nov 17 2012')
	public static void oldMethod() {
		System.out.println('old method, don't use it.');
	}

	@SuppressWarnings({ 'unchecked', 'deprecation' })
	@MethodInfo(author = 'Pankaj', comments = 'Main method', date = 'Nov 17 2012', revision = 10)
	public static void genericsTest() throws FileNotFoundException {
		List l = new ArrayList();
		l.add('abc');
		oldMethod();
	}

	}

相信这个例子可以不言自明并能展示在不同场景下的应用。

###Java注解解析###
我们将使用反射技术来解析java类的注解。那么注解的RetentionPolicy应该设置为RUNTIME否则java类的注解信息在执行过程中将不可用那么我们也不能从中得到任何和注解有关的数据。

	package com.journaldev.annotations;

	import java.lang.annotation.Annotation;
	import java.lang.reflect.Method;

	public class AnnotationParsing {

	public static void main(String[] args) {
		try {
			for (Method method : AnnotationParsing.class
					.getClassLoader()
					.loadClass(('com.journaldev.annotations.AnnotationExample'))
					.getMethods()) {
				// checks if MethodInfo annotation is present for the method
				if (method
						.isAnnotationPresent(com.journaldev.annotations.MethodInfo.class)) {
					try {
						// iterates all the annotations available in the method
						for (Annotation anno : method.getDeclaredAnnotations()) {
							System.out.println('Annotation in Method ''
									+ method + '' : ' + anno);
						}
						MethodInfo methodAnno = method
								.getAnnotation(MethodInfo.class);
						if (methodAnno.revision() == 1) {
							System.out.println('Method with revision no 1 = '
									+ method);
						}

					} catch (Throwable ex) {
						ex.printStackTrace();
					}
				}
			}
		} catch (SecurityException | ClassNotFoundException e) {
			e.printStackTrace();
		}
	}

	}

运行上面程序将输出：

	Annotation in Method 'public java.lang.String com.journaldev.annotations.AnnotationExample.toString()' : @com.journaldev.annotations.MethodInfo(author=Pankaj, revision=1, comments=Main method, date=Nov 17 2012)
	Method with revision no 1 = public java.lang.String com.journaldev.annotations.AnnotationExample.toString()
	Annotation in Method 'public static void com.journaldev.annotations.AnnotationExample.oldMethod()' : @java.lang.Deprecated()
	Annotation in Method 'public static void com.journaldev.annotations.AnnotationExample.oldMethod()' : @com.journaldev.annotations.MethodInfo(author=Pankaj, revision=1, comments=deprecated method, date=Nov 17 2012)
	Method with revision no 1 = public static void com.journaldev.annotations.AnnotationExample.oldMethod()
	Annotation in Method 'public static void com.journaldev.annotations.AnnotationExample.genericsTest() throws java.io.FileNotFoundException' : @com.journaldev.annotations.MethodInfo(author=Pankaj, revision=10, comments=Main method, date=Nov 17 2012)

这就是该教程的全部内容，希望你可以从中学到些东西。

作者：Pankaj Kumar  
译者：赵亮  
原文：[http://www.javacodegeeks.com/2012/11/java-annotations-tutorial-with-custom-annotation.html](http://www.javacodegeeks.com/2012/11/java-annotations-tutorial-with-custom-annotation.html "http://www.javacodegeeks.com/2012/11/java-annotations-tutorial-with-custom-annotation.html")