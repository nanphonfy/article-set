>我们一般会将文件放入专门的服务器或集群中，复杂点的如FastDFS、HDFS集群等。简单点的直接通过ftp操作磁盘文件。本文重点在于演示如何玩excel，所以直接把应用和文件放入同一台机器。
我们先来观测一张效果图，并引出需求：
![整体图from nanphonfy](http://img.blog.csdn.net/2018021217271248?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTmFucGhvbmZ5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
依照线路站点的静态信息，生成起止站点的邻接矩阵表，以XX站到XX站多少票价作为矩阵值，并按照票价的颜色规则生成样式，以方便从视觉上观测其跳变结果。我们如何实现图示需求呢?可用java的poi操作excel文件来实现。
需要数据：①线路编码、站点编码、站点名，②站点->站点:票价；
>![局部图from nanphonfy](http://img.blog.csdn.net/20180212173155736?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTmFucGhvbmZ5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
思路：
①首先，生成行标题栏，即前三行标题；    
②其中，前三行三列的信息为静态（固定值），后续行列为动态值（从DTO中赋值）；  
③建立线路->样式的HashMap，为线路所属区域赋颜色；  
④建立雏形后需按线路号对横向&竖向标题栏合并单元格；  
⑤还需考虑横向&竖向标题栏的冻结滚动问题；  
⑥垂直标题栏和内容区域，从DTO对象合成；  
⑦每个DTO对象需包含竖向标题栏属性和值序列；  
⑧竖向标题栏使用规则1，值序列使用规则2；  
⑨依次操作单元格，即可生成如上所示的excel文件。  


```java  

public void saveExcel(String title, List<List<StationExcelDTO>> result, List<String> headers, String docPath, String fileName) {
    OutputStream stream = null;
    // 文件所在上级路径
    String filePath = docPath + fileName;
    File excelParentPath = new File(docPath);
    File excelFile = new File(filePath);

    try {
        // 先判断excel所在目录存不存在，不存在则先创建一个
        if (!excelParentPath.exists()) {
            excelParentPath.mkdirs();
        }
        // 判断文件存不存在，没有则创建一个
        if (!excelFile.exists()) {
            excelFile.createNewFile();
        }
        //创建写文件流
        stream = new FileOutputStream(filePath);
		//调用生成excel的方法
        exportExcel(title, headers, result, stream);
    } catch (FileNotFoundException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    }finally {
        if (stream != null) {
            try {
                stream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

FastDFS+Nginx(单点部署)事例
https://www.cnblogs.com/cnmenglang/p/6251696.html