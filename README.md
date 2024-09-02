# znpool
一个又快又好的全球免费代理轮询工具
原理：
1、通过网络空间搜索引擎检索全球的socks代理
2、批量异步根据自定义条件进行筛选
3、监听一个端口提供socks5服务，然后轮询代理提供服务。

config文件配置说明：
[listener]#监听的IP和端口，提供给客户端连接socks5代理来轮询软件提供的代理
IP='127.0.0.1'
PORT='9115'
[checkSocks]
#通过访问实际url来验证代理的可用性
checkURL='https://www.baidu.com'#可以配置为要访问的目标地址，确保所有代理都能访问到目标地址
checkRspKeywords='百度一下'#上面地址原始响应中的某个字符串，用来验证通过代理访问目标时有无因某种原因被ban掉。
maxConcurrentReq='15'#同时最多N个并发通过代理访问上面的地址，检测socks5代理是否可用，可根据网络环境调整。云主机的话开500、1000都可以，本机的话，开三五十差不多。
timeout='6'#单位秒，验证socks5代理的超时时间,建议保持在5或6，检查及使用代理访问上面的地址时，超过这个时间，判定无效
[checkSocks.checkGeolocate]
#通过访问返回IP归属地信息的URL和关键字判断，来排除某些代理，如：某些情况下，真正要访问的系统限制只有大陆地区IP可以访问
switch='close' #open:启用，非open:禁用
checkURL='https://qifu-api.baidubce.com/ip/local/geo/v1/district'#访问此url获取IP归属地信息，出于某些原因，建议用国内的公司的相关接口。
#下面的两个的值，需要根据上面url的返回内容填写
excludeKeywords=['澳门','香港','台湾']#格式如：['澳门','香港']优先级最高，返回的body内容中，存在任一关键字，则跳过，
includeKeywords=['中国']#格式如：['中国','北京']则只获取中国北京的代理，如果是['中国'],排除上述关键字的前提下则获取中国所有其他地区代理
[FOFA]
switch = 'close' #open:启用，非open:禁用
apiUrl='https://fofa.info/api/v1/search/all'
email = '****@****.com'
key = '**********'
queryString = 'protocol=="socks5" && country="CN" && banner="Method:No Authentication"'#官方语法
resultSize='500' #此处最大为10000,需小于等于从官网web界面看到的结果数量
[QUAKE]
switch = 'close' 
apiUrl='https://quake.360.net/api/v3/search/quake_service'
key = '**********'
queryString = 'service:socks5  AND country: "CN" AND response:"No authentication"'#官方语法
resultSize='500' #此处最大为10000,需小于等于从官网web界面看到的结果数量
[HUNTER]
switch = 'close'
apiUrl='https://hunter.qianxin.com/openApi/search'
key = '**********'
queryString = 'protocol=="socks5"&&protocol.banner="No authentication"&&ip.country="CN"'#官方语法
resultSize='300' #最小为100,按100条/每页翻页，最大值需小于从官网web界面看到的结果数量，值需为100的整倍数，如200、300、1000、2000等


使用场景1：
配置好空间搜索引擎，fofa，quake，hunter等，可以多个组合使用，也可以单独使用，通过switch开关进行控制。直接搜索结果去重测试，然后提供轮询代理服务器。







使用场景2：
使用pools.txt文件内的代理，关闭搜索引擎，程序会自动使用pools.txt里面的代理进行轮询，使用之前也会去重验证文件里的代理是否满足我们的要求。







使用场景3：
多条件筛选，可以修改配置文件[checkSocks.checkGeolocate]项中的excludeKeywords、includeKeywords来进行代理地区的匹配。或者修改[checkSocks]项中的验证地址来针对性的验证代理对于目标的可访问性，适合于批量攻击测试某目标时。
更多的玩法可以自己开发，搜索引擎的语句也可以自己配置。

已经编译了win、linux和macos系统的可执行文件。

