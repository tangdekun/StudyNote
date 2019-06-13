# Kotlin中类的序列化

> Android中对象序列化主要有两种方式：Serializable和Parcelable。
>
> Serializable实际是使用Java的放射机制对对象做序列化和反序列化，其性能远低于使用Parcelable。但是使用Java写的Parcelable有点繁琐，需要我们实现Parcelable接口，手写很多模板代码。

## Kotlin中序列化Parcelable有三种方式：

### 1.手动实现`Parcelable`接口方法

```kotlin

data class Music(var name: String?,
                 /**歌曲名*/
                 var path: String?,
                 /**路径*/
                 var album: String?,
                 /**所属专辑*/
                 var artist: String?,
                 /**艺术家(作者)*/
                 var size: Long,
                 /**文件大小*/
                 var duration: Int,
                 /**时长*/
                 var pinyin: String
        /**歌曲名的拼音，用于字母排序*/
) : Parcelable {
    constructor(source: Parcel) : this(
            source.readString(),
            source.readString(),
            source.readString(),
            source.readString(),
            source.readLong(),
            source.readInt(),
            source.readString()
    )

    override fun describeContents() = 0

    override fun writeToParcel(dest: Parcel, flags: Int) = with(dest) {
        writeString(name)
        writeString(path)
        writeString(album)
        writeString(artist)
        writeLong(size)
        writeInt(duration)
        writeString(pinyin)
    }

    companion object {
        @JvmField
        val CREATOR: Parcelable.Creator<Music> = object : Parcelable.Creator<Music> {
            override fun createFromParcel(source: Parcel): Music = Music(source)
            override fun newArray(size: Int): Array<Music?> = arrayOfNulls(size)
        }
    }
}
```

### 2.使用插件实现`Parcelable`接口方法

​     **步骤：**

1. 在AS的*Settings*中选择*Plugins*，然后搜索*Parcelable Code Generator(for kotlin)*，安装即可     

2. 在需要序列化的类中使用`Alt+Insert`快捷键会弹出提示框

3. 图片走一波
 ![图片走一波](C:\Users\tangdekun\Desktop\usage.gif)
  > 图片来自：https://www.jianshu.com/p/e4bb5e23d9f5
代码如上

### 3.使用`@Parcelize`注解实现序列化

### Kotlin Parcelable特性

Kotlin1.1.4新增了Parcelable特性，添加注解*@Parcelize*就可以自动生成相应的Parcelable代码。

  上面的例子实现如下：

```
@Parcelize
data class User(val id: String, val name: String) : Parcelable
```

相当的简洁！

**使用**

1、kotlin：>=1.1.4

2、app的build.gradle添加插件

```
apply plugin: 'kotlin-android-extensions'
```

3、app的build.gradle下android配置添加

```
androidExtensions {
    experimental = true
}
```

> 如果`@Parcelize`无法导入，Build几次就好

代码：

```kotlin
@SuppressLint("ParcelCreator")// 用于处理 Lint 的错误提示
@Parcelize
data class Video(var name: String?,
                 /**歌曲名*/
                 var path: String?,
                 /**路径*/
                 var album: String?,
                 /**所属专辑*/
                 var artist: String?,
                 /**艺术家(作者)*/
                 var size: Long,
                 /**文件大小*/
                 var duration: Int,
                 /**时长*/
                 var pinyin: String
        /**歌曲名的拼音，用于字母排序*/
) : Parcelable
```

