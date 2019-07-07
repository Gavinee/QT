# QTreeView

### QTreeView 常用
``` C++ 
    // 设置不能对树视图内容进行修改
    ui->treeView->setEditTriggers(QAbstractItemView::NoEditTriggers);

    QList<GsVectorLayer*> LayerIsSelect = LayerList();

    treeModel = new QStandardItemModel();
    ui->treeView->setModel(treeModel);
    for(int i = 0;i<LayerIsSelect.size();i++)
    {
        // 创建 QStandardItem 对象
        QStandardItem* item = new QStandardItem((QString)LayerIsSelect[i]->Name());

        // 设置 item 可以勾选
        item->setCheckable(true);

        // 判断该图层是否可选
        if(LayerIsSelect[i]->isEnableSelect())
        {
            // 设置 item 为已经勾选
            item->setCheckState(Qt::Checked);
        }
        else
        {
            // 设置 item 为未勾选
            item->setCheckState(Qt::Unchecked);
        }

        // 往QStandardItemModel 中添加QStandardItem
        treeModel->appendRow(item);
    }

    enum EditTrigger {
        NoEditTriggers = 0,     // 不能对表格内容进行修改
        CurrentChanged = 1,     // 任何时候都能对单元格修改
        DoubleClicked = 2,      // 双击单元格
        SelectedClicked = 4,    // 单击已选中的内容
        EditKeyPressed = 8,     // 
        AnyKeyPressed = 16,     // 按下任意键就能修改
        AllEditTriggers = 31    //以上条件全部包括
    };

    enum CheckState {
        Unchecked,              // 不选中
        PartiallyChecked,       // 不确定是否选中
        Checked                 // 选中
    };
```
