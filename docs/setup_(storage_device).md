# Storage Device Setup

### Burnin Test

A new drive should first be thoroughly checked before being introduced into a system.

First run all S.M.A.R.T. tests using smartctl (as root). Be sure that the drive is not mounted or otherwise in use while performing these tests.

```
# smartctl -t short -C /dev/<device>
# smartctl -t conveyance -C /dev/<device>
# smartctl -t long -C /dev/<device>
```

Once those are finished you will want to run `badblocks` to search for bad blocks on the device.

```
# badblocks -b <blocksize> -wsv -o /root/<device>.badblocks /dev/<device>
```

* -b: The blocksize defaults to 1024 but for larger drives (2TB+) you'll probably need to use 4096 
* -o: Create a file with all found badblocks so it may be used by mke2fs or e2fsck
* -w: Use the destructive write-mode test
* -v: Verbose
* -s: Show the progress of the scan

This will take a *long* time to complete.

Since S.M.A.R.T. tests are passive and detect errors after a read or write attempt you should run the `long` test again after `badblocks` completes.

```
# smartctl -t long -C /dev/<device>
```

Once that finishes you can inspect the `/root/<device>.badblocks` and `smartctl`.

```
# smartctl -A /dev/<device>
smartctl 6.2 2013-07-26 r3841 [x86_64-linux-3.13.0-85-generic] (local build)
Copyright (C) 2002-13, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF READ SMART DATA SECTION ===
SMART Attributes Data Structure revision number: 10
Vendor Specific SMART Attributes with Thresholds:
ID# ATTRIBUTE_NAME          FLAG     VALUE WORST THRESH TYPE      UPDATED  WHEN_FAILED RAW_VALUE
  1 Raw_Read_Error_Rate     0x000f   116   100   006    Pre-fail  Always       -       108117320
  3 Spin_Up_Time            0x0003   091   091   000    Pre-fail  Always       -       0
  4 Start_Stop_Count        0x0032   100   100   020    Old_age   Always       -       39
  5 Reallocated_Sector_Ct   0x0033   100   100   010    Pre-fail  Always       -       0
  7 Seek_Error_Rate         0x000f   070   060   030    Pre-fail  Always       -       267023868211
  9 Power_On_Hours          0x0032   084   084   000    Old_age   Always       -       14527
 10 Spin_Retry_Count        0x0013   100   100   097    Pre-fail  Always       -       0
 12 Power_Cycle_Count       0x0032   100   100   020    Old_age   Always       -       32
183 Runtime_Bad_Block       0x0032   098   098   000    Old_age   Always       -       2
184 End-to-End_Error        0x0032   100   100   099    Old_age   Always       -       0
187 Reported_Uncorrect      0x0032   085   085   000    Old_age   Always       -       15
188 Command_Timeout         0x0032   100   097   000    Old_age   Always       -       34360262671
189 High_Fly_Writes         0x003a   099   099   000    Old_age   Always       -       1
190 Airflow_Temperature_Cel 0x0022   062   032   045    Old_age   Always   In_the_past 38 (217 146 43 17 0)
191 G-Sense_Error_Rate      0x0032   012   012   000    Old_age   Always       -       177278
192 Power-Off_Retract_Count 0x0032   100   100   000    Old_age   Always       -       0
193 Load_Cycle_Count        0x0032   098   098   000    Old_age   Always       -       4991
194 Temperature_Celsius     0x0022   038   068   000    Old_age   Always       -       38 (0 17 0 0 0)
195 Hardware_ECC_Recovered  0x001a   116   100   000    Old_age   Always       -       108117320
197 Current_Pending_Sector  0x0012   100   100   000    Old_age   Always       -       160
198 Offline_Uncorrectable   0x0010   100   100   000    Old_age   Offline      -       160
199 UDMA_CRC_Error_Count    0x003e   200   200   000    Old_age   Always       -       0
240 Head_Flying_Hours       0x0000   100   253   000    Old_age   Offline      -       146033183044863
241 Total_LBAs_Written      0x0000   100   253   000    Old_age   Offline      -       81621679594
242 Total_LBAs_Read         0x0000   100   253   000    Old_age   Offline      -       439822455052
```

Probably the most important RAW_VALUEs to look for are ATTRIBUTE_NAME's `Reallocated_Sector_Ct`, `Current_Pending_Sector`, and `Offline_Uncorrectable`. If any of those are greater than zero (0) then there are bad blocks on the drive and if possible the drive should be returned / replaced. In the above example you can see there are 160 bad blocks.

### Filesystem Setup

* [EXT4](setup_(ext4).md)
