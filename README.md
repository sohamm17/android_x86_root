# android_x86_root

These steps will elaborate how to enable root access for Android in
x86-based machine from a terminal in the host Android itself(Not through `adb`). I am using 
[Intel's Celadon
branch](https://01.org/projectceladon/documentation/getting_started/build-source) 
for Android 8.1-x86_64. 

This is not a permanent solution to enable root. However, it still works in a
straightforward way.

1. Change the sepolicy from `enforcing` to `permissive` in 
   `device/intel/project-celadon/celadon/mixins.spec`. Now, we can at least see 
   the `su` binary in `/system/xbin` from a devloper mode Terminal app in Android. 
   However, we still cannot execute the `su` binary for other users 
   through the Terminal app because of file permissions (execute access is not
   available for `other` users).
2. To enable execute access for `other` users, we need to change the
   `system/core/libcutils/fs_config.cpp` file. Find and change the line to 
   `{ 04755, AID_ROOT, AID_SHELL,     0, "system/xbin/su" }` to enable execute 
   permission of the `su` binary.
3. Finally, we need to change the `su` program itself in
   `system/extras/su/su.cpp`. I commented out the following line in the `main`
   function: `if (current_uid != AID_ROOT && current_uid != AID_SHELL) error(1,
   0, "not allowed");`
   This is because we want to enable access to any program (in our case the
   Terminal app) so that it can change the user to `root`.
   Note that we also have to comment out the line right before it with
   `uid_t current_uid = getuid();` since unused variables are marked as errors during compilation.

The Terminal app is available by enabling the developer mode in Android
settings.
