---

copyright:
  years: 2014, 2018
lastupdated: "2018-08-02"

---
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}

# Linux에서 MPIO iSCSI LUN에 연결

다음 지시사항은 RHEL6/Centos6용입니다. 다른 OS에 대한 참고사항이 추가되었지만 이 문서에는 모든 Linux 배포판이 포함되지는 **않습니다**. 다른 Linux 운영 체제를 사용 중인 경우에는 사용자에 해당하는 배포 문서를 참조하고 다중 경로가 경로 우선순위에 대해 ALUA를 지원하는지 확인하십시오. 

예를 들어, iSCSI 이니시에이터 구성([여기](https://help.ubuntu.com/lts/serverguide/iscsi-initiator.html){:new_window:}) 및 DM 다중 경로 설정([여기](https://help.ubuntu.com/lts/serverguide/multipath-setting-up-dm-multipath.html){:new_window})에 대한 Ubuntu의 지시사항을 찾을 수 있습니다.

시작하기 전에 {{site.data.keyword.blockstoragefull}} 볼륨에 액세스할 호스트를 [{{site.data.keyword.slportal}}](https://control.softlayer.com/){:new_window}을 통해 이미 권한 부여했는지 확인하십시오.

1. {{site.data.keyword.blockstorageshort}} 나열 페이지에서 새 볼륨을 찾고 **조치**를 클릭하십시오.
2. **호스트 권한 부여**를 클릭하십시오.
3. 목록에서 볼륨에 대한 액세스 권한이 있는 호스트를 선택하고 **제출**을 클릭하십시오.

## {{site.data.keyword.blockstorageshort}} 볼륨 마운트

다음은 Linux 기반의 {{site.data.keyword.BluSoftlayer_full}} 컴퓨팅 인스턴스를 다중 경로 입력/출력(MPIO) iSCSI(internet Small Computer System Interface) LUN(Logical Unit Number)에 연결하는 데 필요한 단계입니다.

**참고:** 지시사항에서 참조하는 호스트 IQN, 사용자 이름, 비밀번호 및 대상 주소는 [{{site.data.keyword.slportal}}](https://control.softlayer.com/){:new_window}의 **{{site.data.keyword.blockstorageshort}} 세부사항** 화면에서 얻을 수 있습니다.

**참고:** 방화벽을 우회하는 VLAN을 통해 스토리지 트래픽을 실행하는 것이 가장 좋습니다. 소프트웨어 방화벽을 통해 스토리지 트래픽을 실행하면 대기 시간이 늘어나서 결국 스토리지 성능이 저하됩니다.

1. 호스트에 iSCSI 및 다중 경로 유틸리티를 설치하십시오.
   - RHEL/CentOS

   ```
   yum install iscsi-initiator-utils device-mapper-multipath
   ```
   {: pre}

   - Ubuntu/Debian

   ```
   sudo apt-get update
   sudo apt-get install multipath-tools
   ```
   {: pre}

2. 다중 경로 구성 파일을 작성하거나 편집하십시오.
   - 다음 명령에서 제공되는 최소 구성으로 **/etc/multipath.conf**를 편집하십시오. <br /><br /> **참고:** RHEL7/CentOS7의 경우, `multipath.conf`는 OS에 기본으로 제공되는 구성이 있기 때문에 공백으로 둘 수 있습니다. Ubuntu는 다중 경로 도구에 빌드되므로 multipath.conf를 사용하지 않습니다.

   ```
   defaults {
   user_friendly_names no
   max_fds max
   flush_on_last_del yes
   queue_without_daemon no
   dev_loss_tmo infinity
   fast_io_fail_tmo 5
   }
   # All data under blacklist must be specific to your system.
   blacklist {
   wwid "SAdaptec*"
   devnode "^hd[a-z]"
   devnode "^(ram|raw|loop|fd|md|dm-|sr|scd|st)[0-9]*"
   devnode "^cciss.*"  
   }
   devices {
   device {
   vendor "NETAPP"
   product "LUN"
   path_grouping_policy group_by_prio
   features "3 queue_if_no_path pg_init_retries 50"
   prio "alua"
   path_checker tur
   failback immediate
   path_selector "round-robin 0"
   hardware_handler "1 alua"
   rr_weight uniform
   rr_min_io 128
   }
   }
   ```
   {: codeblock}

3. 다중 경로 모듈을 로드하고 다중 경로 서비스를 시작한 후 부팅 시에 시작되도록 설정하십시오.
   - RHEL 6
     ```
     modprobe dm-multipath
     ```
     {: pre}

     ```
     service multipathd start
     ```
     {: pre}

     ```
     chkconfig multipathd on
     ```
     {: pre}

   - CentOS 7
     ```
     modprobe dm-multipath
     ```
     {: pre}

     ```
     systemctl start multipathd
     ```
     {: pre}

     ```
     systemctl enable multipathd
     ```
     {: pre}

   - Ubuntu
     ```
     service multipath-tools start
     ```
     {: pre}

   - 다른 배포판의 경우, OS 공급업체 문서를 참조하십시오.

4. 다중 경로가 작동 중인지 확인하십시오.
   - RHEL 6
     ```
     multipath -l
     ```
     {: pre}

     공백을 리턴하면 작동 중인 것입니다.

   - CentOS 7
     ```
     multipath -ll
     ```
     {: pre}

     RHEL 7/CentOS 7은 No fc_host device를 리턴할 수 있지만 이는 무시해도 됩니다.

5. `/etc/iscsi/initiatorname.iscsi` 파일을 {{site.data.keyword.slportal}}의 IQN으로 업데이트하십시오. 값은 소문자로 입력하십시오.
   ```
   InitiatorName=<value-from-the-Portal>
   ```
   {: pre}
6. {{site.data.keyword.slportal}}의 사용자 이름 및 비밀번호를 사용하여 `/etc/iscsi/iscsid.conf`에서 CHAP 설정을 편집하십시오. CHAP 이름에는 대문자를 사용하십시오.
   ```
    node.session.auth.authmethod = CHAP
    node.session.auth.username = <Username-value-from-Portal>
    node.session.auth.password = <Password-value-from-Portal>
    discovery.sendtargets.auth.authmethod = CHAP
    discovery.sendtargets.auth.username = <Username-value-from-Portal>
    discovery.sendtargets.auth.password = <Password-value-from-Portal>
   ```
   {: codeblock}

   **참고:** 다른 CHAP 설정은 주석 처리된 채로 두십시오. {{site.data.keyword.BluSoftlayer_full}} 스토리지는 단방향 인증만 사용합니다. 상호 CHAP를 사용하지 마십시오.

7. iSCSI가 부팅 시에 시작되도록 설정하고 지금 시작하십시오.
   - RHEL 6

      ```
      chkconfig iscsi on
      ```
      {: pre}
      ```
            chkconfig iscsid on
      ```
      {: pre}

      ```
      service iscsi start
      ```
      {: pre}

      ```
      service iscsid start
      ```
      {: pre}

   - CentOS 7

      ```
      systemctl enable iscsi
      ```
      {: pre}

      ```
      systemctl enable iscsid
      ```
      {: pre}

      ```
      systemctl start iscsi
      ```
      {: pre}

      ```
      systemctl start iscsid
      ```
      {: pre}

   - 다른 배포판의 경우 OS 공급업체 문서를 참조하십시오.

8. {{site.data.keyword.slportal}}에서 확보한 대상 IP 주소를 사용하여 디바이스를 검색하십시오.

     A. iSCSI 배열에 대해 검색을 실행하십시오..
     ```
          iscsiadm -m discovery -t sendtargets -p <ip-value-from-SL-Portal>
     ```
     {: pre}

     B. iSCSI 배열에 자동 로그인되도록 호스트를 설정하십시오.
     ```
          iscsiadm -m node -L automatic
     ```
     {: pre}

9. 호스트가 iSCSI 배열에 로그인되어 해당 세션을 유지보수하는지 확인하십시오.
   ```
   iscsiadm -m session
   ```
   {: pre}

   ```
   multipath -l
   ```
   {: pre}

   이 명령은 경로를 보고합니다.

10. 디바이스가 연결되었는지 확인하십시오. 기본적으로, 디바이스는 `/dev/mapper/mpathX`에 연결되며 여기서 X는 연결된 디바이스에 대해 생성된 ID입니다.
    ```
    fdisk -l | grep /dev/mapper
    ```
    {: pre}
  이 명령은 다음 예와 비슷한 내용을 보고합니다.
    ```
    Disk /dev/mapper/3600a0980383030523424457a4a695266: 73.0 GB, 73023881216 bytes
    ```
  이제 볼륨이 마운트되어 호스트에서 액세스 가능합니다.

## 파일 시스템 작성(선택사항)

다음 단계를 따라 새로 마운트된 볼륨에서 파일 시스템을 작성하십시오. 파일 시스템은 볼륨을 사용하는 대부분의 애플리케이션에서 필요합니다. 2TB 미만의 드라이브에는 `fdisk`를 사용하고 2TB 이상인 디스크에 대해서는 `parted`를 사용하십시오.

### `fdisk` 사용

1. 디스크 이름을 가져오십시오.
   ```
   fdisk -l | grep /dev/mapper
   ```
   {: pre}
   리턴되는 디스크 이름은 `/dev/mapper/XXX`와 비슷하게 표시됩니다.

2. 디스크에서 파티션을 작성하십시오.

   ```
   fdisk /dev/mapper/XXX
   ```
   {: pre}

   XXX는 1단계에서 리턴된 디스크 이름을 나타냅니다. <br />

   **참고**: 더 아래로 스크롤하여 `fdisk` 명령 표에 나열된 명령 코드를 확인하십시오.

3. 새 파티션에서 파일 시스템을 작성하십시오.

   ```
   fdisk –l /dev/mapper/XXX
   ```
   {: pre}

   - 새 파티션은 `XXXlp1`과 유사하게 디스크와 함께 나열되며 크기, 유형(83), Linux 다음에 표시됩니다.
   - 파티션 이름은 다음 단계에서 필요하기 때문에 기록해 두십시오. (XXXlp1이 파티션 이름입니다.)
   - 파일 시스템을 작성하십시오.

     ```
     mkfs.ext3 /dev/mapper/XXXlp1
     ```
     {: pre}

4. 파일 시스템에 대한 마운트 지점을 작성하고 이를 마운트하십시오.
   - 파티션 이름 `PerfDisk` 또는 파일 시스템을 마운트하려는 위치를 작성하십시오.

     ```
     mkdir /PerfDisk
     ```
     {: pre}

   - 파티션 이름을 사용하여 스토리지를 마운트하십시오.
     ```
     mount /dev/mapper/XXXlp1 /PerfDisk
     ```
     {: pre}

   - 새 파일 시스템이 나열되는지 확인하십시오.
     ```
     df -h
     ```
     {: pre}

5. 새 파일 시스템을 시스템의 `/etc/fstab` 파일에 추가하여 부팅 시에 자동 마운팅이 사용되도록 설정하십시오.
   - 다음 행을 `/etc/fstab`의 끝에 추가하십시오(3단계의 파티션 이름 사용). <br />

     ```
     /dev/mapper/XXXlp1    /PerfDisk    ext3    defaults,_netdev    0    1
     ```
     {: pre}

#### `fdisk` 명령 표

<table border="0" cellpadding="0" cellspacing="0">
	<caption><code>fdisk</code> 명령 표의 왼쪽에는 명령이 표시되고 오른쪽에는 예상되는 결과가 표시됩니다.</caption>
    <thead>
	<tr>
		<th style="width:40%;">명령</th>
		<th style="width:60%;">결과</th>
	</tr>
    </thead>
    <tbody>
	<tr>
		<td><code>Command: n</code></td>
		<td>새 파티션을 작성합니다. &#42;</td>
	</tr>
	<tr>
		<td><code>Command action: p</code></td>
		<td>파티션이 1차 파티션이 됩니다.</td>
	</tr>
	<tr>
		<td><code>Partition number (1-4): 1</code></td>
		<td>디스크에서 파티션 1이 됩니다.</td>
	</tr>
	<tr>
		<td><code>First cylinder (1-8877): 1 (default)</code></td>
		<td>실린더 1에서 시작합니다.</td>
	</tr>
	<tr>
		<td><code>Last cylinder, +cylinders or +size {K, M, G}: 8877 (default)</code></td>
		<td>Enter를 눌러 마지막 실린더로 이동하십시오.</td>
	</tr>
	<tr>
		<td><code>Command: t</code></td>
		<td>파티션 유형을 설정합니다. &#42;</td>
	</tr>
	<tr>
		<td><code>Select partition 1.</code></td>
		<td>파티션 1이 특정 유형으로 설정되도록 선택합니다.</td>
	</tr>
	<tr>
		<td><code>Hex code: 83</code></td>
		<td>유형으로 Linux를 선택합니다(83은 Linux의 16진 코드). &#42;&#42;</td>
	 </tr>
	<tr>
		<td><code>Command: w</code></td>
		<td>디스크에 새 파티션 정보를 기록합니다. &#42;</td>
	</tr>
   </tbody>
</table>

  (`*`)도움말을 보려면 m을 입력하십시오.

  (`**`)16진 코드를 나열하려면 L을 입력하십시오.

### `parted` 사용

많은 Linux 배포판에서 `parted`는 미리 설치되어 제공됩니다. distro에 포함되어 있지 않다면 다음으로 설치할 수 있습니다.
- Debian/Ubuntu
  ```
  sudo apt-get install parted  
  ```
  {: pre}

- RHEL/CentOS
  ```
  yum install parted  
  ```
  {: pre}


`parted`를 사용하여 파일 시스템을 작성하려면 다음 단계를 수행하십시오.

1. `parted`를 실행하십시오.

   ```
   parted
   ```
   {: pre}

2. 디스크에서 파티션을 작성하십시오.
   1. 다르게 지정된 경우가 아니면 `parted`에서는 기본 드라이브를 사용합니다. 기본 드라이브는 대부분의 경우 `/dev/sda`입니다. **select** 명령을 사용하여 파티션하려는 디스크로 전환하십시오. **XXX**는 새 디바이스 이름으로 대체하십시오.

      ```
      (parted) select /dev/mapper/XXX
      ```
      {: pre}

   2. `print`를 실행하여 올바른 디스크에서 작업 중인지 확인하십시오.

      ```
      (parted) print
      ```
      {: pre}

   3. GPT 파티션 표를 작성하십시오.

      ```
      (parted) mklabel gpt
      ```
      {: pre}

   4. `Parted`는 기본 및 논리 디스크 파티션 작성에 사용할 수 있으며 수행 단계는 동일합니다. 파티션을 작성하기 위해 `parted`에서는 `mkpart`를 사용합니다. 작성하려는 파티션 유형에 따라 **primary** 또는 **logical**과 같은 기타 매개변수를 제공할 수도 있습니다.
   <br /> **참고**: 나열된 단위는 기본적으로 MB 단위이며 10GB 파티션을 작성하려면 1에서 시작하여 10000에서 끝나야 합니다. 필요한 경우 `(parted) unit TB`를 입력하여 크기 단위를 TB로 변경할 수도 있습니다.

      ```
      (parted) mkpart
      ```
      {: pre}

   5. `quit`로 `parted`를 종료하십시오.

      ```
      (parted) quit
      ```
      {: pre}

3. 새 파티션에서 파일 시스템을 작성하십시오.

   ```
   mkfs.ext3 /dev/mapper/XXXlp1
   ```
   {: pre}

   **참고**: 이 명령을 실행할 때 올바른 디스크와 파티션을 선택하는 것이 중요합니다.
   파티션 표를 인쇄하여 결과를 확인하십시오. 파일 시스템 열에 ext3이 표시되어야 합니다.

4. 파일 시스템에 대한 마운트 지점을 작성하고 이를 마운트하십시오.
   - 파티션 이름 `PerfDisk` 또는 파일 시스템을 마운트하려는 위치를 작성하십시오.

     ```
     mkdir /PerfDisk
     ```
     {: pre}

   - 파티션 이름을 사용하여 스토리지를 마운트하십시오.

     ```
     mount /dev/mapper/XXXlp1 /PerfDisk
     ```
     {: pre}

   - 새 파일 시스템이 나열되는지 확인하십시오.

     ```
     df -h
     ```
     {: pre}

5. 새 파일 시스템을 시스템의 `/etc/fstab` 파일에 추가하여 부팅 시에 자동 마운팅이 사용되도록 설정하십시오.
   - 다음 행을 `/etc/fstab`의 끝에 추가하십시오(3단계의 파티션 이름 사용). <br />

     ```
     /dev/mapper/XXXlp1    /PerfDisk    ext3    defaults    0    1
     ```
     {: pre}




## MPIO가 `*NIX` OSes에서 제대로 구성되었는지 확인

다중 경로에서 디바이스를 선택하는지 확인하려면 디바이스를 나열하십시오. 올바르게 구성된 경우 두 개의 NETAPP 디바이스만 표시됩니다.

```
# multipath -l
```
{: pre}

```
root@server:~# multipath -l
3600a09803830304f3124457a45757067 dm-1 NETAPP,LUN C-Mode size=20G features='1 queue_if_no_path' hwhandler='0' wp=rw
|-+- policy='round-robin 0' prio=-1 status=active`
6:0:0:101 sdd 8:48 active undef running `-+- policy='round-robin 0' prio=-1 status=enabled`
7:0:0:101 sde 8:64 active undef running
```

디스크가 있는지 확인하십시오. ID가 같은 디스크가 두 개 있으며 ID가 같고 크기가 같은 `/dev/mapper`가 나열되는지 확인하십시오. `/dev/mapper` 디바이스는 다중 경로에서 설정하는 디바이스입니다.

```
# fdisk -l | grep Disk
```
{: pre}

```
root@server:~# fdisk -l | grep Disk
Disk /dev/sda: 500.1 GB, 500107862016 bytes Disk identifier: 0x0009170d
Disk /dev/sdc: 21.5 GB, 21474836480 bytes Disk identifier: 0x2b5072d1
Disk /dev/sdb: 21.5 GB, 21474836480 bytes Disk identifier: 0x2b5072d1
Disk /dev/mapper/3600a09803830304f3124457a45757066: 21.5 GB, 21474836480 bytes Disk identifier: 0x2b5072d1
```

올바르게 설정되지 않은 경우 이 예제와 같이 표시됩니다.
```
No multipath output root@server:~# multipath -l root@server:~#
```

이 명령은 블랙리스트된 디바이스를 표시합니다.
```
# multipath -l -v 3 | grep sd <date and time>
```
{: pre}

```
root@server:~# multipath -l -v 3 | grep sd Feb 17 19:55:02
| sda: device node name blacklisted Feb 17 19:55:02
| sdb: device node name blacklisted Feb 17 19:55:02
| sdc: device node name blacklisted Feb 17 19:55:02
| sdd: device node name blacklisted Feb 17 19:55:02
| sde: device node name blacklisted Feb 17 19:55:02
```

`fdisk`에서는 `sd*` 디바이스만 표시하고 `/dev/mapper`는 표시하지 않습니다.

```
# fdisk -l | grep Disk
```
{: pre}

```
root@server:~# fdisk -l | grep Disk
Disk /dev/sda: 500.1 GB, 500107862016 bytes Disk identifier: 0x0009170d
Disk /dev/sdc: 21.5 GB, 21474836480 bytes Disk identifier: 0x2b5072d1
Disk /dev/sdb: 21.5 GB, 21474836480 bytes Disk identifier: 0x2b5072d1
```
