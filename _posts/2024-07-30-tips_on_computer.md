## 1.磁盘分区、合并操作  
- win10 system: diskpart  
    >- cmd以管理员身份打开
    >- 输入diskpart
    >- list disk (列出电脑的所有硬盘)
    >- select disk "no."  (选择要操作的硬盘)
    >- list partition  (列出硬盘内的分区)
    >- select partition "no."  (选择要操作的分区)
    >- delect partition override  (删除所选分区, ps:操作失败)
    >- clean    (清除所选分区, ps:实际清除了所有分区，太有效了)  
- Mac system: disktool
    >- 启动>disktool
    >- 分区:+/-

## 2.Alist 挂载阿里云盘
- 驱动:阿里云盘open
- 用户:/任意名
- 刷新令牌:opentoken  
只要填这三样即可。

