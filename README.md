#!/bin/bash
#clean logs
partitionnumber=864
rm -rf ./gettime/seclectedtime_${partitionnumber}.txt
rm -rf ./gettime/seq_${partitionnumber}.txt
rm -rf ./gettime/seclectedtime2_${partitionnumber}.txt
rm -rf ./gettime/seq2_${partitionnumber}.txt
rm -rf ./gettime/sqlseclectedtime_${partitionnumber}.txt
echo -e "\033[33m [Delete old txt!]${i} \033[0m"
#getselectedtime from logs
i=0
for file in /home/sparkuser/tpcds/run_tpcds/logs/*
do
# echo "$(basename ${file})"
 str="${partitionnumber}_.log"
 tmp=$(basename ${file})
 result=$(echo $tmp | grep "${str}")
 echo "$result"
 if [[ $result != "" ]]
 then
 i=`expr $i + 1`
# echo -e "\033[32m sucess \033[0m"
 tac ${file}|sed -n '1p'|tac >>./gettime/seq_${partitionnumber}.txt
 tac ${file}|sed -n '4p'|cut -d '(' -f2|cut -d ')' -f1|sed -n '1p'|tac >>./gettime/seclectedtime_${partitionnumber}.txt
 echo -e "\033[32m [GET TIME SUCCESS]${i} \033[0m"
 # sed -n '/sr127:10001> ;/,/^Closing/p' $file >/home/sparkuser/tpcds/run_tpcds/transf/$(basename ${file} .log).csv #extract tables
 fi
done
echo -e "\033[32m [Extract and Merge txt]${i} \033[0m"
awk -F" " '{print $6 }' ./gettime/seq_${partitionnumber}.txt >>./gettime/seq2_${partitionnumber}.txt
awk -F" " '{print $1 }' ./gettime/seclectedtime_${partitionnumber}.txt >>./gettime/seclectedtime2_${partitionnumber}.txt
paste ./gettime/seq2_${partitionnumber}.txt ./gettime/seclectedtime2_${partitionnumber}.txt >./gettime/sqlseclectedtime_${partitionnumber}.txt
