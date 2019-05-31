# Android Studio编译jar架包必看

> 公司Unity项目和Android项目交互需要Android端打包一个Jar架包，之前是Eclipse工程的，可以直接导出Jar包 。但是现在Android Studio已经成为Android主流开发工具，所以也有必要把Library项目迁移到AS中。AS中的确没有Jar导出功能，但是通过编译过后的library项目的build中是有jar包的。

## 一.先决条件

      项目是Library项目；
    
      即module中是library插件而不是 Application `apply plugin: 'com.android.library'`

##   二.gradle代码

 因为gradle在构建的时候，会对这个模块的class文件先打包成classes.jar并放在`build/intermediates/packaged-classes/release/`。于是我只需要把它拷出来，并重命名就可以了。

```groovy
task makeJar(type: Copy) {
    //删除旧的jar包
    delete 'build/libs/mysdk.jar'
    //原地址
    from('build/intermediates/packaged-classes/release/')
    //导出jar包的地址
    into('build/libs/')
    //包含的jar包
    include('classes.jar')
    //重命名jar包为mysdk
    rename ('classes.jar', 'mysdk.jar')
}
makeJar.dependsOn(build)
```

代码解释：

1. 这是一个任务，功能是完成一个数据拷贝工作
2. 编译jar的路径：`build/libs/`
3. 把`build/intermediates/packaged-classes/release/`下面的文件拷贝到`build/libs/`；
4. 对于不同的AS 版本，gradle构建class.jar的路径不同。笔者AS版本3.2，class.jar的路径`build/intermediates/packaged-classes/release/`
5. 其他可能路径`build/bundles/release`，`build/intermediates/classes/`可以去尝试，如果实在否不可以，建议把Build文件夹下面都搜索一下，找到classes.jar所在文件夹，替换上面的`from`的地址

## 三.注意事项

**1.jar只能打包java文件，不包括项目引入的其他so文件，aar文件和jar文件；**
**2.可以使用aar的情况下尽量不使用jar**
**3.Unity与Android的最佳交互方式：工程A用于处理数据，引用所有操作数据的架包；Library工程B用于与Unity交互，通过ContentProvider与G工程A进行数据操作；其中Unity中要引入ContentProvider相关架包**