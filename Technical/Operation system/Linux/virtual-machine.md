# Virtual Machine
- The most popular virtual technology is QEMU-KVM. It is 2 pieces of software that interconnect together.
	- QEMU: **Q**uick **Emu**lator
	- KVM: **K**ernel-based **V**irtual **M**achine
## VIRSH
```shell
# Define or create a machine
virsh define test-machine.yaml 
# start machine
virsh start TestMachine
# reboot machine
virsh reboot TestMachine
# reset
virsh reset TestMachine
# shutdown gracefully
virsh shutdown TestMachine
# force shutdown
virsh destroy TestMachine
# undefine or remove a machine
virsh undefine TestMachine
# get info of a machine 
virsh doinfo TestMachine
# set vcpu of a machine
virsh setvcpus TestMachine 2 --config
virsh setvcpus TestMachine 2 --config --maximum
# set memory of a machine
virsh setmaxmem TestMachine 2048M --config 
virsh setmem TestMachine 2048M --config 
```