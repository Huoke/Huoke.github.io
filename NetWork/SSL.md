# SSL_get_error
# NAME
SSL_get_error - 获取 TLS/SSL I/O操作的结果码
# 语法
```c
 #include <openssl/ssl.h>
 int SSL_get_error(const SSL *ssl, int ret);
```
# 描述
SSL_get_error() 返回结果值(适用于"C"的switch语句), 返回的值是先前在SSL上调用SSL_connect()、SSL_accept()、SSL_do_handshake()、SSL_read()、SSL_peek()、或者SSL_write()的结果代码。
这个值必须通过SSL_get_error()的参数返回。

除了ssl和ret之外, SSL_get_error() 会检查当前线程的OpenSSL错误队列。
因此, SSL_get_error() 必须在执行TLS/SSL I/O操作的同一线程中使用, 并且不应在这两个线程之间出现其他OpenSSL函数调用。
在尝试TLS/SSL I/O操作之前, 当前线程的错误队列必须为空, 否则SSL_get_error()将无法可靠地工作。

# 返回值
- SSL_ERROR_NONE
SSL/TLS I/O 操作完成。这个结果值将被返回, 当且仅当ret > 0 时返回此结果。

- SSL_ERROR_ZERO_RETURN
TLS/SSL连接已关闭。如果协议版本是ssl3.0或更高版本，则仅当协议中发生关闭警报时，即连接已干净关闭时，才会返回此结果代码。
请注意，在这种情况下，SSL_ERROR_ZERO_RETURN并不一定表示底层传输已关闭。

- SSL_ERROR_WANT_READ, SSL_ERROR_WANT_WRITE
操作没有完成；稍后应该再次调用相同的TLS/SSL I/O函数。到那时，底层BIO有可供读取的数据(如果SSL_get_error()返回码时SSL_ERROR_WANT_READ)或者
允许写入数据(SSL_ERROR_WANT_WRITE), 则一些TLS/SSL协议的进展将会发生，即至少一个TLS/SSL记录将被读取或者写入。
请注意: 重试可能会再次导致SSL_ERROR_WANT_READ或SSL_ERROR_WANT_WRITE条件。对于可能需要的迭代次数没有固定的上限，直到进度在应用程序协议级别可见为止。

对于套接字BIOs (例如: 当使用SSL_set_fd() 时)，可以在底层socket上使用select()或者poll来检测什么时候应该重试TLS/SSL I/O函数。

⚠ 任何TLS/SSL I/O函数都可能导致 SSL_ERROR_WANT_READ, SSL_ERROR_WANT_WRITE。特别是SSL_read()或者SSL_peek()希望写入数据SSL_write()希望
读取数据。主要原因是因为TLS/SSL握手可能在协议期间的任何时候发生(由客户端或服务器启动); SSL_read()、SSL_peek()、和SSL_write()将处理任何挂起的握手。

- SSL_ERROR_WANT_CONNECT, SSL_ERROR_WANT_ACCEPT
