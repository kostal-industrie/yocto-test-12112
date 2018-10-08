# Testcase to reproduce Yocto Bug 12112

Details: https://bugzilla.yoctoproject.org/show_bug.cgi?id=12112

Steps:
* Clone repository and initialize submodules (yocto poky 2.5.1)
  ```console
  $ git clone --recurse-submodules https://github.com/kostal-industrie/yocto-test-12112.git
  $ cd yocto-test-12112
  ```
* Initialize environment: 
  ```console
  $ . poky/oe-init-build-env build/
  ```
* Build kernel and u-boot: 
  ```console
  $ bitbake u-boot linux-yocto
  ```
* Backup u-boot.* from tmp/deploy/images
  ```console
  $ cp tmp/deploy/images/beaglebone-yocto/u-boot.img u-boot-clean.img
  $ cp tmp/deploy/images/beaglebone-yocto/u-boot.dtb u-boot-clean.dtb
  ```
* Delete tmp dir
  ```console
  $ rm -rf tmp
  ```
* Build kernel and u-boot from sstate: 
  ```console
  $ bitbake u-boot linux-yocto
  ```
* Copy new u-boot.* from tmp/deploy/images
  ```console
  $ cp tmp/deploy/images/beaglebone-yocto/u-boot.img u-boot-sstate.img
  $ cp tmp/deploy/images/beaglebone-yocto/u-boot.dtb u-boot-sstate.dtb
  ```
* Convert u-boot.dtb back to dts using dtc
  ```console
  $ dtc -I dtb -O dts -s -o u-boot-clean.dts u-boot-clean.dtb
  $ dtc -I dtb -O dts -s -o u-boot-sstate.dts u-boot-sstate.dtb
  ```
* Diff old and new u-boot.dts: /signature/key-dev node is missing in dtb restored from sstate
  ```console
  $ diff u-boot-clean.dts u-boot-sstate.dts
  ```
