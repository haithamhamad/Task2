# Task2

## Script for collecting disk usage and free disk, memory used and free memory, and cpu
utilization and store them in files with timestamps
 ```
#!/bin/bash

diskname="disk_info_$(date "+%H:%M:%S-%F")"
memoryname="memory_info_$(date "+%H:%M:%S-%F")"
cpuname="cpu_info_$(date "+%H:%M:%S-%F")"

 df | awk '{if($3>1024*1024&&NR>1){s=$3/(1024*1024)"G";d=$4/(1024*1024)"G" ;printf "%-20s%20s%20s\n" ,$1,s, $4}else if($3>1024&&NR>1){s=$3/1024"M";d=$4/(1024*1024)"G" ;printf "%-20s%20s%20s\n",$1 ,s, $4}else if(NR>1){printf "%-20s%19s%s%20s\n",$1 ,$3 ,"K", $4/1024}else{printf "%-20s%20s%20s\n",$1 ,$3, $4 }}'>> /var/www/html/disk/"$diskname".txt
df -m | awk '{s+=$3;t+=$4}END{print "total used: " s"\ntotal available: " t}'  >> /var/www/html/disk/"$diskname".txt

free -m | awk '{print $3"M" "\t" $4"M"}' >>  /var/www/html/memory/"$memoryname".txt
free -m | awk '{s+=$3;t+=$4}END{print "total used: " s"\ntotal free: " t}' >> /var/www/html/memory/"$memoryname".txt

iostat |  awk '{if(NR<5){print $0}}' >> /var/www/html/cpu/"$cpuname".txt
iostat |  awk '{if(NR==4){print "cpu usage: " 100-$6 " %"}}' >>  /var/www/html/cpu/"$cpuname".txt

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

sed -n '/cpu usage:/p'  /var/www/html/cpu/cpu_info*.txt | awk '{s+=$3}END{print "Average cpu usage: " s/NR}' >> /var/www/html/cpu/"$cpuname".txt

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

![diski](https://github.com/haithamhamad/Task2/blob/d3d42e7e0659dff69959577007a39ad32852e333/VirtualBox_CentOs%207.9_03_11_2022_12_50_39.png)
![davg](https://github.com/haithamhamad/Task2/blob/d3d42e7e0659dff69959577007a39ad32852e333/VirtualBox_CentOs%207.9_03_11_2022_12_50_51.png)
![cpui](https://github.com/haithamhamad/Task2/blob/d3d42e7e0659dff69959577007a39ad32852e333/VirtualBox_CentOs%207.9_03_11_2022_12_51_16.png)
![cpuavg](https://github.com/haithamhamad/Task2/blob/d3d42e7e0659dff69959577007a39ad32852e333/VirtualBox_CentOs%207.9_03_11_2022_12_51_26.png)
![memi](https://github.com/haithamhamad/Task2/blob/d3d42e7e0659dff69959577007a39ad32852e333/VirtualBox_CentOs%207.9_03_11_2022_12_51_44.png)
![memavg](https://github.com/haithamhamad/Task2/blob/d3d42e7e0659dff69959577007a39ad32852e333/VirtualBox_CentOs%207.9_03_11_2022_12_51_59.png)

















