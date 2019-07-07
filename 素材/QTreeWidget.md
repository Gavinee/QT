# QTreeWidget

### QTreeWidget 常用
``` C++
    // 隐藏header 
    ui->treeWidget->header()->hide();

    // 把列的最小宽度设成了一个比较大的数，然后横向滚动条就出来了
    ui->treeWidget -> header() -> setMinimumSectionSize(500);

    // 设置水平和竖直方向上的滚动条，滚动条需要时才显示 ( Qt::ScrollBarAsNeeded )
    ui->treeWidget->setHorizontalScrollMode(QAbstractItemView::ScrollPerPixel);
    ui->treeWidget->setHorizontalScrollBarPolicy(Qt::ScrollBarAsNeeded);
    ui->treeWidget->setVerticalScrollMode(QAbstractItemView::ScrollPerPixel);
    ui->treeWidget->setVerticalScrollBarPolicy(Qt::ScrollBarAsNeeded);

    enum ScrollBarPolicy {
        ScrollBarAsNeeded, 	//滚动条需要时才显示
        ScrollBarAlwaysOff,	//滚动条一直关闭
        ScrollBarAlwaysOn	//滚动条一直打开
    };
```

### 递归QTreeWidget中的元素
``` C++ {.line-numbers}
// 递归QTreeWidget中的元素
// 参数1 QTreeWidgetItem* parentItem   :  此参数为树形结构的一个节点
// 参数2 int depth                     :  递归的深度
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

### 往QTreeWidget的各个节点中添加图标
``` C++ 
   QString layerString = "工程树";
   QTreeWidgetItem* layerItem = new QTreeWidgetItem(this->ui->treeWidget, QStringList(layerString));
   layerItem->setIcon(0,QIcon("../../../img/EagleEyeSet.png"));
```

### 树的节点全部展开
``` C++ {.line-numbers}
    // 新建一个节点
    QString layerString = "图层";
    QTreeWidgetItem* layerItem = new QTreeWidgetItem(this->ui->treeWidget, QStringList(layerString));
    // 遍历map，map的key为图层，map的value为图层的OID列表
    Q_FOREACH(GsVectorLayer* pLayer, m_mdqueryResultMap.keys())
    {
        QString tempString = (QString)pLayer->Name();
        QTreeWidgetItem *LayerItemChild = new QTreeWidgetItem(LayerItem, QStringList(tempString));
        /// 将子节点添加到树的孩子节点中
        LayerItem->addChild(LayerItemChild);
    }
    // 将树的所有节点全部展开
    this->ui->treeWidget->expandAll();
```