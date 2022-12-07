Refer either the Assignment.docx or the Assignment_Kubernetes.rtf
Images referred are in folder AssignmentPic.
I have copied the content of rtf file here.
Activities:



(A) Pods created.

[root@ip-172-31-10-214 k8s-specifications]# kubectl apply -f .
deployment.apps/db created
service/db created
deployment.apps/redis created
service/redis created
deployment.apps/result created
service/result created
deployment.apps/vote created
service/vote created
deployment.apps/worker created


(B) Pods Running

[root@ip-172-31-10-214 k8s-specifications]# kubectl get pods
NAME                      READY   STATUS    RESTARTS   AGE
db-b54cd94f4-vklsw        1/1     Running   0          80s
redis-868d64d78-5hdfz     1/1     Running   0          80s
result-5d57b59f4b-9p4gr   1/1     Running   0          80s
vote-94849dc97-smz4x      1/1     Running   0          79s
worker-dd46d7584-k9mxn    1/1     Running   0          79s


[root@ip-172-31-10-214 k8s-specifications]# kubectl get all
NAME                          READY   STATUS    RESTARTS   AGE
pod/db-b54cd94f4-vklsw        1/1     Running   0          10m
pod/redis-868d64d78-5hdfz     1/1     Running   0          10m
pod/result-5d57b59f4b-9p4gr   1/1     Running   0          10m
pod/vote-94849dc97-smz4x      1/1     Running   0          10m
pod/worker-dd46d7584-k9mxn    1/1     Running   0          10m

NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/db           ClusterIP   10.97.112.142   <none>        5432/TCP         10m
service/kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP          40h
service/redis        ClusterIP   10.101.20.206   <none>        6379/TCP         10m
service/result       NodePort    10.99.82.158    <none>        5001:31003/TCP   10m
service/vote         NodePort    10.97.123.133   <none>        5000:31002/TCP   10m

NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/db       1/1     1            1           10m
deployment.apps/redis    1/1     1            1           10m
deployment.apps/result   1/1     1            1           10m
deployment.apps/vote     1/1     1            1           10m
deployment.apps/worker   1/1     1            1           10m

NAME                                DESIRED   CURRENT   READY   AGE
replicaset.apps/db-b54cd94f4        1         1         1       10m
replicaset.apps/redis-868d64d78     1         1         1       10m
replicaset.apps/result-5d57b59f4b   1         1         1       10m
replicaset.apps/vote-94849dc97      1         1         1       10m
replicaset.apps/worker-dd46d7584    1         1         1       10m



Questions:
Delete them in same order 
1st ► VOTE pod ► Observe what happens both in frontEnd & in Unix


Front end Observations:

·      No impact to the frontend or voting application even when pod is deleted.
·      Another Voter pod got started or replaced the old one.
·      Both Vote app and Result app are running properly.  
 
Unix/Terminal:
 
·      Even after deleting the pod, the pod is still running, the pod got recreated. 


[root@ip-172-31-10-214 k8s-specifications]# kubectl get pods
NAME                      READY   STATUS    RESTARTS   AGE
db-b54cd94f4-vklsw        1/1     Running   0          46m
redis-868d64d78-5hdfz     1/1     Running   0          46m
result-5d57b59f4b-9p4gr   1/1     Running   0          46m
vote-94849dc97-smz4x      1/1     Running   0          46m
worker-dd46d7584-k9mxn    1/1     Running   0          46m
[root@ip-172-31-10-214 k8s-specifications]# kubectl delete pod vote-94849dc97-smz4x
pod "vote-94849dc97-smz4x" deleted
^[[O[root@ip-172-31-10-214 k8s-specifications]# kubectl get pods
NAME                      READY   STATUS    RESTARTS   AGE
db-b54cd94f4-vklsw        1/1     Running   0          49m
redis-868d64d78-5hdfz     1/1     Running   0          49m
result-5d57b59f4b-9p4gr   1/1     Running   0          49m
vote-94849dc97-56vlw      1/1     Running   0          84s
worker-dd46d7584-k9mxn    1/1     Running   0          49m
[root@ip-172-31-10-214 k8s-specifications]# 


2nd ► WORKER pod ► Observe what happens both in frontEnd & in Unix


Front end Observations:
 
·      No impact to the frontend user. 
·      Both Vote app and Result app are running properly. 
 
Backend 
·      No impact, the pod again got recreated due to replica set in the deployment file.
·      The logs pertaining to old worker instance were lost. 
 
Unix /terminal:
·      The worker got recreated and all pods are running, please refer the below information.


[root@ip-172-31-10-214 k8s-specifications]# kubectl delete pod worker-dd46d7584-k9mxn
pod "worker-dd46d7584-k9mxn" deleted
^[[O[root@ip-172-31-10-214 k8s-specifications]# kubectl get pods
NAME                      READY   STATUS    RESTARTS   AGE
db-b54cd94f4-vklsw        1/1     Running   0          56m
redis-868d64d78-5hdfz     1/1     Running   0          56m
result-5d57b59f4b-9p4gr   1/1     Running   0          56m
vote-94849dc97-56vlw      1/1     Running   0          8m45s
worker-dd46d7584-kxzdf    1/1     Running   0          81s


3rd ► DB pod ► Observe what happens both in frontEnd & in Unix

Front end:
 
Result app is not showing any votes posted using the voting app. Please refer image 4 & 5. You will see the there is no change in result app even when there is change in the voter app.
 
Unix:
 
Db pod got recreated after getting deleted. Please refer the below logs.

[root@ip-172-31-10-214 k8s-specifications]# kubectl delete pod db-b54cd94f4-vklsw
pod "db-b54cd94f4-vklsw" deleted

[root@ip-172-31-10-214 k8s-specifications]# kubectl get pods
NAME                      READY   STATUS    RESTARTS   AGE
db-b54cd94f4-bzmh6        1/1     Running   0          89s
redis-868d64d78-5hdfz     1/1     Running   0          120m
result-5d57b59f4b-9p4gr   1/1     Running   0          120m
vote-94849dc97-56vlw      1/1     Running   0          72m
worker-dd46d7584-kxzdf    1/1     Running   1          64m



Making the Result app to work again:

To recover the result app, delete the result app, it gets restarted due to replica set and gets connected to the DB again,
 
Unix/Terminal logs:

[root@ip-172-31-10-214 k8s-specifications]# kubectl delete pod result-5d57b59f4b-9p4gr
pod "result-5d57b59f4b-9p4gr" deleted

[O[root@ip-172-31-10-214 k8s-specifications]# kubectl get pods
NAME                      READY   STATUS    RESTARTS   AGE
db-b54cd94f4-bzmh6        1/1     Running   0          8m51s
redis-868d64d78-5hdfz     1/1     Running   0          127m
result-5d57b59f4b-n2cv2   1/1     Running   0          61s
vote-94849dc97-56vlw      1/1     Running   0          79m
worker-dd46d7584-kxzdf    1/1     Running   1          72m
[root@ip-172-31-10-214 k8s-specifications]# 


Front end observation:
 
·  On restart the result app start from 1 again, All the previous votes are lost.  Please refer image3, image 4.
·  After that, the result app works and shows the newly cast votes. Please refer image 5.
