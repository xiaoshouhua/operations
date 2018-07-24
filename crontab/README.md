
#### 什么是Crontab?
    Crontab是一个用于设置周期性执行任务的工具.类似生活中的闹钟

#### Crontab案例应用
    1.每分钟需要执行一个脚本进行系统Health Check 
    2.每天凌晨对过去的一天系统数据进行统计 
    3.每周对系统日志进行备份切割 
    4.每个月对系统关键数据进行备份
    
#### Crontab相关概念
    1.被周期性执行的任务我们称为Cron Job 
    2.周期性执行的任务列表我们称为Cron Table
    
#### Crontab服务安装并检查
    1.安装cron服务
        
        方式一:
            - yum install vixie-cron
            - yum install crontabs
        
        方式二:
            - yum clear all 
            - yum update
            - yum install -y cronie crontabs
            
    2.检查cron服务
        
        2.1.检查Crontab工具是否安装: crontab -l
        
        2.2.检查crond服务是否启动: service crond status
 
#### Crontab的基本组成
        
    1.CROND -- 系统服务,每分钟都会从配置文件刷新定时任务
        
    2.配置文件 -- 文件方式设置定时任务
        
    3.配置工具crontab -- 用于调整定时任务
        
#### Crontab的配置文件格式 
    从左至右分别代表:
        
        分:0-59
        时:0-23
        日期:1-31
        月份:1-12
        星期:0~7(0或者7表示星期天)
        
    常用示例:
        
        1.每晚的21:30重启apache
            30 21 * * * service httpd restart
                
        2.每月1、10、22日的4:45重启apache
            45 4 1,10,22 * * service httpd restart
            
        3.每月1到10日的4:45重启apache
            45 4 1-10 * * service httpd restart
                
        4.每隔两分钟重启apache
            */2 * * * * service httpd restart
            1-59/2 * *　* * service httpd restart
                
        5.晚上11点到早上7点之间,每隔一小时重启apache
            0 23-7/1 * * * service httpd restart
        
        6.每天18:00到23:00之间每隔30分钟重启apache
            0/30 18-23 * * *　service httpd restart
            0-59/30 18-23 * * *　service httpd restart
                
            
    小结:
        - *表示任何时候都匹配
        - 可以用"A,B,C"表示A或者B或者C时执行命令
        - 可以用"A-B"表示A到B之间时执行命令
        - 可以用"*/A"表示每A分钟(小时等)执行一次命令

#### Crontab工具的使用
    
    1.查看某用户的计划任务列表 
        
    2.修改某用户的计划任务
        
    3.查看生成的最终计划任务文件 
        - /var/spool/cron
            
#### Crontab的配置文件
    .全局（系统）配置文件 
        - /etc/crontab 
        - 注意格式
         
    .Root配置文件 
        - 只有root用户才会把这些内容载入到计划任务表中

#### Crontab的日志
    /var/log/cron文件保存着cron的任务执行记录

#### Crontab常用错误
        
    .环境变量（获取不到对应的环境变量）
        
    .命令行双引号中使用%时,未加反斜线\
        
    .第三和第五个域之间执行的是"或"操作
        四月的第一个星期日早晨1时59分执行a.sh
          failed:
            59 1 1-7 4 0 /root/a.sh
        
          success:
            59 1 1-7 4 * test `date +\%w` -eq 0 && /root/a.sh
        
    .分钟设置误用
        两个小时运行一次
          failed:
            * 0,2,4,6,8,10,12,14,16,18,20,22 * * * date
             
          success:
            0 */2 * * * date
            
    .其他:
        Crontab中最小只能设置到每分钟执行一个命令,如果想每半分钟执行某个命令怎么做到？
        -- 通过shell脚本的sleep命令配合Crontab即可完成这一功能
        -- date && sleep 0.5s && date     
          