### 配置

在没有进入项目时配置为全局的。Maven可以在这里配置Maven home directory、User settings file、Local repository（选中后边Override）。

#### 新文件头部

settings -> Editor -> FIle and Code Templates ->inlucdes -> File Header

~~~
/**
  *@Description: ${TODO}
  *@PackageName: ${PACKAGE_NAME}
  *@Author: CSC
  *@Create: ${YEAR}-${MONTH}-${DAY} ${TIME}
  *@Version: 1.0
  */ 
~~~

#### 新方法注释

settings -> Editor -> Live Templates 

可以新建一个Live Group，再新建一个template

Abbreviation *		Description methodDes(随意)

Expand with Enter

template 

~~~
*
 * @Description: $TODO$
 $param$
 $return$
 * @Author: $USER$ 
 * @Date: $date$ $time$
 **/
~~~

之后Edit variables

其中param的值可以使用提供的方法methodParameters()，但是这种生成的是一个数组的形式，这里选用如下的方式将每个变量都单独列出来。将如下的值放在Default value中。

~~~
  groovyScript("if(\"${_1}\".length() == 2) {return '';} else {def result=''; def params=\"${_1}\".replaceAll('[\\\\[|\\\\]|\\\\s]', '').split(',').toList();for(i = 0; i < params.size(); i++) {if(i==0){result+='* @param: ' + params[i] + ': '}else{result+='\\n' + ' * @param: ' + params[i] + ': '}}; return result;}", methodParameters()); 
~~~

return同样有methodReturnType()，这里仍然使用如下的方式，原因同上。

~~~
groovyScript("def returnType = \"${_1}\"; def result = '* @return: ' + returnType; return result;", methodReturnType());
~~~

