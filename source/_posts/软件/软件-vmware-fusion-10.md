---
title: VwareFusion安装使用
comments: true
toc: false
date: 2018-03-28 10:26:19
updated:
tags: 
- VMware 
- 虚拟机
categories: 
- 软件
banner: vmware安装1.png
---
## 需求
研究开发环境，写教程，需要mac和windows版，可能涉及linux。在windows上用过vmware workstation、vmbox、hyper-V。
## 选型
- BootCampAssistant
    ![BootCampAssistant](BootCampAssistant.png)
    ###### 优缺点
    - mac自带。有向导，按向导下载镜像，体验好
    - 下载镜像速度极慢，导致失败，可能是从美国微软下载的。也可能不愿让用户跑到竞争对手那里:smirk:
    - 双系统，并非虚拟机。看使用场景，双系统更干净，注意力不会被分散，开机更快。而我想搭建多种OS环境，方便截图，所以最终选择的还是虚拟机。

- Parallels Desktop
    ###### 优缺点
    - 推荐和使用者众多，基本就它跟vmware竞争mac桌面虚拟化软件
    - 深度整合，能把windows程序停靠在docker栏，尽力打造最好的体验。不过也有人不喜欢这种混淆
    - 据说效率不错，感觉不到延迟
    - 正版较贵，破解版难找

- Vmware Fusion 
    ###### 优缺点
    - 老牌虚拟化厂商，使用者多，不少人都用过win上vmware workstation，不需要额外的学习成本
    - 虚拟机备份是否能跨系统导入没有尝试
    - 正版较便宜

## 下载
[Vmware官网](https://www.vmware.com/)  
vmware产品很多，有win上许多个人用户用过的workstation，有vSphere ESXi之类操作系统层面或管理虚拟机用的，看大家mac上提到fusion，我就下吧  
左侧主菜单/download/找到fusion。中国区下载前要登录vmware账号，我不想注册了，由于下面下载win10镜像的经历，**所以尝试挂上代理访问英文官网，神奇的事发生了：显示了软件列表和大大的
download按钮。然鹅，点击又跳转到了登录页，绝望**😶。虽然注册个账号应该就让下载了，但还是不想留信息，于是发现上个页面右侧：什么viewMyDownloadHistory\
ProductInfo\Documentation\community\downloadFreeTrial, 嗯？有开源社区版吗 上个页面见到过但不会用。**downloadFreeTrial**免费试用，咦（表情变得猥琐起来），
点进去果然就是期待的下载页了，选择try fusion 10 pro
**[Download VMware Fusion 10](https://www.vmware.com/products/fusion/fusion-evaluation.html)**  
下载速度白天5mb/s,代理，很愉快的下完了。
### 激活
有能力的尽量支持正版，160刀，国区价格没看。有网友双十一2折优惠赶上活动什么的就比较便宜啦  
下面放出网上搜到的激活码，测试可用，如果以后不能使用请自行搜索：  
**FG3TU-DDX1M-084CY-MFYQX-QC0RD**

## 安装
1. 打开下载好的dmg安装映像，根据提示双击或拖动到application文件夹  
![](vmware安装1.png)  
    1. 如果你在第一步卡住了  
       需要做**系统设置-安全性与隐私 允许从一下位置下载的应用，🔘单选框调整为`允许任何来源`**  
       这一步与本软件没关系，但是是安装所有软件的前提条件，不做这一步的话可能在第1步打开dmg安装程序时就卡住了
       解决办法参考这篇文章：[Mac任何来源不见了，Mac任何来源消失了怎么办？](https://jingyan.baidu.com/article/afd8f4de8e55e734e286e92a.html)
       此问题产生的原因：处于安全性和希望未来appstore各端大统一的考虑，macOSX禁止安装非appstore来源的软件，10.14之前可以直接在设置中更改。
       **10.14更新后在设置中压根看不到允许任何来源这个选项了😅** 其实是官方隐藏起来，需要进入终端命令行先关闭安全锁定再把那个选项调处来，mac app store
       缺少很多主流大型软件，具体原因有兴趣的可以搜搜，软件商实现功能要权限，苹果想统一各端和安全性考虑不给权限。  
       这一步不做的话还遇到过一个隐藏的坑：一些软件安装后打开提示软件包已损坏。其实不是软件包有问题。
       
2. 当然选择完整功能的pro版本
![](vmware安装2.png)
3. 可以先选择试用，进入软件后再用激活码激活  
![](vmware安装3.png)
![](vmware安装4.png)
4. 允许软件载入
点击允许。安装软件结束时提示*要写入一些东西，请到设置-安全中允许balabala（记不清了大意这样）*,这时根据对话框提示跳转到
`设置-安全性与隐私`点击`允许`按钮，如图所示 ）
原因类似于windows的用户账户控制安全机制，第三方应用运行或更改系统设置时需要管理员授权。如果出现问题参考是否做到了上面的1.1步骤
![](vmware 安装5.png)
5. 完成。可以立即运行。launchpad中可以看到应用图标。  
![](vmware 安装6.png)



## 使用 新建windows/macOS/Linux虚拟机
新建虚拟机比安装物理机简单，镜像下载和物理机安装统一放到另一篇文章  
➡️{% post_link 软件/软件-Windows系统下载和U盘安装 Windows系统下载与安装 %}


## 新建windows
假设已经有windows安装ISO镜像了，没有的参考👆链接的文章
0. 打开软件  
1. 选择安装方法  
    ![](vmware使用1.png)  
    *介绍下其他几个选项的意思：*
    - 迁移您的PC：把当前系统数据做成虚拟机文件，未来将来在另一台电脑上从vmware导入
    - 从恢复分区中安装macOS：macOS的备份工具time machine，从备份文件恢复
    - 导入现有虚拟机：导入别人制作好的虚拟机（也就是上面的迁移您的PC做出来的东西），虚拟机就是一个文件夹，包好vmware自己的硬盘二进制文件和配置文件
    - 从Boot Camp安装：猜测 用mac自带的bootcamp下载win镜像
    - 创建自定虚拟机：
    - 在远程服务器上创建虚拟机：没试过，比较好奇，猜测远程服务器需要下载linux版本的vmware
2. 选择映像路径  
选择过一次后软件会记住路径方便下次新建
![](vmware使用2.png)  
![](vmware使用3.png)  
3. 去掉勾选**☑使用快捷安装**
![](vmware使用4.png)
快捷安装只是帮你新建了个账户，如果有密钥的话自动激活，我想绝大多数人用的都是盗版，账户可以进系统再建立，所以去掉勾选
4. 默认UEFI引导
![](vmware使用5.png) 
有兴趣的搜索BIOS和UEFI区别，物理机上BIOS很早 兼容性好，UEFI最近几年出现是bios的代替品，但一些电脑上需要专门在主板设置调成bios才能安装成功，
虽然UEFI先进许多，毕竟底层的东西，结果都是开开机，平时体验不出什么区别。
5. (选做)摘要中修改虚拟机文件路径
![](vmware使用5-5.png) 
虚拟机硬盘文件默认路径是  `icloud/资料库/虚拟机/[创建的虚拟机名字]`，一个windows10系统安装完14G，如果装软件再使用一段时间很可能几十，
icloud会自动上传，所以没有同步需求的话注意该路径  
我在我的用户根目录下新建了一个文件夹，方便管理，因为删除那个文件就等于删除那个虚拟机  
随着安装系统和添加软件，可以观察到Windows 10 x64.vmwarevm 这个文件会不断增大  
可以把这个文件复制分发给其他人，其他人导入就可以获得跟你一样的虚拟机
![](vmware使用5-6.png)
其它的建议，虚拟机毕竟多套了一层不会太顺滑，劲量安装在固态硬盘  
5. 创建完成
显示了虚拟机和虚拟机的设置窗口
![](vmware使用6.png) 
如果这一步没有成功会提示无法创建虚拟机错误，跟镜像或设置有关  
6. windows系统安装、镜像下载、激活
具体参考 {% post_link 软件/软件-Windows系统下载和U盘安装 Windows系统下载与安装 %}
*坑：点击虚拟机播放按钮后命令行报错提示没找到xx东西，以为引导失败，软件设置问题，结果过了两分钟自己进入到正常安装界面了*😂
7. windows虚拟机安装完成
![](vmware使用8.png) 

## 其它功能
8. vmware tools
共享剪贴板，主虚机文件拖动互传，更低的输入延迟。安装完系统后才可以安装，第一次载入安装非常慢十分钟，可能是我虚拟机内存小的缘故，也可能vmware
在线下载这个软件用时较长。
![](vmware使用9.png) 
9. Unity模式
虚拟机单独窗口-点击右上角图标  
windows系统和软件融入到了macOS的程序坞（docker),两个系统融为一体！虚拟机体验像本地一样无缝接入！  
不过也有人不喜欢这种混淆
![](vmware使用10.png) 
10. 快照管理
方便地来回时间穿梭！快照确保你能及时撤销到历史，在进行风险操作时十分有用，比如你不小心安装了一个流氓软件：下载了许多软件，劫持
了你的浏览器主页，包含病毒木马。回到过去，什么都没发生的时候。  
可以看到新建的快照和快照之间的时间关系。可以从快照建立克隆。
![](vmware使用12.png)
![](vmware使用11.png)


## 管理和新建和小贴士
1. 新建和删除虚拟机。系统程序坞（docker）vmware图标右键或软件设置进入**虚拟机资源库**
![](vmware使用7.png) 
2. 当前虚拟机各项设置。软件设置或虚拟机窗口各项图标，硬盘，光驱，网络等等
![](vmware使用6.png) 
3. 更加流畅。安装在固态硬盘，配置高的可以分配更多的内存比如4G或以上
4. 更加好用。记得最后vmware tools
5. 清理快照以减少磁盘占用


## license
原创文章 欢迎转载



