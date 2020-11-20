---
title: 一台机器最多可以维持多少TCP连接
date: 2020-11-20 21:06:06
tags:
categories: other
---


<code>server code</code>

    import socket  

    if __name__ == '__main__':
        mySocket = socket.create_server(('10.4.204.21', 2000), reuse_port=False)
        while True:
            pass


<code>client code</code>  

    import socket
    if __name__ == '__main__':
        i = 0
        while True:
            mySocket = socket.create_connection(('10.4.204.21', 2000))
            if mySocket is None:
                print("create client connect failed")
                break
            else:
                print("create client connection succeed")
                i = i + 1
                print('current connection count ', i)  

可以创建连接，但存在几个现象  
1. 输出的connection count 是 129，用netstat -nat | grep -i "2000" | wc -l查看连接数是131，不匹配
2. 最大连接数理论上受资源限制，ulimit -n能够得到系统设置的tcp最大连接上限是1024，这也远远大于131  
3. create_connection最终是timeout，为什么会timeout，是资源不足了吗？
4. client进程关掉后，大量的tcp连接仍然没有断掉，要好好了解下TCP状态图。  

