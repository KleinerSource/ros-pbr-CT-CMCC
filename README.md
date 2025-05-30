电信移动 策略路由规则

ip段信息取自 https://ispip.clang.cn

由Github Action自动构建于此。

策略路由分流有两种实现方法：

方法一：
**ros-pbr-CT-CMCC.rsc** 是往route - rules 里生ip段列表。

ros script 例子

```
:local addr "https://cdn.jsdelivr.net/gh/jacyl4/chnroute@main/ros-pbr-CT-CMCC.rsc"
:local result ([/tool fetch mode=https output=user url=$addr as-value])
:if ($result->"status" = "finished") do={
/file remove [find name="ros-pbr-CT-CMCC.rsc"]
/tool fetch url=$addr
/ip route rule remove [find table=main]
/ip route rule remove [find table=CT]
/ip route rule remove [find table=CMCC]
/ip route rule remove [find table=CT2]
/ip route rule remove [find table=CT3]
/ip route rule add src-address=10.0.0.14/32 action=lookup table=main
/ip route rule add src-address=10.0.101.2/32 action=lookup table=CT2
/ip route rule add src-address=10.0.102.2/32 action=lookup table=CT3
/ip route rule add src-address=10.0.2.2/32 action=lookup table=CMCC
/import ros-pbr-CT-CMCC.rsc
}
```

方法二：
**ros-dpbr-CT-CMCC.rsc** 是往Firewall - address lists 里生ip段列表。
```
:local addr "https://cdn.jsdelivr.net/gh/jacyl4/chnroute@main/ros-dpbr-CT-CMCC.rsc"
:local result ([/tool fetch mode=https output=user url=$addr as-value])
:if ($result->"status" = "finished") do={
/file remove [find name="ros-dpbr-CT-CMCC.rsc"]
/tool fetch url=$addr
/ip firewall address-list remove [find list="dpbr-CT"]
/ip firewall address-list remove [find list="dpbr-CMCC"]
/import ros-dpbr-CT-CMCC.rsc
}
```

这个可以用于Firewall - mangle页，通过dst-addrss= 引用
