# Simple_Safe_File_System-SSFS-
In this project, i gonna implement a simple file system. My implementation, in C, will run in user space and will interact with a virtual disk driver for all the needed operations with the disk.

# Virtual disk
The disk is emulated by dividing a (disk image) file into fixed-size sectors. 

**Assume that the sector size is 1024 bytes.**

The virtual disk driver API is composed of the following functions:
```
int vdisk_on(char *filename, DISK *diskp);
int vdisk_read(DISK *diskp, uint32_t sector, uint8_t *buffer);
int vdisk_write(DISK *diskp, uint32_t sector, uint8_t *buffer);
int vdisk_sync(DISK *diskp);
void vdisk_off(DISK *diskp);
```

* vdisk_on(char *filename, DISK *diskp) must be called once before any further use of the virtual disk.</br>
– filename is a null-terminated string specifying the disk image file to be used.</br>
– diskp is a "return parameter", that is a pointer to caller allocated space, which then gets initialized, with data of type DISK, by vdisk_on. This pointer must then be passed to all the other functions of the virtual disk driver, as a way to identify the virtual disk.</br>
– Return value: 0 on success; vdisk_EACCESS if the user does not have appropriate permissions to manipulate the disk image file; vdisk_ENOEXIST if the disk image file does
not exist; vdisk_ENODISK if the size of the disk image file is smaller than the size of a
sector; −1 in the event of an unknown error1</br>

* int vdisk_read(DISK *diskp, uint32_t sector, uint8_t *buffer) reads a sector from
the virtual disk.</br>
– diskp identifies the virtual disk</br>
– sector is the sector number of the virtual disk sector to be read (sectors are numbered
sequentially from 0)</br>
– buffer is a pointer to a buffer that will receive the content of the sector being read. It
is the programmer’s responsibility to ensure the buffer has enough capacity.</br>
– Return value: 0 on success; vdisk_ESECTOR if the sector could only be read partially
(damaged sector); vdisk_EEXCEED if the requested sector is beyond the end of the
virtual disk; vdisk_ENODISK if vdisk_on was either not called or failed, or vdisk_off
was called, prior to this call.</br>
* int vdisk_write(DISK *diskp, uint32_t sector, uint8_t *buffer) writes content to
a sector on the virtual disk.</br>
– diskp identifies the virtual disk</br>
– sector is the sector number of the virtual disk sector to be written (sectors are numbered sequentially from 0)</br>
– buffer is a pointer to a buffer with the content of the sector to be written. A number
of bytes equal to the sector size is written.</br>
– Return value: 0 on success; vdisk_ESECTOR if the sector could only be partially written
(damaged sector); vdisk_EEXCEED if the requested sector is beyond the end of the
virtual disk; vdisk_ENODISK if vdisk_on was either not called or failed, or vdisk_off
was called, prior to this call.</br>

* vdisk_sync(DISK *diskp) transfers ("flushes") all modifications to the virtual disk image.
– diskp identifies the virtual disk</br>
– Return value: 0 on success; vdisk_ENODISK if vdisk_on was either not called or failed,
or vdisk_off was called, prior to this call.</br>
• vdisk_off(DISK *diskp) discards all modifications that have not yet been transferred to
the virtual disk image. After this call, the virtual disk can no longer be used without a new
call to vdisk_on.</br>
– diskp identifies the virtual disk</br>
**Note that none of these functions can change the size of the disk image file.**

