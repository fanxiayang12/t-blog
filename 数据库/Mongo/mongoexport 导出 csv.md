mongoexport --help

## 实测
```shell
# mongo
mongo 10.10.10.45:20000/emdataWeixin

# json
mongoexport -h 10.10.10.45 --port 20000 -d emdataWeixin -c wp_scan_data -q '{}' --type json --jsonArray --pretty -o wp_scan_data.json

# csv
mongoexport -h 10.10.10.45 --port 20000 -d emdataWeixin -c wp_scan_data -f historyId,hospitalId,data,createTime,updateTime -q '{}' --type csv -o wp_scan_data.csv
```

json

mongoexport 
		-h 192.168.9.102 
		--port 20000 
		-d dev_doc_docs 		
		-c doc_info 
		-f name,hospitalId 
		-q '{hospitalId : 647}' 
--type json
		--jsonArray 
		--pretty 
		-o doc_info.json

csv

mongoexport 
		-h 192.168.9.102 
		--port 20000 
		-d dev_doc_docs 
		-c doc_info 
		-f name,hospitalId 
		-q '{hospitalId : 647}' 
--type csv 
		-o doc_info.csv

https://docs.mongodb.com/manual/reference/program/mongoexport/index.html

