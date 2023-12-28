---
creation date: 2023-12-25 20:43 
---
#🌲长青  #计算机网络

## 环境搭建
[BYO Linux installation](https://stanford.edu/class/cs144/vm_howto/vm-howto-byo.html)

[CS144 Lab：Lab0 – LRL52 的博客](https://lrl52.top/992/cs144-lablab-0/)

## 联网试试看
[CS144-Lab0 | Misaka's blog](https://www.misaka-9982.com/2023/02/18/CS144-Lab0/)

在终端使用已经安装好的工具建立 TCP 连接：`telnet cs144. keithw. org http`。

试试看输入下面的内容后会发生什么？😯（记得一起复制最后的换行）


```
GET /hello HTTP/1.1
Host: cs144.keithw.org
Connection: close

```

```
GET /lab0/aqpower HTTP/1.1
Host: cs144.keithw.org
Connection: close

```

![image-20231228203955619](https://jgox-image-1316409677.cos.ap-guangzhou.myqcloud.com/blog/image-20231228203955619.png)

## 使用 c 语言标准库提供好的 TCP 接口

这里的任务是实现一个 `get_URL` 函数。

关键是弄明白TCP连接的过程，需要先根据服务器主机地址建立TCP连接，然后再发送HTTP请求报文。

```c++
void get_URL(const string &host, const string &path)
{
    cerr << "Function called: get_URL(" << host << ", " << path << ")\n";
    TCPSocket tcpSocket;
    Address address(host, "http");
    // 先建立TCP连接！
    tcpSocket.connect(address);
    cout << "connect!" << endl;
    string data = "GET " + path + " HTTP/1.1\r\nHost: " + host + "\r\nConnection: close\r\n\r\n";
    // 连接建立后，你可以通过发送符合 HTTP 协议规范的请求来获取路径所对应的内容。路径信息是在构建 HTTP
    // 请求时放在请求头中的一部分。
    tcpSocket.write(data);
    string buffer;
    while (!tcpSocket.eof()) {
        tcpSocket.read(buffer);
        cout << buffer;
    }
}
```

## 实现可靠字节流的抽象

这里的任务是，完善类的定义和接口实现。

可以先从接口实现入手，有需要什么状态信息字段的话再去类里添加。

为了更好的性能，这里采用`deque`存储字节流缓存区的`buffer`。

### 头文件定义

```c++
class ByteStream
{
  protected:
    uint64_t capacity_;
    // Please add any additional state to the ByteStream here, and not to the
    // Writer and Reader interfaces.
    std::deque<char> buffer;
    bool is_closed_ = false;
    bool is_error_ = false;
    uint64_t poped_len_ = 0;
    uint64_t pushed_len_ = 0;

  public:
    explicit ByteStream(uint64_t capacity);

    // Helper functions (provided) to access the ByteStream's Reader and Writer
    // interfaces
    Reader &reader();
    const Reader &reader() const;
    Writer &writer();
    const Writer &writer() const;
};

class Writer : public ByteStream
{
  public:
    void push(const std::string &data); // Push data to stream, but only as much as
                                                // available capacity allows.

    void close();     // Signal that the stream has reached its ending. Nothing more
                      // will be written.
    void set_error(); // Signal that the stream suffered an error.

    bool is_closed() const;              // Has the stream been closed?
    uint64_t available_capacity() const; // How many bytes can be pushed to the stream right now?
    uint64_t bytes_pushed() const;       // Total number of bytes cumulatively pushed to the stream
};

class Reader : public ByteStream
{
  public:
    std::string_view peek() const; // Peek at the next bytes in the buffer
    void pop(uint64_t len);        // Remove `len` bytes from the buffer

    bool is_finished() const; // Is the stream finished (closed and fully popped)?
    bool has_error() const;   // Has the stream had an error?

    uint64_t bytes_buffered() const; // Number of bytes currently buffered (pushed and not popped)
    uint64_t bytes_popped() const;   // Total number of bytes cumulatively popped from stream
};
```

### 部分实现代码

```c++
void Writer::push(const std::string &data)
{
    uint64_t remaining_capacity = capacity_ - buffer.size();
    if (data.length() <= remaining_capacity) {
        for (char c : data) {
            buffer.push_back(c);
        }
        pushed_len_ += data.length();
    } else {
        for (uint64_t i = 0; i < remaining_capacity; ++i) {
            buffer.push_back(data[i]);
        }
        pushed_len_ += remaining_capacity;
    }
}

void Writer::close() { is_closed_ = true; }

void Writer::set_error() { is_error_ = true; }

bool Writer::is_closed() const { return is_closed_; }

uint64_t Writer::available_capacity() const { return capacity_ - buffer.size(); }
```

