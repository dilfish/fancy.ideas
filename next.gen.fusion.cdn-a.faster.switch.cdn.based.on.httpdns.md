# 下一代融合 CDN -- 基于 HTTPDNS 的快速切换网络

## 不使用 CDN
例如，www.example.com 网站，其静态内容配置3台服务器。则 DNS 配置为，www.example.com 有 3条 A 记录，分别指向3台服务器的 IP 地址。当业务伸缩时，增减服务器需要同步内容，修改 DNS 配置等，而且不容易做到服务器容量合适。

## CDN 和融合 CDN
使用 CDN 之后，www.example.com 配置 CNAME 记录，指向 www.example.com.cdn.com，后者指向 CDN 服务商的服务器池。由 CDN 服务商的服务器池作为缓存，而服务器池从原始的网站（源站）获取数据，称为回源。例如，www.example.com.cdn.com 指向的服务器将从 src.example.com 获取原始数据。

如此，CDN 服务器池实现了查询文件的动态可扩展。为了及时响应源站的变化，例如文件删除，文件修改，文件添加，CDN 服务商提供刷新和预取接口。

- 刷新接口提供删除文件缓存的功能，源站删除文件后，将文件及时从 CDN 服务器池中删除。
- 预取接口提供文件获取功能，源站增加新文件后，CDN 服务器池将文件从源站同步到缓存。
- 如果修改文件，则需要先调用刷新接口，删除老的文件之后，再调用预取接口，替换成为新的接口。

融合 CDN 没有改变 CDN 的主要功能，而是在成本和性能之间取得更好的平衡。此时，www.example.com 指向 www.example.com.fusion.com， www.example.fusion.com 不指向具体的服务器池，而是指向 CDN 服务商池。例如指向 www.example.fusion.com.cdn1.com 和 www.example.fusion.com.cdn2.com。这样在 DNS 解析流程中增加了一层 CNAME 跳转，而融合 CDN 的 CNAME 可以选择合适的时间和区域指向不同的 CDN 服务商。达到成本/性能的综合提升。

## HTTPDNS
上述融合 CDN 在切换时，依赖于 DNS 生效的时间，一般全国生效时间为**30分钟**，为了确保效果，承诺的生效时间可达**1小时以上**。

对于使用 HTTPDNS 接口的 app 来说，可以将融合 CDN 调节时间和区域的能力发挥到极致。

- 目前 HTTPDNS 区域划分使用城市级别，普通 public dns 也可以支持 edns subnet 达到类似效果。随着 IPv6 的普及，区域划分可能会进入到更细粒度。
- 目前 HTTPDNS **不提供**刷新预取功能。普通 public dns 如 google dns 和 opendns 有提供。
- 国内三大运营商已经提供 IPv6 支持，变化频率大约为1小时。

综上，结合了 CDN 刷新预取功能**和 DNS 的刷新预取功能**的融合 CDN，将可以使线路调度在区域中达到个人粒度，同时，切换时间达到**10秒级别**。从而使得成本和性能的提升达到一个新的阶段。
