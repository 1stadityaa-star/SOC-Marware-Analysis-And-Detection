

#To get process_guid:
```
index=endpoint Resume.pdf.exe EventCode=1
```
#To get relevent eventin using processguid
```
index=endpoint {process_guid} | table _time,ParentImage,Image,CommandLine



