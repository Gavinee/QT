# QTableView

### QTableView 常用 
``` C++ {.line-numbers}
    // 设置不能对 tableView 内容进行修改
    this->ui->tableView->setEditTriggers(QAbstractItemView::NoEditTriggers);

    // 表头
    fieldModel = new QStandardItemModel();

    // 多图层的 oid列表 遍历
    Q_FOREACH(GsVectorLayer* pLayer, m_dqueryResultMap.keys())
    {
        // 将图层与OID以树型结构展现出来
        TransfromTree(pLayer,m_dqueryResultMap[pLayer]);

        // 初始化显示第一个图层的第一个 OID 信息在 listview 中
        if(m_dqueryResultMap.keys().at(0)==pLayer)
        {
            // getVectorLayer() 将OID列表与图层设置为当前的OID列表与图层
            getVectorLayer(m_dqueryResultMap[pLayer],pLayer);
        }
    }

    // 清除 Mode l中的内容
    fieldModel->clear();
    //设置水平的第1,2个表头为字段名称
    fieldModel->setHorizontalHeaderItem(0, new QStandardItem(QObject::tr("字段名称")));
    fieldModel->setHorizontalHeaderItem(1, new QStandardItem(QObject::tr("字段名称")));
    //将表头加入到Model中
    this->ui->tableView->setModel(fieldModel);
    //设置tableView 的第一、二列的宽度
    this->ui->tableView->setColumnWidth(0, 210);
    this->ui->tableView->setColumnWidth(1, 210);

    //将字段与值在 listview 中显示，并在Item中显示颜色
    //listField 为字段的list
    for (int i = 0; i < listField.size(); i++)
    {
        fieldModel->setItem(i, 0, new QStandardItem(listField[i]));
        fieldModel->setItem(i, 1, new QStandardItem(valueField[i]));
        if (i % 2 == 0)
        {
            //设置Model中的item(i,0)的背景的RGB值RGB(174, 238, 238)
            fieldModel->item(i, 0)->setBackground(QColor(174, 238, 238));
            fieldModel->item(i, 1)->setBackground(QColor(174, 238, 238));
        }
        else
        {
            //设置Model中的item(i,0)的背景的RGB值RGB(255, 245, 238)
            fieldModel->item(i, 0)->setBackground(QColor(255, 245, 238));
            fieldModel->item(i, 1)->setBackground(QColor(255, 245, 238));
        }
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
```