<article class="Post-Main Post-NormalMain" tabindex="-1">
    <header class="Post-Header">
        <h1 class="Post-Title">IPv6原理、应用与实践</h1>
        <div class="Post-Author">
            <div>
                </span>
            </p>
            </blockquote>
            <p>2017年11月26日，中共中央办公厅和国务院办公厅印发了《<a href="https://link.zhihu.com/?target=http%3A//www.xinhuanet.com/politics/2017-11/26/c_1122012631.htm" class=" wrap external" target="_blank" rel="nofollow noreferrer" data-za-detail-view-id="1043">推荐互联网协议第六版（IPv6）规模部署行动计划</a>》，并发出通知，要求各地区各部门结合实际认真贯彻落实。这条新闻传达了一个很重要的信息：这个是推进中国IPv6发展的战略总动员令。</p>
            <p><b>本文将会从以下几个方面进一步介绍IPv6，包括有：</b>
            </p>
            <ol>
                <li><b>IPv6的基本概念</b>
                </li>
                <li><b>IPv6在Linux操作系统下的实现</b>
                </li>
                <li><b>IPv6的实验</b>
                </li>
                <li><b>IPv6的过渡技术介绍</b>
                </li>
                <li><b>IPv6在Linux平台下socket编程应该注意的问题</b>
                </li>
                <li><b>实现简易版TGW支持IPv6雏形demo</b>
                </li>
            </ol>
            <p>值得说的是，目前我们接触得比较多的主流操作系统内核，已经很好地支持IPv6协议栈，例如：</p>
            <ul>
                <li>Windows： windows 7、windows 8.x、windows 10，默认开启IPv6</li>
                <li>Linux： 内核2.6.x、内核3.x、内核4.x 已经支持IPv6（需要手动开启）</li>
            </ul>
            <p>IOS：IOS9开始已经支持IPv6 Only，2016年苹果已经强制要求app必须支持IPv6</p>
            <p>本文提到的IPv6节点，没有特殊说明，一般指的是纯IPv6节点（IPv6 Only），也就是只支持IPv6协议栈；IPv4节点，是指纯IPv4的节点，也就是只支持IPv4协议栈；如果节点支持IPv6和IPv4双栈，会指明是双栈节点。</p>
            <h2><b>IPv6的基本概念</b></h2>
            <p>众所周知，32位的IPv4地址已经耗竭，IPv6采用128位的地址长度拥有更大的地址空间。首先我们先来认识一下IPv6到底长成什么样子。</p>
            <h2><b>初识IPv6</b></h2>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic4.zhimg.com/v2-7b95a85adb8c0fe8b88c7ab46174f3eb_b.jpg" data-caption="" data-size="normal" data-rawwidth="756" data-rawheight="510" class="origin_image zh-lightbox-thumb" width="756" data-original="https://pic4.zhimg.com/v2-7b95a85adb8c0fe8b88c7ab46174f3eb_r.jpg"
                    />
                </noscript>
                <img src="https://pic4.zhimg.com/80/v2-7b95a85adb8c0fe8b88c7ab46174f3eb_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="756" data-rawheight="510" class="origin_image zh-lightbox-thumb lazy" width="756" data-original="https://pic4.zhimg.com/v2-7b95a85adb8c0fe8b88c7ab46174f3eb_r.jpg"
                data-actualsrc="https://pic4.zhimg.com/v2-7b95a85adb8c0fe8b88c7ab46174f3eb_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图1 IPv6数据报文</p>
            <p>上图是我们最熟悉的ping的IPv6版本ICMPv6。可以看到，IPv6数据报文和IPv4有很大的差别：</p>
            <ul>
                <li>数据链路层（L2）的type字段标识为 0x86dd，表示承载的上层协议是IPv6</li>
            </ul>
            <p>IPv4对比：type字段为0x0800</p>
            <ul>
                <li>IPv6的头部字段，和IPv4差别巨大<b>（可以猜测到，IPv6和IPv4无法兼容）</b>
                </li>
            </ul>
            <p>IPv6的报文头部格式如下：</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic3.zhimg.com/v2-75cfd2989916c954a05eb12d0d18d9be_b.jpg" data-caption="" data-size="normal" data-rawwidth="506" data-rawheight="276" class="origin_image zh-lightbox-thumb" width="506" data-original="https://pic3.zhimg.com/v2-75cfd2989916c954a05eb12d0d18d9be_r.jpg"
                    />
                </noscript>
                <img src="https://pic3.zhimg.com/80/v2-75cfd2989916c954a05eb12d0d18d9be_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="506" data-rawheight="276" class="origin_image zh-lightbox-thumb lazy" width="506" data-original="https://pic3.zhimg.com/v2-75cfd2989916c954a05eb12d0d18d9be_r.jpg"
                data-actualsrc="https://pic3.zhimg.com/v2-75cfd2989916c954a05eb12d0d18d9be_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图2 IPv6报文头部（该图片来自互联网）</p>
            <p>IPv6报文头部更精简了，字段更少了，对比起IPv4，有以下几个地方值得注意：</p>
            <ul>
                <li>IPv6报文头部是定长（固定为40字节），IPv4报文头部是变长的。这个意味着，写代码处理IPv6数据报文的效率会提高很多：）</li>
                <li>IPv6中Hop Limit字段含义类似IPv4的TTL。</li>
                <li>IPv6中的Traffic Class字段含义类似IPv4中的TOS（Type Of Service）。</li>
                <li>IPv6的报文头部取消了校验和字段。取消这个字段也是对IPv4协议的一个改进。当IPv4报文在网路间传输，每经过一个路由器转发就是修改TTL字段，就需要重新计算校验和，而由于数据链路层L2和传输层L4的校验已经足够强壮，因此IPv6取消这个字段会提高路由器的转发效率。值得一提的是，在IPv6协议下，传输层L4协议UDP、TCP是强制需要进行校验和的（IPv4是可选的）。</li>
                <li>IPv6报文头部中的Next Header字段表示“承载上一层的协议类型”或者“扩展头部类型”。<b>这里的含义与IPv4有很大的差别，需要加以解释</b>：</li>
            </ul>
            <p>当IPv6数据报文承载的是上层协议ICMPv6、TCP、UDP等的时候，Next Header的值分别为58、6、17，这个时候和IPv4报文头部中的Protocol字段很类似。</p>
            <p>当不是以上3种协议类型的时候，IPv6报文头部紧接的是扩展头部。扩展头部是IPv6引入的一个新的概念，每个IPv6的数据报文可以承载0个或多个扩展头部，扩展头部通过链表的形式组织起来。当IPv6数据报文承载着扩展头部的时候，Next Header的数值为扩展头部的类型值。</p>
            <p>为什么要引入扩展头部这个概念，这里也是IPv6对IPv4改进的一个方面，用扩展头部取代了IPv4的可选项信息，精简了IPv6的头部，增强了IPv6的扩展性。有同学会不会有疑问，IPv6的分片数据报文怎么处理？其实就是使用了IPv6扩展头部。我们来抓一个UDP分片报文来看看。</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic3.zhimg.com/v2-273be9b69213532ea6fa7376b06b1b5e_b.jpg" data-caption="" data-size="normal" data-rawwidth="826" data-rawheight="564" class="origin_image zh-lightbox-thumb" width="826" data-original="https://pic3.zhimg.com/v2-273be9b69213532ea6fa7376b06b1b5e_r.jpg"
                    />
                </noscript>
                <img src="https://pic3.zhimg.com/80/v2-273be9b69213532ea6fa7376b06b1b5e_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="826" data-rawheight="564" class="origin_image zh-lightbox-thumb lazy" width="826" data-original="https://pic3.zhimg.com/v2-273be9b69213532ea6fa7376b06b1b5e_r.jpg"
                data-actualsrc="https://pic3.zhimg.com/v2-273be9b69213532ea6fa7376b06b1b5e_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图3 IPv6分片报文</p>
            <p>当发送一个分片IPv6数据报文的时候，IPv6使用的是扩展头部的形式组织各个分片的信息，如图IPv6报文头部Next Header字段值为44表示存在扩展头部，扩展头部是IPv6分片数据信息。</p>
            <p>对比IPv4，分片信息是记录在IPv4报文头部的分片字段中。</p>
            <p>IPv6的扩展头部类型有很多种，除了上述的分片头部，还有路由头部、逐跳可选头部等，具体的可以参考RFC2460。</p>
            <p>本章主要介绍了IPv6的一些很直观的认识，下面逐渐介绍IPv6上的基本知识和概念。</p>
            <h2><b>IPv6的地址语法</b></h2>
            <p>一个IPv6的地址使用冒号十六进制表示方法：128位的地址每16位分成一段，每个16位的段用十六进制表示并用冒号分隔开，例如：</p>
            <p>一个普通公网IPv6地址：2001:0D12:0000:0000:02AA:0987:FE29:9871</p>
            <p>IPv6地址支持压缩前导零的表示方法，例如上面的地址可以压缩表示为：</p>
            <p>2001:D12:0:0:2AA:987:FE29:9871</p>
            <p>为了进一步精简IPv6地址，当冒号十六进制格式中出现连续几段数值0的位段时，这些段可以压缩为双冒号的表示，例如上面的地址还可以进一步精简表示为：</p>
            <p>2001:D12::2AA:987:FE29:9871</p>
            <p>又例如IPv6的地址FF80:0:0:0:FF:3BA:891:67C2可以进一步精简表示为：</p>
            <p>FE80::FF:3BA:891:67C2</p>
            <p><b>这里值得注意的是，双冒号只能出现一次。</b>
            </p>
            <h2><b>IPv6地址的号段划分和前缀表示法</b></h2>
            <p>IPv6拥有128位巨大的地址空间，对于那么大的空间，也不是随意的划分，而是使用按照bit位进行号段划分（与鹅厂内部一些的64位uin改造放号的zone划分算法）。</p>
            <p>IPv6的地址结构如下图：</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic4.zhimg.com/v2-adb6ba83adfe2ab04832258900819047_b.jpg" data-caption="" data-size="normal" data-rawwidth="1231" data-rawheight="170" class="origin_image zh-lightbox-thumb" width="1231" data-original="https://pic4.zhimg.com/v2-adb6ba83adfe2ab04832258900819047_r.jpg"
                    />
                </noscript>
                <img src="https://pic4.zhimg.com/80/v2-adb6ba83adfe2ab04832258900819047_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="1231" data-rawheight="170" class="origin_image zh-lightbox-thumb lazy" width="1231" data-original="https://pic4.zhimg.com/v2-adb6ba83adfe2ab04832258900819047_r.jpg"
                data-actualsrc="https://pic4.zhimg.com/v2-adb6ba83adfe2ab04832258900819047_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图4 IPv6地址结构</p>
            <p>例如RFC4291中定义了n=48, m=16，也就是子网和接口ID与各占64位</p>
            <p>IPv6支持子网前缀标识方法，类似于IPv4的无分类域间路由CIDR机制（注意：IPv6没有子网掩码mask的概念）。使用“IPv6地址/前缀长度”表示方法，例如：</p>
            <p>2001:C3:0:2C6A::/64表示一个子网</p>
            <p>而2001:C3:0:2C6A:C9B4:FF12:48BC:1A22/64表示该子网下的一个节点地址。</p>
            <p>可以看到，一个IPv6的地址有子网前缀+接口ID构成，子网前缀由地址分配和管理机构定义和分配，而接口ID可以由各操作系统实现生成，生成算法后面的章节会介绍。</p>
            <h2><b>IPv6的地址类型</b></h2>
            <p>IPv6地址分三种类型</p>
            <p>1、单播，对应于IPv4的普通公网和私网地址</p>
            <p>2、组播，对应于IPv4的组播（多播）地址</p>
            <p>3、任播，IPv6新增的地址概念类型</p>
            <p>IPv6没有广播地址，用组播地址实现广播的功能。实际上我们工作和生活最可能最多接触的就是单播地址，接下来本文重点会讲解单播地址的种类。组播和任播地址有兴趣的同学自行查阅相关RFC和文献。</p>
            <h2><b>IPv6单播地址</b></h2>
            <p>注意，大家如果在网上搜索IPv6的地址，可能都是千篇一律的把所有“出现过”的单播地址介绍出来，其实有一些单播地址类型已经在相关的RFC中被废除或者不建议使用，而本节会指出这类地址。同时，在介绍单播地址的时候，尽量与IPv4中对应的或者相类似的概念做对比，加深理解。</p>
            <p>IPv6单播地址有以下几种：</p>
            <p><b>1、全球单播地址</b>
            </p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic4.zhimg.com/v2-065306e7bb75cb7874de11bda77fa21b_b.jpg" data-caption="" data-size="normal" data-rawwidth="1174" data-rawheight="143" class="origin_image zh-lightbox-thumb" width="1174" data-original="https://pic4.zhimg.com/v2-065306e7bb75cb7874de11bda77fa21b_r.jpg"
                    />
                </noscript>
                <img src="https://pic4.zhimg.com/80/v2-065306e7bb75cb7874de11bda77fa21b_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="1174" data-rawheight="143" class="origin_image zh-lightbox-thumb lazy" width="1174" data-original="https://pic4.zhimg.com/v2-065306e7bb75cb7874de11bda77fa21b_r.jpg"
                data-actualsrc="https://pic4.zhimg.com/v2-065306e7bb75cb7874de11bda77fa21b_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图5 IPv6全球单播地址结构</p>
            <p>前缀2000::/3，相当于IPv4的公网地址（IPv6的诞生根本上就是为了解决IPv4公网地址耗尽的问题）。这种地址在全球的路由器间可以路由。</p>
            <p><b>2、链路本地地址</b>
            </p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic1.zhimg.com/v2-f5dbbd7e3c147693fb25f21beff45258_b.jpg" data-caption="" data-size="normal" data-rawwidth="1224" data-rawheight="124" class="origin_image zh-lightbox-thumb" width="1224" data-original="https://pic1.zhimg.com/v2-f5dbbd7e3c147693fb25f21beff45258_r.jpg"
                    />
                </noscript>
                <img src="https://pic1.zhimg.com/80/v2-f5dbbd7e3c147693fb25f21beff45258_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="1224" data-rawheight="124" class="origin_image zh-lightbox-thumb lazy" width="1224" data-original="https://pic1.zhimg.com/v2-f5dbbd7e3c147693fb25f21beff45258_r.jpg"
                data-actualsrc="https://pic1.zhimg.com/v2-f5dbbd7e3c147693fb25f21beff45258_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图6 链路本地地址结构</p>
            <p>前缀FE80::/10，顾名思义，此类地址用于同一链路上的节点间的通信，主要用于自动配置地址和邻居节点发现过程。Windows和Linux支持或开启IPv6后，默认会给网卡接口自动配置一个链路本地地址。也就是说，一个接口一定有一个链路本地地址。如下图：</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic3.zhimg.com/v2-3dc1209889c4975f325f203c3d7c7e92_b.jpg" data-caption="" data-size="normal" data-rawwidth="745" data-rawheight="238" class="origin_image zh-lightbox-thumb" width="745" data-original="https://pic3.zhimg.com/v2-3dc1209889c4975f325f203c3d7c7e92_r.jpg"
                    />
                </noscript>
                <img src="https://pic3.zhimg.com/80/v2-3dc1209889c4975f325f203c3d7c7e92_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="745" data-rawheight="238" class="origin_image zh-lightbox-thumb lazy" width="745" data-original="https://pic3.zhimg.com/v2-3dc1209889c4975f325f203c3d7c7e92_r.jpg"
                data-actualsrc="https://pic3.zhimg.com/v2-3dc1209889c4975f325f203c3d7c7e92_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图7 Linux下查看链路本地地址</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic2.zhimg.com/v2-0fb5df1eab4895960ab4fb7405c4de95_b.jpg" data-caption="" data-size="normal" data-rawwidth="352" data-rawheight="346" class="content_image" width="352" />
                </noscript>
                <img src="https://pic2.zhimg.com/80/v2-0fb5df1eab4895960ab4fb7405c4de95_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="352" data-rawheight="346" class="content_image lazy" width="352" data-actualsrc="https://pic2.zhimg.com/v2-0fb5df1eab4895960ab4fb7405c4de95_b.jpg"
                data-lazy-status="ok">
            </figure>
            <p>图8 Windows下查看链路本地地址</p>
            <p>值得说的是，每个接口必须至少有一个链路本地地址；每个接口可以配置1个以上的单播地址，例如一个接口可以配置一个链路本地地址，同时也可以配置一个全球单播地址。</p>
            <p>注意，很容易会把链路本地地址和IPv4的私网/内网地址对应起来，其实链路本地地址对应于IPv4的APIPA地址，也就是169.254开头的地址（典型场景就是windows开启自动获取地址而获取失败后自动分配一个169.254的地址）。而IPv4私网对应于IPv6的什么地址，后面会介绍。</p>
            <p>特别地，在IPv6 socket编程中，可以使用链路本地地址编程通信，但是需要增加一些额外的参数（这是一个小坑），在后面介绍编程的章节会介绍。</p>
            <p><b>3、唯一本地地址</b>
            </p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic2.zhimg.com/v2-11f495571c09c9b7f1c320e352d9f711_b.jpg" data-caption="" data-size="normal" data-rawwidth="1382" data-rawheight="126" class="origin_image zh-lightbox-thumb" width="1382" data-original="https://pic2.zhimg.com/v2-11f495571c09c9b7f1c320e352d9f711_r.jpg"
                    />
                </noscript>
                <img src="https://pic2.zhimg.com/80/v2-11f495571c09c9b7f1c320e352d9f711_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="1382" data-rawheight="126" class="origin_image zh-lightbox-thumb lazy" width="1382" data-original="https://pic2.zhimg.com/v2-11f495571c09c9b7f1c320e352d9f711_r.jpg"
                data-actualsrc="https://pic2.zhimg.com/v2-11f495571c09c9b7f1c320e352d9f711_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图9 唯一本地地址结构</p>
            <p>前缀FC00::/7，相当于IPv4的私网地址（10.0.0.0、172.16.0.0、192.168.0.0），在RFC4193中新定义的一种解决私网需求的单播地址类型，用来代替废弃使用的站点本地地址。</p>
            <p>可能看到这里，有同学会跳出来说：IPv6不是为了解决IPv4地址耗尽的问题吗，既然IPv6的地址空间那么大，可以为每一个网络节点分配公网IPv6的节点，那为什么IPv6还需要支持私网？这里需要谈谈对IPv6下私网支持的认识。</p>
            <p>在IPv4中，利用NAT技术私网内的网络节点可以使用统一的公网出口访问互联网资源，大大节省了IPv4公网地址的消耗（IPv6推进缓慢的原因之一）。另一方面，由于默认情况下私网内节点与外界通信的发起是单向的，网络访问仅仅能从私网内发起，外部发起的请求会被统一网关或者防火墙阻隔掉，这样的网络架构很好的保护了私网内的节点安全性和私密性。可以设想一下，如果鹅厂内部每台办公电脑都配置了IPv6的公网地址上网，是多么可怕的事情，每台办公电脑都会面临被黑客入侵的威胁（肉鸡真多）。</p>
            <p>因此，在安全性和私密性要求下，IPv6中同样需要支持私网，并且也需要支持NAT。在Linux内核3.7版本开始加入对IPv6 NAT的支持，实现的方式和IPv4下的差别不大（Linux内核代码中变量和函数的命名几乎就是ctrl+c和ctrl+v过来的-_-||）。</p>
            <p><b>4、站点本地地址</b>
            </p>
            <p>前缀FEC9::/48，以前是用来部署私网的，<b>但RFC3879中已经不建议使用这类地址</b>，建议使用唯一本地地址。大家知道有这么一回事就可以了。网上还有很多文章还提到这种地址，但是没有说明这种地址已经不再使用。</p>
            <p><b>5、特殊地址：回环地址</b>
            </p>
            <p>0:0:0:0:0:0:0:1或::1，等同于IPv4的127.0.0.1</p>
            <p><b>6、过渡地址：内嵌IPv4地址的IPv6地址</b>
            </p>
            <p>就是在IPv6的某一些十六进制段内嵌这IPv4的地址，例如IPv6地址中64:ff9b::10.10.10.10，此IPv6地址最后4个字节内嵌一个IPv4的地址，这类地址主要用于IPv6/IPv4的过渡技术中。</p>
            <p><b>一、IPv4兼容地址</b>
            </p>
            <p>0:0:0:0:0:0:w.x.y.z或::w.x.y.z（其中w.x.y.z是点分十进制的IPv4地址）。<b>但在RFC4291中已经不推荐使用这类地址</b>，大家知道有这么一回事就可以了。</p>
            <p><b>二、过渡地址：IPv4映射地址</b>
            </p>
            <p>0:0:0:0:0:FFFF:w.x.y.z或::FFFF:w.x.y.z（其中w.x.y.z是点分十进制的IPv4地址），用于IPv6地址表示IPv4地址。主要用于某些场景下IPv6节点与IPv4节点通信，Linux内核对这类地址很好地支持，在后面编程和内核分析的章节会分析使用过程。</p>
            <p><b>三、过渡地址：特定过渡技术地址</b>
            </p>
            <p>6to4地址、ISATAP地址、Teredo地址主要用于对应的过渡技术的地址，在后面介绍过渡技术的时候会介绍。</p>
            <h2><b>IPv6接口ID生成算法</b></h2>
            <p>从前面的介绍中可以看出，IPv6单播地址是由前缀（64位）+接口ID（64位）组成。接口ID的生成算法主要有以下几种：</p>
            <p>1、根据RFC4291定义，接口ID可以从EUI-64地址生成。</p>
            <p>详细算法可以查看regli同学的PPT第14页。</p>
            <p>2、为了可以具备某种程度的匿名信，接口ID可以使用一个随机分配的，windows操作系统默认就是使用这种生成算法，Linux下也是默认开启这个算法。</p>
            <p>3、使用状态化的自动配置技术分配，例如DHCPv6分配。</p>
            <p>4、手工配置。</p>
            <h2><b>IPv6地址配置</b></h2>
            <p>前面对IPv6的地址、前缀、接口等等做了介绍，接下来就是要介绍一个接口如何配置IPv6地址。IPv6一个比IPv4更厉害的方面，就是可以自动配置地址，甚至这个配置过程不需要DHCPv6（在IPv4中是DHCPv4）这样的地址配置协议。最典型的例子就是，只要开启了IPv6协议栈的操作系统，每个接口就能自动配置了链路本地地址，这个是和IPv4最重要的区别之一。</p>
            <p>IPv6的地址配置有以下几种：</p>
            <p>1、只要开启了IPv6协议栈，接口自动分配链路本地地址。</p>
            <p>2、无状态自动配置地址（RFC2462），后面会有实验演示。</p>
            <p>3、有状态自动配置地址，例如DHCPv6。</p>
            <p>4、手动配置。</p>
            <h2><b>IPv6的域名解析</b></h2>
            <p>由于IPv6的地址扩展为128位，比IPv4的更难书写和记忆，因此IPv6下的DNS变得尤为重要。IPv6的的DNS资源记录类型为AAAA（又称作4A），用于解析指向IPv6地址的完全有效域名。下面是一个示例：</p>
            <p><a href="https://link.zhihu.com/?target=http%3A//Hostipv6.example.wechat.com" class=" external" target="_blank" rel="nofollow noreferrer" data-za-detail-view-id="1043"><span class="invisible">http://</span><span class="visible">Hostipv6.example.wechat.com</span><span class="invisible"></span></a> IN
                AAAA 2001:db8:1::1</p>
            <p>IPv6下的域名解析可以认为是IPv4的扩展，详细可以查看RFC3596.</p>
            <h2><b>Linux内核IPv6架构简析</b></h2>
            <p>本文后面主要的分析都是基于Linux，会有涉及关于Linux内核对IPv6的实现。主要是因为，现在IPv6的参考资料不多，除了与IPv6相关的RFC之外，还有少数可以参阅的IPv6国外文献，而Linux内核一直都与跟随着IPv6的协议更新和变化，Linux内核IPv6的实现是十分重要的参考材料之一。而且从事后台开发工作主要也是在Linux平台下，熟悉Linux下IPv6的实现也是为以后的工作做知识储备。</p>
            <p>PS：客户端开发的同学可以参考各自平台的文档.............</p>
            <p>Linux在很早之前就已经开始支持IPv6，目前我们接触最多的Linux内核版本都很好地支持IPv6，同时也是支持IPv4/IPv6双栈体系。在Linux操作系统中，IPv4是默认必须开启，IPv6是可选编译和配置开启。</p>
            <p>例如在编译内核的时候，需要选择IPv6编译选项才支持IPv6</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic4.zhimg.com/v2-19dc384faf865a27430446334030ea3f_b.jpg" data-caption="" data-size="normal" data-rawwidth="535" data-rawheight="212" class="origin_image zh-lightbox-thumb" width="535" data-original="https://pic4.zhimg.com/v2-19dc384faf865a27430446334030ea3f_r.jpg"
                    />
                </noscript>
                <img src="https://pic4.zhimg.com/80/v2-19dc384faf865a27430446334030ea3f_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="535" data-rawheight="212" class="origin_image zh-lightbox-thumb lazy" width="535" data-original="https://pic4.zhimg.com/v2-19dc384faf865a27430446334030ea3f_r.jpg"
                data-actualsrc="https://pic4.zhimg.com/v2-19dc384faf865a27430446334030ea3f_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图10 Linux内核编译支持IPv6</p>
            <p>当开启支持IPv6的Linux的内核网络双栈的结构，如下图：</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic2.zhimg.com/v2-d36f0d92e83f323eaf7e8f543eb0facd_b.jpg" data-caption="" data-size="normal" data-rawwidth="1216" data-rawheight="422" class="origin_image zh-lightbox-thumb" width="1216" data-original="https://pic2.zhimg.com/v2-d36f0d92e83f323eaf7e8f543eb0facd_r.jpg"
                    />
                </noscript>
                <img src="https://pic2.zhimg.com/80/v2-d36f0d92e83f323eaf7e8f543eb0facd_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="1216" data-rawheight="422" class="origin_image zh-lightbox-thumb lazy" width="1216" data-original="https://pic2.zhimg.com/v2-d36f0d92e83f323eaf7e8f543eb0facd_r.jpg"
                data-actualsrc="https://pic2.zhimg.com/v2-d36f0d92e83f323eaf7e8f543eb0facd_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图11 Linux内核双栈架构</p>
            <p>Linux内核中，IPv6协议栈与IPv4协议栈并行关系。IPv6和IPv4完全是两套不一样的代码实现。IPv6完整的协议栈逻辑模块包括：</p>
            <p>1、网络层IPv6，核心逻辑：IPv6路由子系统</p>
            <p>2、传输层TCP/UDP实现：TCPv6、UDPv6</p>
            <p>3、控制报文协议ICMPv6，这里值得一提的是ICMPv6在IPv6协议中的地位十分重要。</p>
            <p>ICMPv6不仅提供了与ICMPv4相同的服务诊断功能，例如报告数据包的错误和提供简单的echo服务，ICMPv6是IPv6中邻居发现协议的重要组成部分，用于管理链路上的点到点的通信。</p>
            <p>4、邻居子系统的实现：邻居发现协议NDP（对应于IPv4里面的ARP协议）</p>
            <p>5、其他高级实现（IPv6 NAT、IPv6隧道、iPv6 IPSec等）</p>
            <p>由于我们平时的开发工作在应用层，以上1-4是将会接触得最多。</p>
            <h2><b>IPv6实验</b></h2>
            <p>本章我们通过实验，加深对IPv6的认识。这里的实验没有使用真实现网的IPv6接入点（目前国内绝大部分接入点都是教育网），而实验的目的主要是观察IPv6的数据包结构、IPv6的路由配置等，所以决定自己通过搭建中间路由器、应用服务器的方式做实验，便于抓包和代码分析。</p>
            <p>客户端：windows 7</p>
            <p>路由器：中间路由器使用自己编译和搭建的Linux系统（内核2.6.32.27）</p>
            <p>应用服务器：Ubuntu16.04LTS版本。</p>
            <p>为什么要使用自己编译的Linux作为路由器？因为IPv6的实践类能参考的文献比较少，而Linux内核的IPv6模块是最重要的参考资源之一，在实践中遇到问题可以使用打LOG和分析代码的方法解决。</p>
            <p><b>1、无状态自动配置地址实验</b>
            </p>
            <p>IPv6地址的获取是最重要的环节之一。本实验使用开源的无状态自动配置服务radvd进行实验。</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic4.zhimg.com/v2-0ea6f5b86a5f9b59b5c61b9448319147_b.jpg" data-caption="" data-size="normal" data-rawwidth="1156" data-rawheight="434" class="origin_image zh-lightbox-thumb" width="1156" data-original="https://pic4.zhimg.com/v2-0ea6f5b86a5f9b59b5c61b9448319147_r.jpg"
                    />
                </noscript>
                <img src="https://pic4.zhimg.com/80/v2-0ea6f5b86a5f9b59b5c61b9448319147_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="1156" data-rawheight="434" class="origin_image zh-lightbox-thumb lazy" width="1156" data-original="https://pic4.zhimg.com/v2-0ea6f5b86a5f9b59b5c61b9448319147_r.jpg"
                data-actualsrc="https://pic4.zhimg.com/v2-0ea6f5b86a5f9b59b5c61b9448319147_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图12 IPv6无状态自动配置</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic2.zhimg.com/v2-b2fb0afbf052042e40165a0f906dd47d_b.jpg" data-caption="" data-size="normal" data-rawwidth="1517" data-rawheight="564" class="origin_image zh-lightbox-thumb" width="1517" data-original="https://pic2.zhimg.com/v2-b2fb0afbf052042e40165a0f906dd47d_r.jpg"
                    />
                </noscript>
                <img src="https://pic2.zhimg.com/80/v2-b2fb0afbf052042e40165a0f906dd47d_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="1517" data-rawheight="564" class="origin_image zh-lightbox-thumb lazy" width="1517" data-original="https://pic2.zhimg.com/v2-b2fb0afbf052042e40165a0f906dd47d_r.jpg"
                data-actualsrc="https://pic2.zhimg.com/v2-b2fb0afbf052042e40165a0f906dd47d_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图13 IPv6无状态自动配置报文分析</p>
            <p>无状态自动配置过程：</p>
            <p>1、由链路上的主机向链路发起“路由请求”报文，这个报文是以组播协议发送，寻找链路上最合适的路由器。</p>
            <p>2、路由器收到请求会返回“路由通告”报文，报文里面带着本链路的地址前缀信息主机将接收到的前缀和自身的接口ID，组成完整的新地址。</p>
            <p>3、主机尝试使用新地址发起地址重复检测，检测链路上是否有其他主机也是这个地址，如果有，就停止使用该地址；如果没有，就启用这个新地址。</p>
            <p>可以看到无状态自动配置过程十分简易（对比DHCPv4和DHCPv6来说），实际上，无状态自动配置可以单独组网使用，也可以配合有状态自动配置一般会配合使用，加强网络节点管理。涉及自动配置和地址检测等更多细节，可以查阅RFC1971、RFC4861。</p>
            <p><b>2、IPv6静态路由配置实验</b>
            </p>
            <p>本次实验主要是了解windows和linux的静态路由配置。</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic4.zhimg.com/v2-a75f79da36c40fc6f0f044190f46c8c7_b.jpg" data-caption="" data-size="normal" data-rawwidth="1319" data-rawheight="532" class="origin_image zh-lightbox-thumb" width="1319" data-original="https://pic4.zhimg.com/v2-a75f79da36c40fc6f0f044190f46c8c7_r.jpg"
                    />
                </noscript>
                <img src="https://pic4.zhimg.com/80/v2-a75f79da36c40fc6f0f044190f46c8c7_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="1319" data-rawheight="532" class="origin_image zh-lightbox-thumb lazy" width="1319" data-original="https://pic4.zhimg.com/v2-a75f79da36c40fc6f0f044190f46c8c7_r.jpg"
                data-actualsrc="https://pic4.zhimg.com/v2-a75f79da36c40fc6f0f044190f46c8c7_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图14 IPv6典型的网络拓扑</p>
            <p>由于各自的网络前缀（网段）不一致，在不使用默认路由的情况下，我们尝试配置路由让客户端可以访问到服务器。</p>
            <p>一、Windows 7配置静态路由：</p>
            <p>去往服务器的2001:db8:5::/64网段的路由</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic4.zhimg.com/v2-865dfd74079ef3cef100ff63fdc45d1f_b.jpg" data-caption="" data-size="normal" data-rawwidth="587" data-rawheight="499" class="origin_image zh-lightbox-thumb" width="587" data-original="https://pic4.zhimg.com/v2-865dfd74079ef3cef100ff63fdc45d1f_r.jpg"
                    />
                </noscript>
                <img src="https://pic4.zhimg.com/80/v2-865dfd74079ef3cef100ff63fdc45d1f_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="587" data-rawheight="499" class="origin_image zh-lightbox-thumb lazy" width="587" data-original="https://pic4.zhimg.com/v2-865dfd74079ef3cef100ff63fdc45d1f_r.jpg"
                data-actualsrc="https://pic4.zhimg.com/v2-865dfd74079ef3cef100ff63fdc45d1f_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图15 Windows配置IPv6路由</p>
            <p>二、路由器1配置</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic3.zhimg.com/v2-41196349cc0ddcd8eb63ee45d2d33786_b.jpg" data-caption="" data-size="normal" data-rawwidth="819" data-rawheight="451" class="origin_image zh-lightbox-thumb" width="819" data-original="https://pic3.zhimg.com/v2-41196349cc0ddcd8eb63ee45d2d33786_r.jpg"
                    />
                </noscript>
                <img src="https://pic3.zhimg.com/80/v2-41196349cc0ddcd8eb63ee45d2d33786_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="819" data-rawheight="451" class="origin_image zh-lightbox-thumb lazy" width="819" data-original="https://pic3.zhimg.com/v2-41196349cc0ddcd8eb63ee45d2d33786_r.jpg"
                data-actualsrc="https://pic3.zhimg.com/v2-41196349cc0ddcd8eb63ee45d2d33786_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图16 Linux下配置IPv6路由</p>
            <p>三、路由器2配置</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic1.zhimg.com/v2-5630a8236b9abc20bbf5eafbc7869d18_b.jpg" data-caption="" data-size="normal" data-rawwidth="813" data-rawheight="450" class="origin_image zh-lightbox-thumb" width="813" data-original="https://pic1.zhimg.com/v2-5630a8236b9abc20bbf5eafbc7869d18_r.jpg"
                    />
                </noscript>
                <img src="https://pic1.zhimg.com/80/v2-5630a8236b9abc20bbf5eafbc7869d18_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="813" data-rawheight="450" class="origin_image zh-lightbox-thumb lazy" width="813" data-original="https://pic1.zhimg.com/v2-5630a8236b9abc20bbf5eafbc7869d18_r.jpg"
                data-actualsrc="https://pic1.zhimg.com/v2-5630a8236b9abc20bbf5eafbc7869d18_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图17 Linux下配置IPv6路由</p>
            <p>四、服务器静态路由配置</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic2.zhimg.com/v2-a9adcd6d73d88cc8abc133472fbb83a9_b.jpg" data-caption="" data-size="normal" data-rawwidth="758" data-rawheight="217" class="origin_image zh-lightbox-thumb" width="758" data-original="https://pic2.zhimg.com/v2-a9adcd6d73d88cc8abc133472fbb83a9_r.jpg"
                    />
                </noscript>
                <img src="https://pic2.zhimg.com/80/v2-a9adcd6d73d88cc8abc133472fbb83a9_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="758" data-rawheight="217" class="origin_image zh-lightbox-thumb lazy" width="758" data-original="https://pic2.zhimg.com/v2-a9adcd6d73d88cc8abc133472fbb83a9_r.jpg"
                data-actualsrc="https://pic2.zhimg.com/v2-a9adcd6d73d88cc8abc133472fbb83a9_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图18 服务器配置IPv6路由</p>
            <p>五、结果</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic3.zhimg.com/v2-1c42d9db535cab5e6a7496e339e3312e_b.jpg" data-caption="" data-size="normal" data-rawwidth="552" data-rawheight="625" class="origin_image zh-lightbox-thumb" width="552" data-original="https://pic3.zhimg.com/v2-1c42d9db535cab5e6a7496e339e3312e_r.jpg"
                    />
                </noscript>
                <img src="https://pic3.zhimg.com/80/v2-1c42d9db535cab5e6a7496e339e3312e_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="552" data-rawheight="625" class="origin_image zh-lightbox-thumb lazy" width="552" data-original="https://pic3.zhimg.com/v2-1c42d9db535cab5e6a7496e339e3312e_r.jpg"
                data-actualsrc="https://pic3.zhimg.com/v2-1c42d9db535cab5e6a7496e339e3312e_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图19 客户端访问服务器</p>
            <p>客户端可以顺利ping通服务器。可以看到，IPv6下的路由配置，无论是windows还是linux，与IPv4的配置差别不大，熟悉IPv4各个平台路由配置的同学可以很快上手IPv6的路由配置。</p>
            <p><b>3、IPv6的web服务</b>
            </p>
            <p>复用2的架构，在服务器端部署一个web服务，在客户端访问该web服务。web服务没有选择像apache或者nginx这样的庞然大物，而选择了很轻量的boa。原因是boa虽然原始支持IPv6，但是我想粗暴的把所有IPv4的socket套接字都替换成IPv6版本，尝试做一个自定义的升级。结果需要改动的代码非常少，不超过20行，boa就能完全支持IPv6。</p>
            <p>配合实验，写了一个简单的CGI，只是在版面echo字符串。如下图：</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic1.zhimg.com/v2-c42e32dbd7e41a2e7056b9b1c59ca6f8_b.jpg" data-caption="" data-size="normal" data-rawwidth="399" data-rawheight="126" class="content_image" width="399" />
                </noscript>
                <img src="https://pic1.zhimg.com/80/v2-c42e32dbd7e41a2e7056b9b1c59ca6f8_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="399" data-rawheight="126" class="content_image lazy" width="399" data-actualsrc="https://pic1.zhimg.com/v2-c42e32dbd7e41a2e7056b9b1c59ca6f8_b.jpg"
                data-lazy-status="ok">
            </figure>
            <p>图20 浏览器使用IPv6地址访问网络资源</p>
            <p><b>这里值得注意的是，在浏览器中使用IPv6的地址访问web资源，IPv6的地址必须要使用中括号“[]”包起来。</b>
            </p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic1.zhimg.com/v2-3aea18bad9aee85a82e32b66b0bcd5b0_b.jpg" data-caption="" data-size="normal" data-rawwidth="830" data-rawheight="520" class="origin_image zh-lightbox-thumb" width="830" data-original="https://pic1.zhimg.com/v2-3aea18bad9aee85a82e32b66b0bcd5b0_r.jpg"
                    />
                </noscript>
                <img src="https://pic1.zhimg.com/80/v2-3aea18bad9aee85a82e32b66b0bcd5b0_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="830" data-rawheight="520" class="origin_image zh-lightbox-thumb lazy" width="830" data-original="https://pic1.zhimg.com/v2-3aea18bad9aee85a82e32b66b0bcd5b0_r.jpg"
                data-actualsrc="https://pic1.zhimg.com/v2-3aea18bad9aee85a82e32b66b0bcd5b0_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图21 IPv6下的http报文</p>
            <p>从Server端抓包看，IPv6下的Web服务http报文，除了网络层L3的报文头部不一样之外，其余的都和IPv4版本的没有太大差别差别。</p>
            <p><b>4、IPv6的过渡技术实验</b>
            </p>
            <p><b>这部分将在过渡技术介绍中一起实验。</b>
            </p>
            <h2><b>IPv6的过渡技术</b></h2>
            <p>IPv6的提出，最重要的目的就是解决公网IPv4耗尽的问题，而且IPv6协议的设计就考虑到了更加好的效率、安全、扩展等方面，可以那么说，IPv6是未来网络发展的大趋势。但为什么IPv6已经发展了十几年了，目前在我们的工作和生活中还是比较少接触和使用。这里的原因是非常的复杂，有技术上障碍，因为IPv6和IPv4是两个完全不兼容的协议（在极少数的特定场景可以实现兼容），如果要从支持IPv4升级到IPv6，无论是应用程序用客户端、服务器程序端、路由器等等，都要同时支持IPv6才能解决问题，这个的升级改造需要花费的成本是巨大的。而且，正是由于技术上的升级花费大量的人力物力，无论是运营商还是互联网服务商，一方面要重视用户的体验问题，这个肯定不能强制客户更新换代硬件设备和软件，另一方面也要维护自身的投资和利益，更愿意去选择利用现有技术降低IPv4地址耗尽带来的压力，例如NAT的广泛应用，就是IPv6推广使用的一个重要的“障碍”。</p>
            <p>由上所述，IPv4升级到IPv6肯定不会是一蹴而就的，是需要经历一个十分漫长的过渡阶段（用我厂通用的术语说，就是IPv4升级IPv6这个灰度的时间非常长），要数十年的时间都不为过。现阶段，就出现了IPv4慢慢过渡到IPv6的技术（或者叫过渡时期的技术）。过渡技术要解决最重要的问题就是，如何利用现在大规模的IPv4网络进行IPv6的通信。</p>
            <p>要解决上面的问题，这里主要介绍3种过渡技术：</p>
            <p>1、双栈技术</p>
            <p>2、隧道技术</p>
            <p>3、转换技术（有一些文献叫做翻译技术）</p>
            <p>本章节会对以上的过渡技术，选取几个典型的、我们未来最有机会接触到的具体的过渡技术结合实验观察过渡技术的具体实现和数据包的表现形式。</p>
            <h2><b>双栈技术</b></h2>
            <p>这种技术其实很好理解，就是通信节点同时支持IPv4和IPv6双栈。例如在同一个交换机下面有2个Linux的节点，2个节点都是IPv4/IPv6双栈，节点间原来使用IPv4上的UDP协议通信传输，现在需要升级为IPv6上的UDP传输。由于2个节点都支持IPv6，那只要修改应用程序为IPv6的socket通信基本达到目的了。</p>
            <p>上面的例子在局域网通信的改造是很容易的。但是在广域网，问题就变得十分复杂了。因为主要问题是在广域网上的2个节点间往往经过多个路由器，按照双栈技术的部署要求，之间的所有节点都要支持IPv4/IPv6双栈，并且都要配置了IPv4的公网IP才能正常工作，这里就无法解决IPv4公网地址匮乏的问题。因此，双栈技术一般不会直接部署到网络中，而是配合其他过渡技术一起使用，例如在隧道技术中，在隧道的边界路由器就是双栈的，其他参与通信的节点不要求是双栈的。</p>
            <h2><b>隧道技术</b></h2>
            <p>当前的网络是以IPv4为主，因此尽可能地充分利用IPv4网络进行IPv6通信是十分好的手段之一。隧道技术就是这样子的一种过渡技术。</p>
            <p>隧道将IPv6的数据报文封装在IPv4的报文头部后面（IPv6的数据报文是IPv4的载荷部分），IPv6通信节点之间传输的IPv6数据包就可以穿越IPv4网络进行传输。隧道技术的一个很重要的优点是透明性，通过隧道进行通信的两个IPv6节点（或者节点上的应用程序）几乎感觉不到隧道的存在。</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic2.zhimg.com/v2-fb2f40e44b4dac19bac18de33a03326d_b.jpg" data-caption="" data-size="normal" data-rawwidth="1620" data-rawheight="474" class="origin_image zh-lightbox-thumb" width="1620" data-original="https://pic2.zhimg.com/v2-fb2f40e44b4dac19bac18de33a03326d_r.jpg"
                    />
                </noscript>
                <img src="https://pic2.zhimg.com/80/v2-fb2f40e44b4dac19bac18de33a03326d_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="1620" data-rawheight="474" class="origin_image zh-lightbox-thumb lazy" width="1620" data-original="https://pic2.zhimg.com/v2-fb2f40e44b4dac19bac18de33a03326d_r.jpg"
                data-actualsrc="https://pic2.zhimg.com/v2-fb2f40e44b4dac19bac18de33a03326d_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图22 IPv6典型的隧道</p>
            <p>上图是一种典型的隧道技术：路由器-路由器隧道，两个IPv6网络中的主机通过隧道方式穿越了IPv4进行通信。其中C节点和D节点被称为边界路由器，边界路由器必须要支持IPv4-IPv6双栈。当IPv6网络1的主机A将IPv6数据包发给边界路由器C，C对IPv6数据包进行IPv4封装，然后在IPv4网络上进行传输，发送到边界路由器D，D收到IPv4的数据包后剥掉IPv4的包头，还原IPv6的数据包，发送到IPv6网络2的主机B。</p>
            <p>根据隧道的出口入口的构成，隧道可以分为路由器-路由器，主机-路由器隧道、路由器-主机、主机-主机隧道等类型。</p>
            <p>隧道的类型也分为手动配置类型和自动配置类型两种，手动配置是指点对点的隧道是手动加以配置，例如手动配置点对点隧道外层的IPv4地址才能建立起隧道；自动配置是指隧道的建立和卸载是动态的，一般会把隧道外层的IPv4地址内嵌到数据包的目的IPv6地址里面，在隧道路由器获取该IPv6地址时候取出内嵌IPv4地址从而使用该IPv4地址作为隧道的对端来建立隧道。</p>
            <p>下面就介绍几种我们很可能会接触到的具体的隧道技术。</p>
            <p>在介绍具体的隧道技术前，特别要说明一下，Linux内核原生支持一种叫做sit（Simple Internet Transition）隧道。这个隧道专门用于IPv6-in-IPv4的数据封装解封和传输，应用十分之广泛，现在很多主流的IPv6隧道技术都能基于sit隧道实现。关于sit隧道的技术实现，可以查阅Linux内核源码 net/ipv6/sit.c 。</p>
            <p><b>1、6to4隧道</b>
            </p>
            <p>6to4是当前使用得比较广泛的一种自动配置隧道技术，这种技术采用特殊的IPv6地址，称为6to4地址，这种地址是以2002开头，接着后面的32位就是内嵌的隧道对端的IPv4地址。当边界路由器收到这类目的地址，取出IPv4地址建立隧道。</p>
            <p>6to4隧道一般用在路由器-路由器、主机-路由器、路由器-主机场景，典型的应用场景是两个IPv6的站点内主机通过6to4隧道进行相互访问。</p>
            <p>6to4隧道的一个限制是内嵌的IPv4地址必须是公网地址。</p>
            <p><b>6to4隧道实验</b>
            </p>
            <p>如下图，就是本次6to4实验中使用的隧道架构，该架构是典型的路由器-路由器隧道，隧道两侧的IPv6网络对隧道的存在无感知。</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic2.zhimg.com/v2-4214391ef8a6f0f345c51ac04173fe6d_b.jpg" data-caption="" data-size="normal" data-rawwidth="1235" data-rawheight="541" class="origin_image zh-lightbox-thumb" width="1235" data-original="https://pic2.zhimg.com/v2-4214391ef8a6f0f345c51ac04173fe6d_r.jpg"
                    />
                </noscript>
                <img src="https://pic2.zhimg.com/80/v2-4214391ef8a6f0f345c51ac04173fe6d_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="1235" data-rawheight="541" class="origin_image zh-lightbox-thumb lazy" width="1235" data-original="https://pic2.zhimg.com/v2-4214391ef8a6f0f345c51ac04173fe6d_r.jpg"
                data-actualsrc="https://pic2.zhimg.com/v2-4214391ef8a6f0f345c51ac04173fe6d_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图23 6to4路由器-路由器隧道</p>
            <p>在Linux下的sit隧道可以自适应为6to4隧道。</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic1.zhimg.com/v2-4d2d4be9a2069db35c120a99b8ba3bf4_b.jpg" data-caption="" data-size="normal" data-rawwidth="814" data-rawheight="420" class="origin_image zh-lightbox-thumb" width="814" data-original="https://pic1.zhimg.com/v2-4d2d4be9a2069db35c120a99b8ba3bf4_r.jpg"
                    />
                </noscript>
                <img src="https://pic1.zhimg.com/80/v2-4d2d4be9a2069db35c120a99b8ba3bf4_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="814" data-rawheight="420" class="origin_image zh-lightbox-thumb lazy" width="814" data-original="https://pic1.zhimg.com/v2-4d2d4be9a2069db35c120a99b8ba3bf4_r.jpg"
                data-actualsrc="https://pic1.zhimg.com/v2-4d2d4be9a2069db35c120a99b8ba3bf4_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图24 Linux下配置sit隧道（6to4）</p>
            <p>上图就是在路由器上配置sit隧道的命令，因为是使用6to4隧道，隧道的目的端点地址是从目的地址中获取，因此只需要配置本地端点即可。</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic1.zhimg.com/v2-4eb34edcc2f08944900dfb97933be010_b.jpg" data-caption="" data-size="normal" data-rawwidth="410" data-rawheight="123" class="content_image" width="410" />
                </noscript>
                <img src="https://pic1.zhimg.com/80/v2-4eb34edcc2f08944900dfb97933be010_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="410" data-rawheight="123" class="content_image lazy" width="410" data-actualsrc="https://pic1.zhimg.com/v2-4eb34edcc2f08944900dfb97933be010_b.jpg"
                data-lazy-status="ok">
            </figure>
            <p>图25 浏览器通过隧道访问web服务</p>
            <p>配置完隧道后，使用客户端访问web服务，可以正常访问。</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic2.zhimg.com/v2-746e15a4da3487305dcd95db65a8b5e9_b.jpg" data-caption="" data-size="normal" data-rawwidth="811" data-rawheight="445" class="origin_image zh-lightbox-thumb" width="811" data-original="https://pic2.zhimg.com/v2-746e15a4da3487305dcd95db65a8b5e9_r.jpg"
                    />
                </noscript>
                <img src="https://pic2.zhimg.com/80/v2-746e15a4da3487305dcd95db65a8b5e9_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="811" data-rawheight="445" class="origin_image zh-lightbox-thumb lazy" width="811" data-original="https://pic2.zhimg.com/v2-746e15a4da3487305dcd95db65a8b5e9_r.jpg"
                data-actualsrc="https://pic2.zhimg.com/v2-746e15a4da3487305dcd95db65a8b5e9_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图26 web服务器端抓取http报文</p>
            <p>在web服务端抓取http报文，可以看到，web服务获取到就是一个普通的http请问报文。</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic4.zhimg.com/v2-ebb4ef84c53a997506f7a0d719901827_b.jpg" data-caption="" data-size="normal" data-rawwidth="767" data-rawheight="772" class="origin_image zh-lightbox-thumb" width="767" data-original="https://pic4.zhimg.com/v2-ebb4ef84c53a997506f7a0d719901827_r.jpg"
                    />
                </noscript>
                <img src="https://pic4.zhimg.com/80/v2-ebb4ef84c53a997506f7a0d719901827_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="767" data-rawheight="772" class="origin_image zh-lightbox-thumb lazy" width="767" data-original="https://pic4.zhimg.com/v2-ebb4ef84c53a997506f7a0d719901827_r.jpg"
                data-actualsrc="https://pic4.zhimg.com/v2-ebb4ef84c53a997506f7a0d719901827_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图27 隧道内抓取http报文</p>
            <p>在隧道内抓取http报文，可以看到里面的乾坤。这个不是一般的http报文，它比服务端抓取到的多了一层IPv4报文头部，是隧道的外出通信协议，隧道内层IPv6才是真正的数据。IPv4报文头部中的协议字段，不是我们熟悉的TCP(6)/UDP(17)协议，而是IPv6-in-IPv4专属的隧道协议类型。</p>
            <p>可以看到，经过隧道的数据报文，在隧道两端的边界路由器分别完成了隧道协议的封包和解包，在真正获取到数据的节点看来，几乎不感知隧道的存在。</p>
            <p><b>2、ISATAP隧道</b>
            </p>
            <p>ISATAP全称是站点内自动隧道寻址协议（Intra-Site Automatic Tunnel Addressing Protocol），用来为IPv4网络中的IPv6双栈节点可以跨越IPv4网络访问外部的IPv6节点。</p>
            <p>ISATAP隧道一般用于主机-主机、主机-路由器的场景。</p>
            <p><b>ISATAP隧道实验</b>
            </p>
            <p>如下图就是本次实验使用的架构，是一种典型的主机-路由器场景。实验中需要在路由器2上部署radvd服务，用于客户端进行无状态自动配置地址。Linux下的ISATAP隧道也是可以使用sit隧道实现。</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic2.zhimg.com/v2-28b8ac8019d7c957b827820d495e6a0d_b.jpg" data-caption="" data-size="normal" data-rawwidth="1180" data-rawheight="527" class="origin_image zh-lightbox-thumb" width="1180" data-original="https://pic2.zhimg.com/v2-28b8ac8019d7c957b827820d495e6a0d_r.jpg"
                    />
                </noscript>
                <img src="https://pic2.zhimg.com/80/v2-28b8ac8019d7c957b827820d495e6a0d_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="1180" data-rawheight="527" class="origin_image zh-lightbox-thumb lazy" width="1180" data-original="https://pic2.zhimg.com/v2-28b8ac8019d7c957b827820d495e6a0d_r.jpg"
                data-actualsrc="https://pic2.zhimg.com/v2-28b8ac8019d7c957b827820d495e6a0d_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图28 ISATAP主机-路由器隧道</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic3.zhimg.com/v2-c8f62625c2ba9a9dac7d8360f5ce6292_b.jpg" data-caption="" data-size="normal" data-rawwidth="572" data-rawheight="189" class="origin_image zh-lightbox-thumb" width="572" data-original="https://pic3.zhimg.com/v2-c8f62625c2ba9a9dac7d8360f5ce6292_r.jpg"
                    />
                </noscript>
                <img src="https://pic3.zhimg.com/80/v2-c8f62625c2ba9a9dac7d8360f5ce6292_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="572" data-rawheight="189" class="origin_image zh-lightbox-thumb lazy" width="572" data-original="https://pic3.zhimg.com/v2-c8f62625c2ba9a9dac7d8360f5ce6292_r.jpg"
                data-actualsrc="https://pic3.zhimg.com/v2-c8f62625c2ba9a9dac7d8360f5ce6292_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图29 Windows下配置ISATAP隧道</p>
            <p>实验用的客户端使用windows 7，原生支持ISATAP隧道，如上图，需要进入netsh开启并且设置ISATAP的路由器地址（支持域名）。</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic4.zhimg.com/v2-0a67335b25e63873a037fead3815d0f3_b.jpg" data-caption="" data-size="normal" data-rawwidth="823" data-rawheight="615" class="origin_image zh-lightbox-thumb" width="823" data-original="https://pic4.zhimg.com/v2-0a67335b25e63873a037fead3815d0f3_r.jpg"
                    />
                </noscript>
                <img src="https://pic4.zhimg.com/80/v2-0a67335b25e63873a037fead3815d0f3_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="823" data-rawheight="615" class="origin_image zh-lightbox-thumb lazy" width="823" data-original="https://pic4.zhimg.com/v2-0a67335b25e63873a037fead3815d0f3_r.jpg"
                data-actualsrc="https://pic4.zhimg.com/v2-0a67335b25e63873a037fead3815d0f3_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图30 ISATAP隧道中的无状态自动配置</p>
            <p>当客户端设置完router后，隧道已经建立，客户端便发起了无状态自动配置流程，可以看到上面的截图路由器通过隧道将前缀信息下发给客户端，客户端完成无状态自动配置，获取到公网IP地址。</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic4.zhimg.com/v2-70870500c6f17f10074de1975ad939d3_b.jpg" data-caption="" data-size="normal" data-rawwidth="582" data-rawheight="404" class="origin_image zh-lightbox-thumb" width="582" data-original="https://pic4.zhimg.com/v2-70870500c6f17f10074de1975ad939d3_r.jpg"
                    />
                </noscript>
                <img src="https://pic4.zhimg.com/80/v2-70870500c6f17f10074de1975ad939d3_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="582" data-rawheight="404" class="origin_image zh-lightbox-thumb lazy" width="582" data-original="https://pic4.zhimg.com/v2-70870500c6f17f10074de1975ad939d3_r.jpg"
                data-actualsrc="https://pic4.zhimg.com/v2-70870500c6f17f10074de1975ad939d3_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图31 ISATAP隧道接口地址</p>
            <p>在windows 7上查看ISATAP接口，获取到公网地址。这个地址类型是ISATAP专用的地址结构，由64位全球单播路由前缀:200(0):5e5f:w.x.y.z组成(w.x.y.z是客户端的IPv4地址)。</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <figure data-size="normal">
                <noscript>
                    <img src="https://pic2.zhimg.com/v2-d754bce8087bd5d38beb4de17da17469_b.jpg" data-caption="" data-size="normal" data-rawwidth="863" data-rawheight="470" class="origin_image zh-lightbox-thumb" width="863" data-original="https://pic2.zhimg.com/v2-d754bce8087bd5d38beb4de17da17469_r.jpg"
                    />
                </noscript>
                <img src="https://pic2.zhimg.com/80/v2-d754bce8087bd5d38beb4de17da17469_1440w.jpg" data-caption="" data-size="normal" data-rawwidth="863" data-rawheight="470" class="origin_image zh-lightbox-thumb lazy" width="863" data-original="https://pic2.zhimg.com/v2-d754bce8087bd5d38beb4de17da17469_r.jpg"
                data-actualsrc="https://pic2.zhimg.com/v2-d754bce8087bd5d38beb4de17da17469_b.jpg" data-lazy-status="ok">
            </figure>
            <p>图32 使用ISATAP隧道访问web服务</p>
            <p>如上图，使用ISATAP隧道访问web服务，在隧道内的数据抓包，可以看到和6to4的类似，这里就不再深入阐述。</p>
            <p><b>3、Teredo隧道</b>
            </p>
            <p>前面的隧道技术，主要是在IPv4的数据报文承载着IPv6的数据报文，这是一种特殊的数据包格式（IPV6-in-IPv4），不同于我们熟悉的TCP、UDP等传输层协议。而我们平常接触到的网络都存在于NAT架构中（例如我们的办公网络和家庭网络），在这种网络架构中，路由器仅对于TCP、UDP等传输层协议做NAT处理，而无法正确处理IPv6-in-IPv4这种报文，例如使用ISATAP隧道，IPv6双栈节点与ISATAP路由器之前如果存在NAT，ISATAP建立隧道失败；6to4隧道也会遇到同样的问题。</p>
            <p>Teredo隧道是有微软公司主导的一项隧道技术，主要用于在NAT网络架构下建立穿越NAT的隧道。</p>
            <p>Teredo隧道的核心思路，是将IPv6的数据封装成IPv4的UDP数据包，利用NAT对IPv4的UDP支持进行穿越NAT的传输，当UDP包到达隧道的另外一端后，再把IPv4的包头、UDP包头剥离，还原IPv6的数据包，再进行下一步的IPv6数据通信转发。Teredo节点会分配一个以2001::/32的前缀，而且地址中还包含Teredo的服务器、标志位和客户端外部映射模糊地址和端口号等信息。</p>
            <p>Teredo的实现还会遇到NAT的类型不同而被限制的问题。NAT的类型有锥形NAT、受限制的NAT、对称NAT几种，Teredo只能在锥形NAT和受限制的NAT的环境下正常工作，而且在这两种NAT需要处理的逻辑又是不一样的。因此Teredo整体的实现会比较复杂。</p>
            <p><b>实验环境搭建：</b>
            </p>
            <p>在Linux平台下有开源的Teredo实现版本：miredo。由于时间和文章篇幅的原因，而且部署miredo比较复杂，因此这里的实验等以后有机会再补充。:(</p>
            <h2><b>转换技术（有一些文献叫做：翻译技术）</b></h2>
            <p>隧道技术是比较好地解决了在很长期一段时间内还是IPv4网络是主流的情况下IPv6节点（或者双栈节点）间的通信问题。但是由于IPv4到IPv6的过渡是十分漫长的，因此也需要解决IPv6节点与IPv4节点通信的问题。协议转换技术可以用来解决这个问题。</p>
            <p>协议转换技术根据协议在网络中位置的不同，分为网络层协议转换、传输层协议转换和应用层协议转换等。协议转换技术的核心思路就是在IPv4和IPv6通信节点之间部署中间层，将IPv4和IPv6相互映射转换。</p>
            <p>我们非常熟悉的NAT也是一种典型的协议转换技术，是将私网IPv4地址映射转换为公网IPv4地址，这种转换技术又称为NAT44。而我们接着要重点介绍的名为NAT64/DNS64的协议转换技术。</p>
            <p class="ztext-empty-paragraph">
                <br>
            </p>
            <p><b>想了解更多请点击原文：<a href="https://link.zhihu.com/?target=https%3A//cloud.tencent.com/developer/article/1088685%3FfromSource%3Dwaitui" class=" external" target="_blank" rel="nofollow noreferrer" data-za-detail-view-id="1043"><span class="invisible">https://</span><span class="visible">cloud.tencent.com/devel</span><span
