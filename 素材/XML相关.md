# · QDomDocument
# · QRegExp


### QDomDocument 需要进行的一些配置与添加的头文件
1.  在工程配置文件 .pro中添加
    QT += core gui xml

2.  在.cpp文件中添加头文件
    #include <QtXml/QDomDocument>
    #include <QtXml/QDomNode>
    #include <QtXml/QDomNodeList>
    #include <QtXml/QDomText>
    #include <QFile>
    #include <QString>

3.  再在.cpp文件中添加:
    #pragma comment(lib,"QT5Xmld.lib")


### QDomDocument xml读操作
``` C++ {.line-numbers}
/// 具体的业务背景，将xml解析出来后，用QTreeeWidget 显示出来，并只展开三层目录 
// 读取 Xml
void MenuTree::ReadingXml()
{
    //若要更新，则先清除treeWidget，然后将header隐藏
    ui->treeWidget->clear();
    ui->treeWidget->header()->hide();

    QFile file("../../../data/admin.xml");
    if(!file.open(QFile::ReadOnly))
        return ;
    if(!file.isReadable())
    {
        LOGINFO("File is not read!");
        return ;
    }

    QDomDocument doc("../../../data/admin");
    QString errorMsg = "";
    int errorLine = 0;
    int errorColumn = 0;
    // 获取xml文件中的内容，如果没有获取到xml的内容，这个函数会返回报错信息
    if(!doc.setContent(&file,true,&errorMsg,&errorLine,&errorColumn))
    {
        LOGINFO(errorMsg+"     "+QString::number(errorLine)+"     "+QString::number(errorColumn));
        file.close();
        return;
    }
    file.close();

   QString layerString = "行政区目录";
   QTreeWidgetItem* layerItem = new QTreeWidgetItem(this->ui->treeWidget, QStringList(layerString));
   layerItem->setIcon(0,QIcon("../../../img/menu.png"));

   QDomElement root = doc.documentElement();
   QDomNodeList n = root.childNodes();
   TraversalTree(n,layerItem);

   //展开树至镇一级
   int depth = 3;
   ExpendDepth(layerItem,depth);
}

// 递归遍历xml 的节点树，将需要提取的信息提取出来，并放入QTreeWidgetItem 中
void MenuTree::TraversalTree(QDomNodeList n,QTreeWidgetItem* parentItem)
{
    if(n.isEmpty())
    return;

    for(int i = 0;i<n.size();i++)
    {
        QDomElement e = n.at(i).toElement();
        QStringList str1(e.attribute("名称")+"("+e.attribute("代码")+")");
        QTreeWidgetItem* item = new QTreeWidgetItem(parentItem,str1);
        item->setIcon(0,QIcon("../../../img/menu.png"));
        if(e.childNodes().size())
        {
            TraversalTree(e.childNodes(),item);
        }
    }
}

// 递归遍历图层树，只展开深度为depth 以上的节点
void MenuTree::ExpendDepth(QTreeWidgetItem* parentItem,int depth)
{
    if(depth == 0)
        return;

    int itemCount = parentItem->childCount();
    if(itemCount == 0)
        return;

    ui->treeWidget->expandItem(parentItem);

    for(int i = 0; i <itemCount; i++ )
    {
        ExpendDepth(parentItem->child(i),depth-1);
    }
}
```
**注意:**
**1.当给QTreeWidgetItem 的节点添加图标，layerItem->setIcon(0,QIcon("../../../img/menu.png"))**
    **若图标占用空间太大，则不会显示，最好选取图标占用空间不大的。**

### QDomDocument xml写操作
``` C++ {.line-numbers}
/// xml 写入
//改变manage这个xml中的信息
void OpenPrj::ChangeManageXML()
{
    QString filePath = "../../../ProjectFile/Projects/";

    QFile file("../../../ProjectFile/manage.xml");
    if(!file.open(QIODevice::WriteOnly|QIODevice::Truncate))
        return ;

    QDomDocument doc;
    QDomElement root = doc.createElement("工程文件配置");
    doc.appendChild(root);

    //遍历filePath下的子文件
    QDir dir(filePath);
    dir.setFilter(QDir::Dirs|QDir::NoDotAndDotDot);

    foreach(QFileInfo info,dir.entryInfoList())
    {
        LOGINFO(info.fileName());

        QString strName = info.fileName();
        QString path = info.path();
        QString buildTime = info.created().toString("yyyy-MM-dd hh:mm:ss");
        QString endOpen = info.lastRead().toString("yyyy-MM-dd hh:mm:ss");

        AddElement(doc,root,strName,path,buildTime,endOpen);
    }

    QTextStream out(&file);
    doc.save(out,4);
    file.close();
}

void OpenPrj::AddElement(QDomDocument doc,QDomElement root,QString strName,QString path,QString buildTime,QString endOpen)
{
    path += ("/"+strName + ".xml");

    QDomElement element;
    element = doc.createElement("工程文件");
    element.setAttribute("名称",strName);
    element.setAttribute("路径",path);
    element.setAttribute("创建时间",buildTime);
    element.setAttribute("最后打开时间",endOpen);

    // 将 element 添加到 root 的子节点
    root.appendChild(element);
}
```
**注意：**
**1. file.open(QIODevice::WriteOnly|QIODevice::Truncate)，无法往xml插入元素，所以将xml中的信息全部清除，然后重新写入**


### QRegExp xml进行正则表达式解析
``` C++ {.line-numbers}
    /// 将xml输出为字符串，利用正则表达式进行匹配，将取到的结果存入list中
    QString stringXml = fields.ToXml().c_str();
    // 匹配的表达式(正则表达式)
    QRegExp regx("<Name>(.*)</Name>");
    int pos(0);
    // 这句是设置贪婪算法，默认是minimal，只要匹配到，就匹配结束。设为false,则匹配到最大最多值
    regx.setMinimal(true);

    QList<QString> listField;
    while ((pos = regx.indexIn(stringXml, pos)) != -1)
    {
        QString temp = regx.capturedTexts().at(0);
        temp = temp.mid(6, temp.length() - 13);
        //将提取的字段用 list 存储
        listField.append(temp);
        pos += regx.matchedLength();
    }
```
**注意:**
**1.在进行正则表达式匹配时，一定要设置regx.setMinimal(true)**
**2. QRegExp的用法:**
**//企图从位置偏移为零(默认值)出找到一个匹配的字符处str，返回第一个匹配点的位置position，如果没有匹配则返回-1**
**int QRegExp::indexIn ( const QString & str, int offset = 0, CaretMode caretMode = CaretAtZero ) const**