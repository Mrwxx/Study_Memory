## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	复制单级文件夹
&nbsp;  &nbsp;  &nbsp;  &nbsp;单级文件夹，即该文件夹中只有文件，没有文件夹。
<br>


```java
package File使用;

import java.io.*;

public class 复制单级文件夹 {
    public static void main(String[] args) throws IOException {
        //文件夹File对象
        File srcFolder = new File("D:\\MR.WANG\\FILE");
        String srcFolderName = srcFolder.getName();

        //目的路径File对象
        File destFolder = new File("Student", srcFolderName);

        //判断目的路径是否存在
        if(!destFolder.exists()){
            destFolder.mkdir();
        }

        //获取src目录所有File
        File[] srcFiles = srcFolder.listFiles();

        //遍历
        for(File srcFile : srcFiles){
            String srcFileName = srcFile.getName();
            File destFile = new File(destFolder, srcFileName);

            copyFile(srcFile, destFile);
        }

    }

    private static void copyFile(File srcFile, File destFile) throws IOException {
        BufferedInputStream bis = new BufferedInputStream(new FileInputStream(srcFile));
        BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream(destFile));

        byte[] bys = new byte[1024];
        int len;
        while((len=bis.read(bys))!=-1){
            bos.write(bys, 0, len);
        }

        bos.close();
        bis.close();
    }
}
```

<br>

## 二. 复制多级文件夹

&nbsp;  &nbsp;  &nbsp;  &nbsp;多级文件夹，一个文件夹嵌套和多个文件夹。
<br>

```java
import java.io.*;

public class 复制多级文件夹 {
    public static void main(String[] args) throws IOException {
        File srcFile = new File("D:\\MR.WANG\\FILE");

        File destFile = new File("Student");

        copyFolder(srcFile, destFile);
    }

    private static void copyFolder(File srcFile, File destFile) throws IOException {
        //判断是否是目录
        if(srcFile.isDirectory()){
            //是目录，创建新目录
            String srcFileName = srcFile.getName();
            File newFolder = new File(destFile, srcFileName);
            //如果该目录不存在
            if(!newFolder.exists()){
                newFolder.mkdir();
            }

            //获取源目录里的File对象
            File[] fileArray = srcFile.listFiles();
            //对每个File对象递归调用copyFolder操作
            for(File file : fileArray){
                copyFolder(file, newFolder);
            }
        }
        //如果不是目录，直接复制文件
        else{
            File newFile = new File(destFile, srcFile.getName());
            copyFile(srcFile, newFile);
        }
    }

    private static void copyFile(File srcFile, File destFile) throws IOException {
        //字节流缓冲
        BufferedInputStream bis = new BufferedInputStream(new FileInputStream(srcFile));
        BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream(destFile));

        byte[] bys = new byte[1024];
        int len;
        while((len=bis.read(bys))!=-1){
            bos.write(bys, 0, len);
        }
        bos.close();
        bis.close();
    }
}
```


