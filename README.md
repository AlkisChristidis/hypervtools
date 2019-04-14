# hypervtools
In order to convert a vmdk image to a vmx one, follow these steps:

1. Run powershell as admin

2. Load the vm utils script module:  
	`Import-Module VirtualMachineConverter\MvmcCmdlet.psd1`

3. Use the dsfk tools to exctract descriptor metadata from the image:  
	`dsfok\dsfo.exe .\whatever.vmdk 512 1024 descriptor.txt`

4. Edit the desriptor file:  
~~~~
# Disk DescriptorFile
version=1
CID=4ca346e5
parentCID=ffffffff
createType="streamOptimized"

# Extent description
RDONLY 23068672 SPARSE "generated-stream.vmdk"

# The Disk Data Base
#DDB

ddb.adapterType = "lsilogic"
ddb.geometry.cylinders = "1435"
ddb.geometry.heads = "255"
ddb.geometry.sectors = "63"
ddb.longContentID = "8eee45efc32a5d377f6bff2731641530"
#ddb.toolsInstallType = "2"  <------------- Comment out this thing here
ddb.toolsVersion = "0"
ddb.virtualHWVersion = "12"
~~~~

5. Flush the descriptor metatada back into the image:  
	`dsfok\dsfi.exe .\whatever.vmdk 512 1024 descriptor.txt`

6. Convert the vm:  
	`ConvertTo-MvmcVirtualHardDisk -SourceLiteralPath .\whatever.vmdk -VhdType DynamicHardDisk -VhdFormat Vhdx -DestinationLiteralPath .\whatever.vmdx -Verbose`
