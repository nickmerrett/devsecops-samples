# devsecops-sampples

## Dataexfil pod 
Apply the following yaml to create a pod to run curl from via the pod's terminal. This yaml will create a pod that will sleep infinitly
```
kind: Deployment
apiVersion: apps/v1
metadata:
  name: dataexfil
  namespace: isv3
spec:
  replicas: 1
  selector:
    matchLabels:
      app: curly
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: curly
    spec:
      containers:
        - name: curly
          image: >-
            registry.access.redhat.com/ubi8/ubi-minimal@sha256:3e1adcc31c6073d010b8043b070bd089d7bf37ee2c397c110211a6273453433f
          command:
            - /bin/sh
          args:
            - '-c'
            - sleep infinity
          resources: {}
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
          imagePullPolicy: IfNotPresent
      restartPolicy: Always
      terminationGracePeriodSeconds: 30
      dnsPolicy: ClusterFirst
      securityContext: {}
      schedulerName: default-scheduler
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 25%
      maxSurge: 25%
  revisionHistoryLimit: 10
  progressDeadlineSeconds: 600
```
Once inside the pod you could do something like the following
```
curl -ivk https://xaqrcng.com
```


## Network Scan Pod
Using the following yaml to create a pod the can be used to scan a network with Nmap
```
kind: Deployment
apiVersion: apps/v1
metadata:
  name: suspiciouscontainer
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nmap
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: nmap
    spec:
      containers:
        - resources: {}
          terminationMessagePath: /dev/termination-log
          name: nmap
          command:
            - /bin/sh
          securityContext:
            capabilities:
              add:
                - NET_RAW
            runAsUser: 0
            allowPrivilegeEscalation: false
          imagePullPolicy: Always
          terminationMessagePolicy: File
          image: docker.io/instrumentisto/nmap
          args:
            - '-c'
            - sleep infinity
      restartPolicy: Always
      terminationGracePeriodSeconds: 30
      dnsPolicy: ClusterFirst
      securityContext: {}
      schedulerName: default-scheduler
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 25%
      maxSurge: 25%
  revisionHistoryLimit: 10
  progressDeadlineSeconds: 600
```
Access the pods terminal and use the command nmap -p 1-1024 -sN <subnet> to initate a scan
