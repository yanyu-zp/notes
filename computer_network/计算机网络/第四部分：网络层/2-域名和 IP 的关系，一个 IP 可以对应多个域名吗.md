整个互联网是一个单一的、抽象的网络。互联网上每一台主机或者路由器在连入互联网时需要被分配一个唯一的标识符，即 IP 地址，在 IPv4 协议下，这个标识符是一个 32 位无符号二进制数。互联网上和一个设备通信时，需要知道其 IP 地址。互联网最常见的应用之一是向某个特定主机请求一些服务，例如请求某个网页的信息，请求某个邮件服务器的服务。然而，数字 IP 地址不方便人们记忆和描述。为此，域名系统（Domain Name System，DNS）被开发和应用，使得人们可以方便地将一串便于记忆和描述的字符串，称为域名，利用 DNS 自动转换为 IP 地址。互联网上分布运行着许多域名服务器，其他互联网上的设备可以向他们询问域名对应的 IP 地址是什么，此即域名解析。网站的运营者可以在域名注册商处注册域名，使其指向自己的网站服务器的 IP 地址，使得这一域名被其他互联网上的用户使用。

在最简单的情形下，一个域名只对应一个 IP 地址，一个 IP 地址只对应一个域名。

实际中，根据需求，多个域名可以被解析为同一个 IP 地址。例如具有一个 IP 地址的服务器上的运营的网站，为了推广自己给自己注册了许多朗朗上口的域名；或者提前注册多个类似域名，防止被竞争者不当利用；或者一个网站历史上使用了另一个域名，现在多个域名同时可用，并被解析到同一个 IP 地址。
此外，相对少见地，一个域名可能会被解析出多个 IP 地址。例如，这个网站为了平衡负载，在多个服务器上提供了同一类服务。这可能表现为：不同时间，同一个域名被解析为不同的 IP；一个域名解析的结果可能有若干备选 IP；一个域名不同的网络服务提供商的网络中会被解析出不同的 IP。

示例：如图，在实现了 Posix 的计算机系统上，nslookup 命令可以用于解析域名。运行 nslookup www.baidu.com 可能会返回多个 IP 地址，而查询 www.baidu.com 和 www.a.shifen.com 的 IP 地址包含了重复的 IP 地址条目。

![nslookup](assets/nslookup.png)