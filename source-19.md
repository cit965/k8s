|PR|something|commit|point|
|---|---|---|---|
|259|async poll interval|
|261|invalid json deserialize|a2513eb|测试了一下json解码|
|262|etcd key path|6fdebd6||
|264|pod id format|78d9538|pod id和容器名称中的随机数格式|
|*265*|*etcd compare and swap*|
|266|local start script|71c0555|大概是文件改了个名吧|
|*269*|*atomic update*|
|271|htpasswd|4eccd64|宿主机上没有这个二进制怎么办|
|273|docker entrypoint|f8060c5|支持以命令行参数和环境变量传入docker地址|
|275|guestbook frontend port|22f25b9|部署guestbook时暴露的端口|
|276|minion regexp|431fcac|支持从命令行传入主机匹配规则|
|277|pod status|13d7a59|专门定义pod status|
|279|check docker ps|c002cac|宿主机上没有docker二进制怎么办|
|282|empty list|28afe91|manifests list为空也处理|
|283|public stringset|3d1e8a9|stringSet挪到util，变成一个通用模块|
|**286**|**scheduler**|
|287|kubecfg|01a6711|cloudcfg改名了，文档也要改|
|289|NewStringSet|b65d685|StringSet的构造方法|
|290|log noise|6c79937|优化了一下注释和日志输出|
|292|collaborative|05fe417|协作文档|
|293|minion fqdn suffix|d5516e4|跟gce相关的改动，没啥值得学习的|