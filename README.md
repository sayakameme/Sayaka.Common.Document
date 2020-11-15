# C#一行代码实现系列原创分享

### 觉得对您有帮助，帮忙推荐加关注

## Nuget Package下载 ##
[https://www.nuget.org/packages/Sayaka.Common/](https://www.nuget.org/packages/Sayaka.Common/)

## 目录

* 此页面会更新链接，保存此页面即可

**初步计划完成以下内容**

1. 窗口程序
      1. [最小化到通知区域](https://www.cnblogs.com/easynetwork/p/13867659.html)
      1. [设置开机启动](https://www.cnblogs.com/easynetwork/p/13867682.html) 
      1. [关闭窗口前弹出确认窗口](https://www.cnblogs.com/easynetwork/p/13867720.html)
      1. [跨线程设置控件值，免去`InvokeRequired`通用方法1](https://www.cnblogs.com/easynetwork/p/13867963.html)
      1. [跨线程设置控件值，免去`InvokeRequired`通用方法2](https://www.cnblogs.com/easynetwork/p/13870558.html)
      1. [跨线程获取控件值，结合`BeginInvoke`和`EndInvoke`使用，以`DataGridView`为例](https://www.cnblogs.com/easynetwork/p/13872377.html)
      1. [遍历窗口所有控件，通过类和名称模糊筛选](https://www.cnblogs.com/easynetwork/p/13872404.html)

1. 运行CMD程序
      1. 无窗口运行CMD程序
      2. 注册OCX控件

1. `App.Config`配置文件读写
      1. 读`App.Config`参数
      1. 写`App.Config`参数
      1. 绑定控件`Text`到`App.Config`参数

1. 注册表读写
      1. 读注册表
      1. 写注册表

1. 文本文件读写
      1. 读文本文件
      1. 写文本文件     

1. Win32API封装
      1. 通过`ClassName`和`WindowText`模糊查找所有顶级窗口
      1. 通过`ClassName`和`WindowText`模糊查找窗口内所有子控件
      1. 发送消息
      1. 模拟点击其他程序窗口按钮
      1. 在其他程序窗口上模拟按键
      1. 正常关闭其他窗口
      1. 强制关闭其他窗口
      1. 获取窗口属性
      1. 修改窗口属性
      1. 获取窗口位置和大小
      1. 修改窗口位置和大小
      1. 其他

1. 先列到这里，未完待续……
      

## 主要功能
- 实现`Winform`程序最小化后，隐藏任务栏显示，在通知区域显示。左键点击通知区域图标，可以弹出菜单，包含`开机启动`和`退出程序`，可以根据需求进行定制。

## 一行代码
```
private void Form1_Load(object sender, EventArgs e)
{
    this.ExAddNotifyIcon();
}
```

## 代码封装


```C#
/// <summary>
/// 添加通知图标，左键点击切换窗口显示，右键点击弹出菜单
/// </summary>
/// <param name="form"></param>
/// <param name="tipText"></param>
/// <param name="timeout"></param>
/// <param name="tipIcon"></param>
/// <returns></returns>
public static NotifyIcon ExAddNotifyIcon(this Form form, string tipText = "程序最小化至后台运行，点击图标显示程序", int timeout = 3000, ToolTipIcon tipIcon = ToolTipIcon.Info)
{
    NotifyIcon notifyIcon = new NotifyIcon();
    notifyIcon.Icon = form.Icon;
    notifyIcon.Visible = true;
    notifyIcon.Text = form.Text;
    notifyIcon.BalloonTipText = form.Text;
    notifyIcon.MouseClick += (sender, args) =>
    {
        if (args.Button == MouseButtons.Left)
        {
            if (!form.Visible)
            {
                form.Show();
                form.WindowState = FormWindowState.Normal;
                form.TopMost = true;
                form.TopMost = false;
            }
            else
            {
                form.WindowState = FormWindowState.Minimized;
                form.Hide();
            }
        }
    };
    notifyIcon.ContextMenu = new ContextMenu(new MenuItem[]
    {
        new MenuItem("开机启动", (sender, args) => { ((MenuItem) sender).Checked = form.ExSwitchRunOnSystemStart(); })
        {
            Checked = form.ExIsRunOnSystemStart()
        },
        new MenuItem("退出程序", (sender, args) => { form.Close(); }),
    });
    form.SizeChanged += (sender, args) =>
    {
        if (form.WindowState == FormWindowState.Minimized)
        {
            form.Hide();
            notifyIcon.ShowBalloonTip(timeout, form.Text, tipText, tipIcon);
        }
    };
    form.FormClosed += (sender, args) => { notifyIcon.Dispose(); };
    return notifyIcon;
}
```


## 声明
- 文章作者：易几施
- 博客地址：https://www.cnblogs.com/easynetwork/
- 版权声明：文章遵循[创作共用版权协议](http://creativecommons.org/licenses/by-nc/3.0/deed.en)，署名、非商业、保持一致，转载请注明出处。
