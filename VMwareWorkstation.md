# VMware Workstation

## This virtual machine appears to be in use.

If this virtual machine is not in use, press the "Take Ownership" button to obtain ownership of it. Otherwise, press the "Cancel" button to avoid damaging it.

Configuration file: E:\vmware\workstation\CentOS-206\CentOS-206.vmx.

出现这原因是因为，虚拟机非正常关闭导致，错误操作可能会导致虚拟机被删除

正确修复步骤：

1. press the "Cancel button"
2. visit "E:\vmware\workstation\CentOS-206"
3. delete all dirs that end with "lck" which means "lock". eg. CentOS-206.vmdk.lck CentOS-206.vmx.lck
4. start the virtual machine
