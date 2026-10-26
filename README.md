# Docker Desktop

## install wsl

enable the Windows Subsystem for Linux (WSL) on Windows 11

https://learn.microsoft.com/en-us/windows/wsl/install
https://learn.microsoft.com/en-us/windows/wsl/setup/environment
https://learn.microsoft.com/en-us/windows/wsl/tutorials/linux

```cmd
wsl --install
```

Open Windows PowerShell

To clean up and shrink the large docker_data.vhdx file on Windows, you must first remove unused Docker resources and then compact the virtual hard disk, as VHDX files do not shrink automatically.

## Prune docker data

Run the following commands to remove unused images, containers, volumes, and build cache. This marks space as free inside the virtual disk but does not shrink the file itself.

```cmd
docker system prune -a --volumes -f
docker builder prune --all -f
```

## shutdown wsl

You must stop all WSL instances to allow the VHDX to be modified.

```cmd
wsl --shutdown
```

## compact the vhdx

Run PowerShell as Administrator to compact the file.  The path may vary depending on your Docker Desktop layout (docker_data.vhdx or ext4.vhdx).

For Windows Pro/Enterprise (using Hyper-V module):

```cmd
Import-Module Hyper-V
Optimize-VHD -Path "$env:LOCALAPPDATA\Docker\wsl\disk\docker_data.vhdx" -Mode Full
```

For Windows Home or if Hyper-V is unavailable (using diskpart):

```cmd
diskpart
select vdisk file="$env:LOCALAPPDATA\Docker\wsl\disk\docker_data.vhdx"
attach vdisk readonly
compact vdisk
detach vdisk
exit
```

> - **Note:**
> - or file="C:\Users\t.chanpiseth\AppData\Local\Docker\wsl\disk\docker_data.vhdx"

## start docker

Restarting Docker Desktop will recreate fresh, small VHDX files.

```cmd
wsl -d debian
```

## Alternative: Full Reset

If pruning does not free enough space, you can purge all data via the Docker Desktop GUI (click the bug icon in the header) or by unregistering the WSL distribution.  Warning: This deletes all containers, images, and volumes.

```cmd
wsl --shutdown
wsl --unregister docker-desktop-data
wsl --unregister docker-desktop
```
