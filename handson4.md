1. use -n
maple.csail.mit.edu->128.30.4.223
willow.csail.mit.edu->128.30.4.222

2. calculate time
from 12:34:41.473036 
to 12:34:44.339015
header or sth = 99736
1626520 byte = 1588kb
2.865979
554.1kb/s

3. 
12:34:41.474225 IP 128.30.4.222.39675 > 128.30.4.223.5001: Flags [.], seq 1473:2921, ack 1, win 115, options [nop,nop,TS val 282136474 ecr 282202089], length 1448
12:34:41.482047 IP 128.30.4.223.5001 > 128.30.4.222.39675: Flags [.], ack 2921, win 159, options [nop,nop,TS val 282202095 ecr 282136474], length 0
12:34:41.482059 IP 128.30.4.222.39675 > 128.30.4.223.5001: Flags [.], seq 18849:20297, ack 1, win 115, options [nop,nop,TS val 282136482 ecr 282202095], length 1448
the next one
12:34:41.474992 IP 128.30.4.222.39675 > 128.30.4.223.5001: Flags [.], seq 13057:14505, ack 1, win 115, options [nop,nop,TS val 282136474 ecr 282202090], length 1448
12:34:41.499373 IP 128.30.4.223.5001 > 128.30.4.222.39675: Flags [.], ack 14505, win 331, options [nop,nop,TS val 282202114 ecr 282136474], length 0
12:34:41.499387 IP 128.30.4.222.39675 > 128.30.4.223.5001: Flags [.], seq 41481:42929, ack 1, win 115, options [nop,nop,TS val 282136499 ecr 282202114], length 1448


4. 
sent before ack
white arrows is to the left of transitions

5. 
44000-45000
6. 
182000-183000

7. window size   
8. slope = windowsize/rtt
9. 1500/0.0013/1024 = 1153846.15/1024 =  1126.80kb/s

10. some info       
* some package retransition
* some other message transition
* the total size exclude the header and some data
