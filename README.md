# My Awesome Book



Redis的服务器进程就是一个事件循环，这个循环中的文件事件负责接收客户端的命令请求， 以及向客户端发送命令回复，
而时间事件则负责执行像serverCron函数这样需要定时运行的函数。 因为服务器在处理文件 事件时可能会执行写命令， 使得一些内容被追加到aof_buf缓冲区里面， 所以在服务器每次结束 一个事件循环之前， 它都会调用flushAppendOnlyFile函数， 考虑是否需要将aof_buf缓冲区中的内容写入和保存到AOF文件里面，
This file file serves as your book's preface, a great place to describe your book's content and ideas.
