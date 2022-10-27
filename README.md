# Task2

## Script for collecting disk usage and free disk, memory used and free memory, and cpu
utilization and store them in files with timestamps
 ```
 #!/bin/bash

diskname="disk_info_$(date "+%H:%M:%S-%F")"
memoryname="memory_info_$(date "+%H:%M:%S-%F")"
cpuname="cpu_info_$(date "+%H:%M:%S-%F")"

df -m | awk '{print $3 "\t" $4}' >> /var/www/html/disk/"$diskname".txt
df -m | awk '{s+=$3;t+=$4}END{print "total used: " s"\ntotal available: " t}'  >> /var/www/html/disk/"$diskname".txt

free -m | awk '{print $3 "\t" $4}' >>  /var/www/html/memory/"$memoryname".txt
free -m | awk '{s+=$3;t+=$4}END{print "total used : " s"\ntotal free: " t}' >> /var/www/html/memory/"$memoryname".txt

top -bn1  | awk '{print $1 "\t" $9 "\t" $12}' >> /var/www/html/cpu/"$cpuname".txt
top -bn1  | awk '{if(NR>5){s+=$9}}END{ print "cpu: " s "%"}' >>  /var/www/html/cpu/"$cpuname".txt
```
## Script for collecting the average of the prevuois data 
```
 #!/bin/bash
 
diskname="disk_avg_$(date "+%H:%M:%S-%F")"
memoryname="memory_avg_$(date "+%H:%M:%S-%F")"
cpuname="cpu_avg_$(date "+%H:%M:%S-%F")"

sed -n '/total used/p'  /var/www/html/disk/disk_info*.txt | awk '{s+=$3}END{print "Average disk usage: " s/NR}' >> /var/www/html/disk/"$diskname".txt
sed -n '/total available/p'  /var/www/html/disk/disk_info*.txt | awk '{s+=$3}END{print "Average free disk: " s/NR}' >>/var/www/html/disk/"$diskname".txt


sed -n '/total used/p'  /var/www/html/memory/memory_info*.txt | awk '{s+=$3}END{print "Average memory usage: " s/NR}' >> /var/www/html/memory/"$memoryname".txt
sed -n '/total free/p'  /var/www/html/memory/memory_info*.txt | awk '{s+=$3}END{print "Average free  memory: " s/NR}' >> /var/www/html/memory/"$memoryname".txt

sed -n '/cpu:/p'  /var/www/html/cpu/cpu_info*.txt | awk '{s+=$2}END{print "Average cpu usage: " s/NR}' >> /var/www/html/cpu/"$cpuname".txt
```
## Index.html
```
<!DOCTYPE HTML>
<HTML>

  <HEAD>
    <TITLE>index</TITLE>
  </HEAD>
  <BODY>

    <a href="/disk">disk info</a>
    <a href="/memory">memory info</a>
    <a href="/cpu">cpu info</a>

  </BODY>
</HTML>
```

## Cronjob
```
0 * * * * statistics.bash
0 * * * * average.bash
```
## Result

![main](/main.png)

![disk](/disk.png)
![info](/info.png)
![avg](/avg.png)


















