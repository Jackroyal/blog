title: python学习笔记--一个简单聊天室的实现
date: 2015-03-26 21:33:35
tags:
- python
- socket
categories:
- python学习笔记
---
最近项目真多,一个接一个的失之交臂,全部都错过了.最近状态有些不好,容易胡思乱想.
这是来自书上的一个python聊天程序,我照着敲了一遍,然后给扩展了一下,加了多个房间和创建选择房间的功能,写了好久好久,感觉都拖了一个星期了.
下一步是做一个gui,恩,那将是我的第一个gui程序.
<!-- more -->
# 先贴一下代码 server5.py
```
# !/usr/bin/env python
# -*- coding:utf-8-*-
__author__ = 'chen'

from asyncore import  dispatcher
from  asynchat import async_chat
import socket,asyncore

PORT = 5005  # 设定程序的端口号
NAME = 'testchat'  # 给服务器一个名称

class EndSession(Exception):pass

class CommandHandler:
    """
    类似标准库中cmd.Cmd的简单命令处理程序
    """
    # 如果输入的命令,那么就返回unknown命令
    def unknown(self, session, cmd):
        session.push('Unknown command: %s \r\n' % cmd)
    
    def handle(self, session, line):
        if not line.strip():
            return
        parts = line.split(' ', 1)
        cmd = parts[0]
        try:
            line = parts[1].strip()
        except IndexError:line = ''
        meth = getattr(self, 'do_' + cmd, None)
        try:
            meth(session, line)
        except TypeError:
            self.unknown(session, cmd)
# 这个类是聊天房间的类,继承上面的类是为了继承执行命令的功能
class ChatRoom(CommandHandler):
    def __init__(self, name, server):
        self.server = server
        self.name = name
        self.sessions = []

    def add(self, session):
        self.broadcast(session.name + ' has entered the room %s\r\n' % self.name)
        session.push('you can type "h" for help\r\n')
        ## 因为后面要将用户挪动房间,所以必须保存每个用户的session,这样才能挪动和删除
        self.server.users[session.name] = session
        self.sessions.append(session)

    def remove(self, session):
        try:
            self.sessions.remove(session)
        except: pass # 如果此处的sessions为空或者已经不存在,会出错,此处不上报

    def broadcast(self, line):
        # 广播,只广播到当前房间
        for session in self.sessions:
            session.push(line)

    def do_say(self, session, line):
        # 说话
        self.broadcast(session.name + ":" + line + '\r\n')

    def do_login(self, session, line):
        # login,其实是实现改名字的功能,懒得去改函数名了
        name = line.strip()
        if not name:
            session.push('please enter a name\r\n')
        elif name in self.server.users.keys():
            session.push('The name %s is taken\r\n' % name)
            session.push('please try again\r\n')
        else:
            session.server.users[name] = session.server.users.pop(session.name)
            session.name = name
            session.enter(self)
            self.do_list(session, '')
            session.push('type "select name" to choose one room\r\n')
    # 查看当前房间有哪些人
    def do_look(self, session, line):
        session.push('the following are in this room:\r\n')
        for other in self.sessions:
            session.push(other.name + "\r\n")
    # 查看当前在线的用户,所有房间的用户
    def do_who(self, session, line):
        session.push('the following are logged in:\r\n')
        for name in self.server.users:
            session.push(name + '\r\n')
    # 查看当前所有的房间
    def do_list(self, session, line):
        session.push('the room list is below\r\n')
        session.push('   '.join(self.server.rooms) + '\r\n')
    # 选择房间
    def do_select(self, session, line):
        name = line.strip()
        if not name:
            session.push('please enter a room name\r\n')
        elif name in self.server.rooms.keys():
            session.enter(self.server.rooms[name])
            self.broadcast(' %s ,welcome to join %s\r\n'% (session.name, name))
    # 输出帮助
    def do_h(self, session, line):
        session.push('you can use this commands:\r\n1,who to see who is on this server(online and offline)\r\n2,'
        'list to see how many room are avaliable\r\n3,look to see who are in this room\r\n4,login to login online and '
        'change a name\r\n5,create to create a new room')
    # 创建新房间
    def do_create(self, session, line):
        name = line.strip()
        if not name:
            session.push('please enter a name\r\n')
        elif name in self.server.rooms.keys():
            session.push('The room name %s is taken\r\n' % name)
            session.push('please try again\r\n')
        else:
            ChatRoom(name, self.server)
            session.server.rooms[name] = ChatRoom(name, self.server)
            session.push("the room %s create successful\r\n" % name)
            session.enter(session.server.rooms[name])
# 每个用户回话,这个是重点类
class ChatSession(async_chat):
    def __init__(self, server, sock):
        async_chat.__init__(self, sock)
        self.server = server
        self.set_terminator('\r\n')
        self.data = []
        self.name = 'visitor' + str(len(server.users))# 初始化用户名,用visitor1之类来表示
        self.room = self.server.main_room
        self.enter(self.server.main_room)

    def enter(self, room):
        try:
            cur = self.room
        except AttributeError: pass
        else: cur.remove(self)
        self.room = room
        room.add(self)
    # 当用户有输入的时候
    def collect_incoming_data(self, data):
        self.data.append(data)
    # 当用户输入终止符的时候
    def found_terminator(self):
        line = ''.join(self.data)
        self.data = []
        try:
            self.room.handle(self, line)
        except EndSession:
            self.handle_close()
    # 关闭用户回话
    def handle_close(self):
        async_chat.handle_close(self)
        # self.enter()

# 服务器类,这个也是重点类
class ChatServer(dispatcher):
    def __init__(self, port, name):
        dispatcher.__init__(self)
        self.create_socket(socket.AF_INET, socket.SOCK_STREAM)
        # 端口复用
        self.set_reuse_addr()
        self.bind(('', port))
        self.listen(5)
        self.sessions = {}
        self.name = name
        self.users = {}
        self.rooms = {}
        # 新建一个房间hall,因为每个初始登陆的用户没有房间,但是操作是依赖与ChatRoom类的,所以给一个初始默认的房间
        self.main_room = ChatRoom('hall', self)
        self.rooms[self.main_room.name] = self.main_room

    def handle_accept(self):
        conn,addr = self.accept()
        ChatSession(self, conn)
        print 'connection attempt from ', addr[0]

if __name__ == "__main__":
    print 'server start'
    s = ChatServer(PORT, NAME)
    try:
        asyncore.loop()
    except KeyboardInterrupt: print
```
效果如图所示
![运行效果](http://ww3.sinaimg.cn/large/692869a3gw1eqjhaekoz0j20ii0h5q62.jpg)
