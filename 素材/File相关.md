# · QFile     
# · QDir
# · QFileInfo   

### QFile
```C++ {.line-numbers}
    /// 新建文件
    // 判断文件是否存在
    if(file.exists(fileName))
        return;
    // 创建文件
    if(!file.open(QIODevice::WriteOnly|QIODevice::Text))
        return;
    // 文件是否可读
    if(!file.isReadable())
    {
        LOGINFO("File is not read!");
    }
    // 关闭文件
    file.close();



    enum OpenModeFlag {
        NotOpen = 0x0000,                   // 设备没有打开  
        ReadOnly = 0x0001,                  // 设备以只读方式打开,这时无法写入
        WriteOnly = 0x0002,                 // 设备以只写方式打开，这时无法读取
        ReadWrite = ReadOnly | WriteOnly,   // 设备以读/写方式打开
        Append = 0x0004,                    // 设备以附加模式打开，所有数据将写入到文件的末尾
        Truncate = 0x0008,                  // 如果可能，设备在打开前会被截断，设备先前的内容都将丢失
        Text = 0x0010,                      // 当读取时，行结尾终止符会转换为'\n';当写入时，行结尾终止符被转换为本地编码，例如在win32上时"/r/n"
        Unbuffered = 0x0020                 // 绕过设备的缓冲区
    };
```

### QDir
``` C++ {.line-numbers}
    /// 新建目录
    QDir dir;
    // 判断文件是否存在
    if(!dir.exists(filePath))
    {   
        // 如果不存在则创建目录
        if(!dir.mkpath(filePath))
        {
            return ;
        }
        QMessageBox::information(this,"提示","创建成功");
    }


    /// 遍历目录下的子目录
    //遍历filePath下的子文件
    QDir dir(filePath);
    dir.setFilter(QDir::Dirs|QDir::NoDotAndDotDot);

    // dir.entryInfoList() 遍历文件夹
    foreach(QFileInfo info,dir.entryInfoList())
    {
        // 显示子目录下的文件的名称
        LOGINFO(info.fileName());
    }

    enum Filter { Dirs        = 0x001,                          // 列出目录
                  Files       = 0x002,                          // 列出文件
                  Drives      = 0x004,                          // 列出逻辑驱动器名称，该枚举变量在Linux/Unix中将被忽略
                  NoSymLinks  = 0x008,                          // 不列出符号链接
                  AllEntries  = Dirs | Files | Drives,          //其值为Dirs | Files | Drives，列出目录、文件、驱动器及软链接等所有文件
                  TypeMask    = 0x00f,

                  Readable    = 0x010,                          // 列出当前应用有读权限的文件或目录
                  Writable    = 0x020,                          // 列出当前应用有写权限的文件或目录
                  Executable  = 0x040,                          // 列出当前应用有执行权限的文件或目录
                  PermissionMask    = 0x070,

                  Modified    = 0x080,                          // 列出已被修改的文件，该值在Linux/Unix系统中将被忽略
                  Hidden      = 0x100,                          // 列出隐藏文件
                  System      = 0x200,                          // 列出系统文件

                  AccessMask  = 0x3F0,

                  AllDirs       = 0x400,                        // 列出所有目录，不对目录名进行过滤
                  CaseSensitive = 0x800,
                  NoDot         = 0x2000,                       // 不列出.文件，即指向当前目录的软链接
                  NoDotDot      = 0x4000,                       // 不列出..文件
                  NoDotAndDotDot = NoDot | NoDotDot,            // 不列出文件系统中的特殊文件.及..

                  NoFilter = -1
                };
```

### QFileInfo
``` C++ {.line-numbers}

    QFileInfo info("admin.xml");
    // 文件名
    QString strName = info.fileName();
    // 文件的相对路径，不带名称和xml
    QString path = info.path();
    // 文件创建的时间，以2019-06-26 13:55:11 的格式输出
    QString buildTime = info.created().toString("yyyy-MM-dd hh:mm:ss");
    // 文件最后读取的时间，以2019-06-26 13:55:11 的格式输出
    QString endOpen = info.lastRead().toString("yyyy-MM-dd hh:mm:ss");
    // 文件的绝对路径
    QString absoluteFilePath = info.absoluteFilePath();
    // 文件的基本名称
    QString baseName = info.baseName();
    // 文件的大小
    qint64 fileSize = info.size();
    // 文件的后缀
    QString fileSuffix = info.suffix();
    // 


```