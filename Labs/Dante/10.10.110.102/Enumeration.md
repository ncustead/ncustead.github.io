┌──(root㉿kali)-[/home/kali/ligolo-ng]
└─# curl -i http://10.10.110.102:8080/api/python                  
HTTP/1.1 200 OK
Date: Wed, 06 Dec 2023 23:22:04 GMT
X-Content-Type-Options: nosniff
X-Jenkins: 2.63
X-Jenkins-Session: 3d45ffbf
Content-Type: text/x-python;charset=UTF-8
Content-Length: 523
Server: Jetty(9.4.z-SNAPSHOT)

{"_class":"hudson.model.Hudson","assignedLabels":[{}],"mode":"NORMAL","nodeDescription":"the master Jenkins node","nodeName":"","numExecutors":2,"description":None,"jobs":[],"overallLoad":{},"primaryView":{"_class":"hudson.model.AllView","name":"all","url":"http://10.10.110.102:8080/"},"quietingDown":False,"slaveAgentPort":-1,"unlabeledLoad":{"_class":"jenkins.model.UnlabeledLoadStatistics"},"useCrumbs":True,"useSecurity":True,"views":[{"_class":"hudson.model.AllView","name":"all","url":"http://10.10.110.102:8080/"}]}  


┌──(root㉿kali)-[/home/kali/ligolo-ng]
└─# curl -i http://10.10.110.102:8080/api/json
HTTP/1.1 200 OK
Date: Wed, 06 Dec 2023 23:24:08 GMT
X-Content-Type-Options: nosniff
X-Jenkins: 2.63
X-Jenkins-Session: 3d45ffbf
Content-Type: application/json;charset=utf-8
Content-Length: 523
Server: Jetty(9.4.z-SNAPSHOT)

{"_class":"hudson.model.Hudson","assignedLabels":[{}],"mode":"NORMAL","nodeDescription":"the master Jenkins node","nodeName":"","numExecutors":2,"description":null,"jobs":[],"overallLoad":{},"primaryView":{"_class":"hudson.model.AllView","name":"all","url":"http://10.10.110.102:8080/"},"quietingDown":false,"slaveAgentPort":-1,"unlabeledLoad":{"_class":"jenkins.model.UnlabeledLoadStatistics"},"useCrumbs":true,"useSecurity":true,"views":[{"_class":"hudson.model.AllView","name":"all","url":"http://10.10.110.102:8080/"}]}  


