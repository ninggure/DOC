# pm2
## 快速启动
```
    pm2 start ./bin/www --watch
```

## 常用命令
### 启动
参数说明:

*   --watch: 监听应用目录的变化，一旦发生变化，自动重启。如果是要精确监听、不监听的目录，最好设置配置文件
*   -i(-- instances): 启动多少个实例，可用于负载均衡。如果-i 0或者-i max,则根据当前机器核书确定实例数目
*   --ignore-watch: 排除监听的目录/文件,可以是特定的文件名，也可以说是正则。比如--ignore-watch="test node_modules "some scripts""
*   -n(--name): 应用的名称。查看应用信息的时候可以用到
*   -o(--output) <path>: 标准输出日志文件的路径
*   -e(--error) <path>: 错误输出日志文件的路径

### 启动
```
    pm2 start app.js
    pm2 start npm --name 'next' -- start
    pm2 start npm --name 'next' -- run start
```

## 启动指令

### 重启
```
    pm2 restart app.js
```

### 停止
停止特定的应用，可以先通过pm2 list获取应用的名字(--name指定的)或者进程id
```
    pm2 stop app_name|app_id
```
停止所有应用
```
    pm2 stop all
```
### 查看进程状态
```
    pm2 list
```


## 配置
```
    {
        "apps": [{
            "name": "xx",           //项目名
            "script": "xx",         //执行文件
            “cwd”: "xx",            //根目录
            "args": "",             //传递给脚本的参数
            "interpreter": "",      //指定的脚本解释器
            "interpreter_args": "", //传递给解释器的参数
            "watch": true,          //是否监听文件变动然后重启
            "ignore_watch": [],     //不用监听的文件
            "exec_mode": "cluster_mode",  //应用启动模式，支持fork和cluster模式
            "instances": 4,          //用于启动实例个数，仅在clugster模式有效,默认为fork;或者max
            "max_memory_restart": 8， //最大内存限制数，超出自动重启
            "error_file": "",          //错误日志文件
            “out_file”: "",            //正常日志文件
            "merge_logs": true         //设置追加日志而不是新建日志
            "log_date_format": "YYYY-MM-DD HH:mm:ss"  //指定日志文件的事件格式
            "min_uptime": "60s"        //应用运行少于时间被认为是异常启动
            "max_restarts": 30,        //最大异常重启次数，min_uptime运行时间重启次数；
            "autorestart": true,        //默认为true,发生异常的情况下自动重启
            "cron_restart": "",                         // crontab时间格式重启应用，目前只支持cluster模式;
            "restart_delay": "60s",                     // 异常重启情况下，延时重启时间
            "watch_options": {
                "usePolling": true  // 轮询监听
            
            },
            //环境参数，当前指定为生产环境 process.env.NODE_ENV
            "env": {
                 "NODE_ENV": "production",                // 环境参数
                 "REMOTE_ADDR": "爱上大声地"               // 
            },
            "env_dev": {
                "NODE_ENV": "development",              // 环境参数，当前指定为开发环境 pm2 start app.js --env_dev
                "REMOTE_ADDR": ""
            },
            "env_test": {                               // 环境参数，当前指定为测试环境 pm2 start app.js --env_test
                "NODE_ENV": "test",
                "REMOTE_ADDR": ""
            }
        }


    }
```
