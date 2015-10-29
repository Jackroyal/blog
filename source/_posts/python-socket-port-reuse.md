title: python学习笔记--socket编程端口复用
date: 2015-03-18 08:47:02
tags:
- python
- socket
- Linux
categories:
- python学习笔记
---
最近在学习socket编程,遇到一个问题:
我先bind一个端口后,如果通过ctr+c关闭进程.接下来执行程序的时候,就会提示`socket.error: Address already in use`.
<!-- more -->
照例google一番,找到[这个](http://blog.csdn.net/xl_xunzhao/article/details/3130037).博主说的情况和我的一样.
我还在stackoverflow上找到[这个](http://stackoverflow.com/questions/4465959/python-errno-98-address-already-in-use).
修改后代码如下:
```
    server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    # 下面这行是关健
    server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)

    server_socket.bind(('', PORT))
    server_socket.listen(5)
```
然后就搞定了.

下面的代码是socket编程敲得两个小例子,基于socket的聊天小程序都是别人的东西,只是练习一下,源地址在本文最后.
## 多线程版本服务器端程序server2.py
```
# !/usr/bin/env python
# -*- coding:utf-8-*-
__author__ = 'chen'


import socket,sys
from thread import *

HOST = ''
PORT = 8888

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
print 'socket created'

try:
    s.bind((HOST, PORT))
except socket.error, msg:
    print 'bind failed.Error code: |||%S Message: %s' %(str(msg[0]), msg[1])
    sys.exit()


print 'socket bind complete'

s.listen(10)
print 'socket now listening'

def clientthread(conn):
    conn.send('welcome to the server.Type something and hit enter\n')

    while True:
        data = conn.recv(1024)
        reply = 'ok...' + data
        if not data:
            break
        conn.sendall(reply)
    conn.close()

while 1:
    conn, addr = s.accept()
    print 'connected with %s : %s' %(addr[0],str(addr[1]))

    start_new_thread(clientthread, (conn,))

s.close()
```
直接telnet连接socket,就可以调试
```
telnet localhost 8888
```
## 改良版,带广播的聊天室程序server3.py
```
# !/usr/bin/env python
# -*- coding:utf-8-*-
__author__ = 'chen'

import socket, select

def broadcast_data(sock, message):
    for socket in CONNECTION_LIST:
        if socket != server_socket != sock:
            try:
                socket.send(message)
            except msg:
                socket.close()
                CONNECTION_LIST.remove(socket)

if __name__ == "__main__":
    CONNECTION_LIST = []
    RECV_BUFFER = 4096
    PORT = 5000
    server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    server_socket.bind(('', PORT))
    server_socket.listen(5)

    CONNECTION_LIST.append(server_socket)
    print "chat server started on port %s" % str(PORT)

    while 1:
        read_sockets, write_sockets,error_sockets = select.select(CONNECTION_LIST, [], [])

        for sock in read_sockets:
            if sock == server_socket:
                sockfd,addr = server_socket.accept()
                CONNECTION_LIST.append(sockfd)
                print "client (%s, %s) connected" % addr

                broadcast_data(sockfd, "[%s:%s] entered room\n" % addr)

            else:
                try:
                    data = sock.recv(RECV_BUFFER)
                    if data:
                        print  "[%s:%s]" % (str(sock.getpeername()), data)
                        broadcast_data(sock, "[%s:%s]" % (str(sock.getpeername()), data))
                except msg:
                    print msg
                    broadcast_data(sock, "client (%s, %s) is offline "% addr)
                    print "client (%s,%s) is offline " % addr
                    sock.close()
                    CONNECTION_LIST.remove(sock)
                    continue
    server_socket.close()
```
## 客户端程序client3.py
```
# !/usr/bin/env python
# -*- coding:utf-8-*-
__author__ = 'chen'

import socket,select,string,sys

def prompt():
    sys.stdout.write('[you]')
    sys.stdout.flush()

if __name__ == "__main__":
    if(len(sys.argv)<3):
        print 'usage: python client3.py hostname port'
        sys.exit()

    host = sys.argv[1]
    port = int(sys.argv[2])

    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.settimeout(2)

    try:
        s.connect((host, port))
    except:
        print 'unable to connect'
        sys.exit()

    print 'connected to remote host. start sending messages'
    prompt()

    while 1:
        rlist = [sys.stdin, s]

        read_list, write_list, error_list = select.select(rlist, [], [])

        for sock in read_list:
            if sock == s:
                data = sock.recv(4096)
                if not data:
                    print '\nDisconnected from chat server'
                    sys.exit()
                else:
                    sys.stdout.write(data)
                    prompt()

            else:
                msg = sys.stdin.readline()
                s.send(msg)
                prompt()
```
---
# 参考文献
1 [Python Socket 网络编程](http://www.cnblogs.com/hazir/p/python_socket_programming.html)
2 [Python Socket 编程——聊天室示例程序](http://www.cnblogs.com/hazir/p/python_chat_room.html)
