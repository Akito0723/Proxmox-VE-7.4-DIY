## Proxmox VE 7.4-17 自用留档
* **内核版本 Linux 6.1.6-1-pve。**
* **包括但不限于换源、直通、界面显示温度/频率等。**


***


### [PVEtools(https://github.com/ivanhao/pvetools)](https://github.com/ivanhao/pvetools)

温度显示，去除订阅，换源

***

### 核显直通(intel)

<details>
<summary>点击展开，查看详细教程！</summary>

#### 待编辑

#### 验证核显直通

 * 在直通后的系统的终端执行：
```
ls /dev/dri
```
 * 出现“renderD128”就成功了


</details>


***



### [SR-IOV(https://github.com/strongtz/i915-sriov-dkms)](https://github.com/strongtz/i915-sriov-dkms)

**允许单个物理设备表现为多个虚拟设备以供不同的虚拟机或容器独立使用**

***

### 直通硬盘(全盘映射)

<details>
<summary>点击展开，查看详细教程！</summary>

#### 查看读取存储设备序列号：
SATA设备
```
ls /dev/disk/by-id | grep ata
```

找出自己的硬盘序列号。比如我的就是：

ata-WDC_WD10xxxxxxxx

#### 执行命令：

 * 102：改成自己要直通硬盘的的虚拟机ID。

 * sata1：已有sata0，所以往后排，为sata1，按需修改。

```
qm set 102 -sata1 /dev/disk/by-id/ata-WDC_WD10xxxxxxxx
```

返回下面信息就说明成功挂载：

update VM 102: -sata1 /dev/disk/by-id/ata-WDC_WD10xxxxxxxx


#### 3.返回PVE查看，已经挂载，重启即可完成。

</details>


***


### 配置PVE的性能/省电模式

* 常理来说Linux的系统CPU模式是性能模式，而PVE是基于debian的系统，所以PVE默认的也是性能模式。

* 可能有些小伙伴的PVE主机性能很强，功耗也高，可以通过设置成省电模式，来降低功耗。

* 具体能降低多少，请自测。

<details>
<summary>点击展开，查看详细教程！</summary>


#### 1.当前PVE主机是否支持多种模式切换，终端执行：
```
cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_available_governors
```

![jpg](./pic/48.jpg)

能返回 `performance` (性能模式) 和 `powersave` (省电模式)等，就接着往下看，如果只有 `performance` 就关闭这个教程。

* 有些机器CPU比较新，可能还有conservative、schedutil、ondemand等，都可以切换。

#### 2.安装cpupower，终端执行：
```
apt-get install linux-cpupower -y
```



#### 3.切换成省电模式，终端执行：
```
cpupower -c all frequency-set -g powersave
```


有多少核，就会返回多少cpu。


* 如果想切换到其他模式：conservative、schedutil、ondemand等

```
cpupower -c all frequency-set -g conservative
```
```
cpupower -c all frequency-set -g schedutil
```
```
cpupower -c all frequency-set -g ondemand
```


* 然后执行： `cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor` ，查看当前CPU处于什么模式。


可以看到已经切成`powersave`省电模式。

* 如果要切回性能模式，就执行： `cpupower -c all frequency-set -g performance` 。


</details>



***
