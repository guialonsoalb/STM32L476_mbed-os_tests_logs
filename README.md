# Issue found related with test "mbed-os-features-storage-nvstore-tests-nvstore-functionality" over boards mounting STM32L476xx and STM32L475xx
After a number of full mbed-os tests run over RHOMBIO_L476DMW1K it was seen that mbed-os-features-storage-nvstore-tests-nvstore-functionality test failed many times with different compilers, specially with ARMC6. We then run same test over several ST boards and found out that it failed even more often than over RHOMBIO board.

A number of single tests "mbed-os-features-storage-nvstore-tests-nvstore-functionality" were then run over following boards:
* DISCO_L475VG_IOT01A
* NUCLEO_L476RG
* DISCO_L476VG
* RHOMBIO_L476DMW1K (L476VG)

## Mbed OS tests logs over target boards using STM32L476 and STM32L475 MCU
Attached there are several logs run over said boards. Logs named "xxxx_log0" include full compile & run test log while the rest show run test log only.

## Just one dummy "printf" seems to help(?) in some way, in some cases, to apparently solve the problem
After digging into the code and adding some debug points in some parts of the code trying to figure out where the problem could be we found out by casuality that adding a dummy `printf("\n");` at the end of `static void nvstore_multi_thread_test()`function found in `mmbed-os/features/storage/nvstore/TESTS/nvstore/functionality/main.cpp` affected in some way helping to pass much more tests than befor, but not all. 

We checked that behaviour by adding said dummy-print on this line: https://github.com/ARMmbed/mbed-os/blob/46603f831e13705d5aab4f73d52f00611e1f4d7d/features/storage/nvstore/TESTS/nvstore/functionality/main.cpp#L565

For example, all tests run with ARMC6 over RHOMBIO_L476DMW1K and DISCO_L476VG failed with original mbed-os while all tests passed OK when dummy-print was added. But same dummy-print compiled with ARMC6 didn't help when tests were run over DISCO_L475VG_IOT01A 

## Other info that may be of help 
* Tests compiled with IAR passed OK when run over all boards but NUCLEO_L476RG
* Tests compiled with ARMC6 seemed to always passed OK with NUCLEO_L476RG but never with the rest 
* Tests compiled with ARMC5 failed in all cases
* Tests compiled with GCC_ARM failed almost in all cases, only passed some times over RHOMBIO_L476DMW1K

## Summary of test results
| Test run: 	mbed-os-features-storage-nvstore-tests-nvstore-functionality |
| -------------------------------------------------------------------------- |
| Compiler & Test_log | DISCO_L475VG_IOT01A | NUCLEO_L476RG | DISCO_L476VG | RHOMBIO_L476DMW1K |
| ------------------- | ------------------- | ------------- | ------------ | ----------------- |
| GCC_ARM_Test-0 | - | - | ERROR | - |
| GCC_ARM_Test-1 | ERROR | ERROR | ERROR | ERROR |
| GCC_ARM_Test-2 | ERROR | ERROR | ERROR | OK |
| GCC_ARM_Test-3 | ERROR | ERROR | ERROR | OK |
| ARMC5_Test-0 | ERROR | - | ERROR | ERROR |
| ARMC5_Test-1 | ERROR | ERROR | ERROR | ERROR |
| ARMC5_Test-2 | ERROR | ERROR | ERROR | ERROR |
| ARMC5_Test-3 | ERROR | ERROR | ERROR | ERROR |
| ARMC6_Test-0 | ERROR | - | - | - |
| ARMC6_Test-1 | ERROR | OK | ERROR | ERROR |
| ARMC6_Test-2 | ERROR | OK | ERROR | ERROR |
| ARMC6_Test-3 | ERROR | OK | ERROR | ERROR |
| IAR_Test-0 | - |-| OK | OK |
| IAR_Test-1 | OK | ERROR | OK | OK |
| IAR_Test-2 | OK | OK | OK | OK |
| IAR_Test-3 | OK | ERROR | OK | OK |

