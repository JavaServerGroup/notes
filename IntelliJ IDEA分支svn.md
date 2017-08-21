### 目录
1. <a href="#为何使用分支">为何使用分支</a>
2. <a href="#创建分支">创建分支</a>
3. <a href="#合并分支">合并分支</a>
4. <a href="#总结">总结</a>


### 【<a name="为何使用分支" id="为何使用分支" ><font color=black>为何使用分支</font></a>】
    项目开发中经常会遇到这种情况，项目中功能开发完上线后，新的需求又来了，风风火火的在项目里开发，    
    突然有一天测试说有个很致命的bug需要紧急修改上线，完蛋了，原来的代码都不知道改成什么样子了，    
    新开发的代码如果还原几天的工作白干了。
    这种情况分支显的尤为重要。
  
### 【<a name="创建分支" id="创建分支" ><font color=black>创建分支</font></a>】
    在IntelliJ IDEA中选中项目右击-->Subversion --> branch or Tag ...     
    选择Repository Location:需要创建分支的项目    svn://192.168.10.245:83/java/wap-palmchat
    选择Any Location 分支的位置和名字    svn://192.168.10.245:83/java/wap-branches/wap-palmchat_20160504
    分支的名字建议加上日期，方便区分    
         
    切换到分支      
    选中项目右键Subversion --> Update Directory  勾选Update/Switch to specific url   
    选择URL 为对应的  svn://192.168.10.245:83/java/wap-branches/wap-palmchat_20160504   
    点击 OK   
        
    查看是否切换成功 项目右键Subversion --> Relocate 查看路径是不是分支的路径    
    现在可以在分支上开发新功能，并提交了。  
     
### 【<a name="合并分支" id="合并分支" ><font color=black>合并分支</font></a>】
    当有bug要修改时，切换回主项目    
    选中项目右键Subversion --> Update Directory  勾选Update/Switch to specific url     
    选择URL 为对应的  svn://192.168.10.245:83/java/wap-palmchat    
    查看是否切换成功   
    
    修改bug 打包上线，都不会影响，因为新开发的功能在分支上，现在我们合并到主分支吧。
    
    选中项目右键 Subversion --> Integrate Directory      
    source 1 选择主分支    svn://192.168.10.245:83/java/wap-palmchat    
    source 2 选择分支   svn://192.168.10.245:83/java/wap-branches/wap-palmchat_20160504    
    点击OK   
    会合并分支修改的代码，如果有冲突，需要手动解决了。   
    其实当我们从分支切换回主分支时，会提示有哪些文件改变了。
    
### 【<a name="总结" id="总结" ><font color=black>总结</font></a>】
    分支其实就是项目的复制品，有两个相同的项目，修改bug在主分支上做，增加新功能在分支上写。它们相互不影响。
