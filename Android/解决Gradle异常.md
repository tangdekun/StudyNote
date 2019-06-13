# 解决Gradle异常

> 避免gradle异常的原则:
>
> 1.开发过程中不要升级AS版本;
>
> 2.开发过程中不要随便更换`gradle`的版本号.无论是AS提醒升级,还是在项目的`build.gradle`中提示升级，一定要坚持不更新。坚持就是胜利

## 问题

1. 如果不幸遇到`Gradle`问题怎么办？
2. 如果提示你`Gradle`与当前AS版本不一样，必须升级AS，你又不想升级怎么办

##  解决方案

 方法很简单，直接还原

-  在当前AS中新建一个新项目，然后复制`gradle-wrapper.properties`文件到问题项目中，替换原先的设置。

- 更换问题项目中`build.gradle`的`com.android.tools.build:gradle`与新建项目的版本号一致

  ```
   dependencies {
          classpath 'com.android.tools.build:gradle:3.5.0-beta01'
         
  
      }
  ```

  

至此，问题基本解决

