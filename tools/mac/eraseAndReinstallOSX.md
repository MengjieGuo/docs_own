## 重装步骤

1、reboot system, when start enter `option + cmd + r`.
2、选择磁盘管理工具，erase HD盘分区，注意不要清除 OS X baseSystem所在分区。
3、左上，退出磁盘管理工具。
4、选择在线重装系统。

## 未能创建用于APFS安装的预启动卷宗

大致原因有

1、erase 磁盘时没有清理干净。使用 `diskutil cs delete` 命令进行清理。然后重新执行重装步骤。
2、系统与OS X版本兼容问题。
