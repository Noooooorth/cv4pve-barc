## 使用指导：

#### 1.查询可用镜像

![images](./docs/select-image.png "Select image restore")

- 命令：

  ```shell
  eve4pve-barc list images \
      --vmid=101 \
      --label='daily' \
      --path=/tmp/bck
      
  # 参数
  ## vmid: PVE虚拟机ID
  ## label: 备份频率（在这里无实际意义）
  ## path: 备份文件存放位置
  ```

- 输出：

  ```shell
  pool-hdd.vm-101-disk-0
  pool-hdd.vm-102-disk-0
  ...
  ```

#### 2.查询镜像备份的时间戳

![timestamps](./docs/select-time.png "Select time restore")

- 命令：

  ```shell
  eve4pve-barc list timestamps \
      --vmid=101 \
      --label='daily' \
      --path=/tmp/bck \
      --src_image_name="pool-hdd.vm-101-disk-0"
      
  # 参数
  ## vmid: PVE虚拟机ID
  ## label: 备份频率（在这里无实际意义）
  ## path: 备份文件存放位置
  ## src_image_name: 镜像名
  ```

- 输出：

  ```shell
  19-09-07_17:51:20
  19-09-07_17:56:06
  ...
  ```

#### 3.查询 Ceph 池

![pool](./docs/select-pool.png "Select pool destination")

- 命令：

  ```shell
  eve4pve-barc list pools \
      --vmid=101 \
      --label='daily' \
      --path=/tmp/bck
      
  # 参数
  ## vmid: PVE虚拟机ID（在这里无实际意义）
  ## label: 备份频率（在这里无实际意义）
  ## path: 备份文件存放位置（在这里无实际意义）
  ```

- 输出：

  ```shell
  pool-ssd
  pool-hdd
  ...
  ```

#### 4.恢复镜像

- 命令：

  ```shell
  eve4pve-barc restore \
      --vmid=101 \
      --label='daily' \
      --path=/tmp/bck \
      --src_image_name='pool-hdd.vm-101-disk-0' \
      --src_image_timestamp='19-09-07_17:56:06' \
      --pool_name='pool-hdd' \
      --des_image_name='vm-101-disk-0_restore_888'
      
  # 参数
  ## vmid: PVE虚拟机ID
  ## label: 备份频率（在这里无实际意义）
  ## path: 备份文件存放位置
  ## src_image_name: 备份镜像名
  ## src_image_timestamp: 备份镜像的时间戳
  ## pool_name: 恢复出来的镜像的存放位置
  ## des_image_name: 恢复出来的镜像名
  ```

- 输出：

  ```shell
  ACTION: Restore image
  Start restore 2019-09-07 21:58:18
  Initial import 190907175120pool-hdd.vm-101-disk-0.img
  Importing image: 100% complete...done.
  Differential 190907175606pool-hdd.vm-101-disk-0.diff
  Importing image diff: 100% complete...done.
  Remove all snapshots
  Removing all snapshots: 100% complete...done.
  Backup pool-hdd.vm-101-disk-0 restored in pool-hdd/vm-101-disk-0_restore_888 with success!
  Consider to manually create VM/CT and change config file from backup adapting restored image.
  End restore 2019-09-07 21:59:22
  ```

#### 5.合并镜像

- 命令：

  ```shell
  eve4pve-barc assemble \
      --vmid=101 \
      --label='daily' \
      --path=/tmp/bck \
      --src_image_name='pool-hdd.vm-101-disk-0' \
      --src_image_timestamp='19-09-07_17:51:20'
      
  # 参数
  ## vmid: PVE虚拟机ID
  ## label: 备份频率（在这里无实际意义）
  ## path: 备份文件存放位置
  ## src_image_name: 备份镜像名
  ## src_image_timestamp: 备份镜像的时间戳
  ```

- 输出：

  ```shell
  Start assemble process
  Copy image to '/tmp/bck/barc/101/daily/assemble-pool-hdd.vm-101-disk-0'
  Assemble /tmp/bck/barc/101/daily/190907175606pool-hdd.vm-101-disk-0.diff
  Reading metadata
  From snap: barcdaily190907175120
  To snap: barcdaily190907175606
  Image size: 34359738368 (32GB)
  End of metadata
  End of data
  Writing 0 bytes to image
  Backup pool-hdd.vm-101-disk-0 assebled in assemble-pool-hdd.vm-101-disk-0 with success!
  ```

  

