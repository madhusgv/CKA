
URL
===
https://github.com/kodekloudhub/certified-kubernetes-administrator-course

Certification Tips
====================

1. Use kubectl run command to create pods/deployments/repicaset
2. Create an NGINX Pod
    kubectl run nginx --image=nginx
3. Generate POD Manifest YAML file (-o yaml). Don't create it(--dry-run)
    kubectl run nginx --image=nginx --dry-run=client -o yaml
4. Create a deployment
    kubectl create deployment --image=nginx nginx
5. Generate Deployment YAML file (-o yaml). Don't create it(--dry-run)
    kubectl create deployment --image=nginx nginx --dry-run=client -o yaml
6. Generate Deployment YAML file (-o yaml). Don't create it(--dry-run) with 4 Replicas (--replicas=4)
    kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml

    Save it to a file, make necessary changes to the file (for example, adding more replicas) and then create the deployment.

    kubectl create -f nginx-deployment.yaml

7. In k8s version 1.19+, we can specify the --replicas option to create a deployment with 4 replicas.
    kubectl create deployment --image=nginx nginx --replicas=4 --dry-run=client -o yaml > nginx-deployment.yaml
    
01. Introduction
=================
    a. Into kuberneres
    b.


02. Core Concepts
=================
    1. Archicture
        1. Master Node(Control Plane)
            1. API Server(kube-apiserver)
                > Authenticate user
                > vaidate request
                > retrive data 
                > update ETCD
                > Interface with scheduler
                > Interface with kubelet
                > API Server need to know the information about other kubernetes components
                > Kubeadm configures all components as pods
                > The manifest located at: /etc/kubernets/manifests/kube-apiserever.yaml
                > /etc/systemd/system/kuber-apiserver.service(Non Kubeadm)
                > ps -aux | grep kube-apiserver(Non Kubeadm)
            2. Scheduler
                1. Only responsible to deciding which pod goes to which node.
                2. Identify the reosuce requriements for a pod and select best node to place the pod.
                3. Steps to Select best node
                    > Filter node
                    > Rank Node
                4. Kube-Scheduler can be downloaded as a package and install as a service
                5. Kubeadm deploys Kube-Scheduler manger as pod
                6. The manifest located at: /etc/kubernets/manifests/kube-scheduler.yaml
                7. /etc/systemd/system/kube-scheduler.service(Non Kubeadm)
                8. ps -aux | grep kube-scheduler(Non Kubeadm)

            3. ETCD
                1. install etcd
                2. ./etcd
                3. Default port 2379
                4. etcdctl - cli to mange ETCD
                5. Any achange in cluster is updated in ETCD
                6. stores data in directory /registery
                7. etcdctl commands
                    etcdctl snapshot save 
                    etcdctl endpoint health
                    etcdctl get
                    etcdctl put
                8. Kubeadm deploys kube-etcd as pod
                9. The manifest located at: /etc/kubernets/manifests/kube-etcd.yaml
                10. /etc/systemd/system/kube-etcd.service(Non Kubeadm)
                11. ps -aux | grep kube-etcd(Non Kubeadm)                 

            4. Kubelet
                1. Master in worker node and runs on each node
                2. Regester node with Kubernetes cluster
                3. Create PODs
                4. Monitor Nodes an PODS
                5. Kubelet can be downloaded as a package and install as a service
                6. Kubeadm does not install kubelet on a node.
                7. /etc/systemd/system/kubelet.service(Non Kubeadm)
                8. ps -aux | grep kubelet(Non Kubeadm)

            5. Controller(Kube-Contoller-Manager)
                1. watch status
                2. take action
                3. Continously monitor kubernetes status and take back the state in to desired state
                4. Node Controller
                    > Checks the status of node for every 5 secs(Monitor Interval)
                    > If a node not responed for 40 secs , then make the node as down (Node Monitor Grace Prriod)
                    > Wait for 5 mins to remove the pod and move it to another node(POD Eviction Timeout)
                5. Replicaset Controller
                    > Monotor all replicaset and ensure all pods are in desired state
                6. There are other controllers persent in kubernetes 
                    > Deployment Controller
                    > Namespace Controller
                    > EndPoint Controller
                    > Job-Controller
                    > PV Protection Controller
                    > Service account controller
                    > Statefulset controller
                7. Kube-Contoller-Manager can be downloaded as a package and install as a service
                    > User can modify default configuration values
                    > provides an option to enable/disable different controllers mentioned in 4-6
                8. Kubeadm deploys kubecontroller manger as pod
                9. The manifest located at: /etc/kubernets/manifests/kube-controller-manager.yaml
                10. /etc/systemd/system/kube-controller-manager.service(Non Kubeadm)
                11.  ps -aux | grep kube-controller-manager(Non Kubeadm)


            6. Network(kube-proxy)
                1. Pod networking solution is provided by kube-proxy
                2. run on each node
                3. Looks for new services and create a rule to forward the request to the new services
                4. kube-proxy can be downloaded as a package and install as a service
                    > User can modify default configuration values
                5. Kubeadm deploys kube-proxy as pod - Deployed as demonsets
                6. The manifest located at: /etc/kubernets/manifests/kube-proxy.yaml
                7. /etc/systemd/system/kube-proxy.service(Non Kubeadm)
                8. ps -aux | grep kube-proxy(Non Kubeadm)
        2. worker Node
            1. Kubelet
            2. Network(kube-proxy)

    2. Pods
        1. Pod is a single instace of an application
        2. 1x1 mapping with containers. Means single pod contains single contianer
        3. But POD suppoters multiple containers also. 
            > ex: Application conianer, helper container in a single pod
            > shares same namespace, storage, network, etc.

    3. Manifest file 
        1. YAML for different kind
        '''
        apiVersion: v1
        kind: Pod
        metadata
            name: 
            labels:
                app: myapp
                type: front-end
        spec:
            containers:
                - name: nginx-container
                    image: nginx
        '''

    4. Practical - Kubectl
        1. kubectl get pods : Get pods
        2. kubectl run nginx --image=nginx : run pod
        3. kubectl describe pods <pod-name>
        4. kubectl get pods -o wide 
        5. kubectl run redis --image=redis123 --dry-run-client -o yaml > sample.yaml
        6. kubectl edit pod redis : To edit the running pod

    5. Replication Controller
        1. High Avaialbility
        2. Load Banancing & Scaling
        3. Relication controller & ReplicaSetde
            > Relication controller - Old
                - creates and manges replication of pod
            > ReplicaSet - New
                - creates and manges replication of pod
                - Groups replicas based on Labels
                - Montor the replicas based on Labels, if one pod goesdown on a label then replciaset starts another pod
                - MatchLabels will be used to monitor the pods
                - Scale - Scale the replicas manully using command
                    > Update the yaml file field 'replicas' to a new number
                - Autoscaling will be done by HPA
        4. Commands
            1. kubectl create -f replicaset.yaml
            2. kubectl get replicaset 
            3. kubectl delete replicaset <replocaset-name>
            4. kubectl replace -f replicaset.yaml
            5. kubectl scale --replicas=6 -f replicaset.yaml
            6. kubectl scale --replicas=6 replicaset(type) myapp-replicaset(name)

    6. Deployments
        1. Rolling updates are useully done in a normal production env
            > This can lead to issue, if any upgrade failes
        2. Deployments provides a provision to update products/instance seamlessly
        3. Commands
            1. kubectl get all

    7. Namespaces
        1. 'Default' namespace is created when k8s started
            > for default
        2. 'kube-system' namespace is created when k8s started
            > for K8s system
        3. 'kube-public' namespace is created when k8s started
            > for public 
        4. to access resouce from another name space 
            ( <podname>.<namespce>.svc.cluster.local)
            apiVersion: v1
            kind: Namespace
            metadata:
                name: dev
        5. kuebctl create namespace dev
        6. kubectl config set-context $(kubectl config curret-context) --namespace=dev
        7. ResouceQuota kind is used to limit the resource usege on a namespace
    8. Services
        1. Enables communication b/w diff components or external
        2. Enalbles loose coupling b/w micro services
        3. Service is an object in K8s.
            1. Node Port service- Service listens on port and forward he request to the pods in the node
            2. Cluster servie IP Service
            3. Load balaner Servvice
        4. Node Port Service
            1. Listens on a node port and forward the request to the port running on the pod in cluster
            2. Defaut node port can be in range of 30008-32767
                apiVerion: v1
                kind: Service
                metadata:
                    name: myapp-service
                spec:
                    type: NodePort
                    ports:
                        - targetPort: 80
                        port: 80
                        nodePort: 30008
                    selector:
                        #Label from pod
                        app: myapp
                        type: front-end
            3. Services uses random algo to redirect the req to multiple replica pods
        5. Cluster IP Service
            1. IP address of Pods are not static
            2. different type(frontend, backend, database) of pods can communicate with each other seamlessly using Service IP
                apiVerion: v1
                kind: Service
                metadata:
                    name: back-end
                spec:
                    type: ClusterIP
                    ports:
                        - targetPort: 80
                        port: 80
                        nodePort: 30008
                    selector:
                        #Label from pod
                        app: myapp
                        type: back-end        
        5. Loadbalancer Service
            1. Works with cloud native loadbalaners(ex: Google Cloud Platform)
                apiVerion: v1
                kind: Service
                metadata:
                    name: back-end
                spec:
                    type: LoadBalancer
                    ports:
                        - targetPort: 80
                        port: 80
                        nodePort: 30008
                    selector:
                        #Label from pod
                        app: myapp
                        type: back-end   
            
    9. Imparative vs declarative
        1. Imparative ( IaaS)
            > Specifies 'what' and 'how' to do 
            > Specifies steps to confog
            > Ansile , Cheff, Puppet 
            > K8s commands are imparative(run , create, edit, etc.)
            > K8s manifest files
        2. Declarative ()
            > Specifies What to do only
            > kubectl apply command is declarative
            > apply commands detects the changes in the input file and apply
    10. Kubectl apply
        1. compares the last applied configuration and input configuration to detect the changes and apply it on the system.
        2. https://kubernetes.io/docs/tasks/manage-kubernetes-objects/declarative-config/
        3. Local file, Last applied configuration, live k8s configuration        
            1. Local file - is the file contains the configuration details on the disk
            2. Last applied configuration- is the configuration detail applied last( Contained by k8s live configuatted as annontations)
            3. live k8s configuration - Live k8S configruation


03. Scheduler
=================
    1. How scheduler works?
        1. What to schedule?
        2. where to schedule?
        3. If schedulers are not running then pods will be in pending state, as it is not assiged to any node.
        
    2. Manual Scheduling 
        1. Node name can be assinged manually in pod spec
        '''
            apiVerion: v1
            kind: Pod
            metadata:
            name: myapp-pod
            labels:
                app: myapp
            spec:
                nodeName: node02
                containers:
                    - name: nginx-container
                    image: nginx
        '''

  
        2. To modify node, use Binding object to bind to a node
    3. Labels and selectors
        1. Labels used to group objects
            ''' 
                apiVerion: v1
                kind: Pod
                metadata:
                name: myapp-pod
                labels:
                    app: webserver
                    function: frontend
                spec:
                nodeName: node02
                containers:
                    - name: nginx-container
                    image: nginx
            '''
            2. selector are used to filter the pods/objects based on the lables
                kubectl get pods --selector app=webserver  (or) kubectl get pods -l app=webserver --no-headers
                kubectl get pods --selector app=webserver,db=redis
                kubectl get pods --show-lables
            3. Annotations are used to specify any other details abuot the pod/company/etc
    4. Taints and Tolerent
        1. Taints and tolerance are to restrict the pods in a node
        2. Taints
            1. No unwanted pods be placed on tainted node
            2. kubectl taint nodes node-name key=value:<taint-effect>
                <taint-effect> 
                    - NoSchedule - Pods will not be scheduled
                    - PreferNoSchedule - will try to no schedule a Pod, but not guranteed
                    - NoExecute - will not schedule any new pods and existing pods does not tolerate the taint will be evicted
                    ex: kubectl taint nodes node1 app=blue:NoSchedule
            3. NoExecute
        3. Tolerent
            1. Pods can be tolerated to be placed on a tainted node
                '''
                apiVerion: v1
                kind: Pod
                metadata:
                    name: myapp-pod
                    labels:
                        app: myapp
                spec:
                    containers:
                        - name: nginx-container
                        image: nginx
                    tolerations:
                        - key: "app"
                          operator: "Equal"
                          value: "blue"
                          effect: "NoSchedule"
                
                '''
                2. kubectl explain pod --recursive | less
            

        3. Node Affinity is aslo used to restrict pods on a node
        4. A taint is set on master node by default
            1. kubectl describe node kubemaster | grep 'Taint'
    5. Node Selctors
        1. Used to select the node based on the selector specification defined in the node lables
            '''        
            apiVerion: v1
            kind: Pod
            metadata:
                name: myapp-pod
                labels:
                    app: myapp
            spec:
                containers:
                - name: nginx-container
                    image: nginx
                nodeSelector:
                    size : Large
            '''
            2. kubectl label nodes <node-name> <label-key>=<label-value>
                kubectl label nodes node01 size=Large

    6. Node Affinity
        1. Ensure pods are hosted on a particular nodes
        2. Helps user to provide advanced complex conditions to select nodes
            '''        
            apiVerion: v1
            kind: Pod
            metadata:
                name: myapp-pod
                labels:
                    app: myapp
            spec:
                containers:
                    - name: nginx-container
                      image: nginx       

                affinity:
                    nodeAffinity:
                        requiredDuringSchedulingIgnoredDuringExecution:
                            nodeSelectorTerms:
                                - matchExpressions:
                                    - key: size
                                    operator: Exists
            '''
        3. Affinity type
            1. requiredDuringSchedulingIgnoreDuringExecution
            2. preferredDuringSchedulingIgnoreDuringExection
            3. requiredDuringSchedulingRequiredDuringExecution - Not released

    7. Taints and Tolerent vs NodeAffinity
        a. Taints and Tolerent Ensures that tainted nodes accecpts only tolerent pods, however the untained nodes also can accept the tolerent pods.This is not expected
        b. NodeAffinity ensures the expected pod to be placed on the right node, however other pods also can be placed on the labeled node.
        c. Combininig both will ensure right pods goes not right nodes

    8. Resource Requirements and Limits 
        a. Scheduler decides the where to place right pod
        b. Default resource assign by kubernets for each pod is 1 vCPU, 512Mi or default CPU request of .5 and memory of 256Mi"?
        c. CPU
            1. Spcifies the CPU of a pod
            2. Minimum vale can be 1m(Milli)
            3. 
        d. Memory
            1. Specifies the Memory of a pod
            2. can be specified as Mebi byte
            3. G - Giga bytes. 1 Giga Byte = 1000 Mega Byte, 1 Meage byte = 1000 Kilo byte, 1 Kilo bytes = 1000bytes
            4. Gi - Gibi bytes :1 Gibi byte = 1024 Mibi Byte, 1 Mebi byte = 1024 Kibi byte, 1 Kibi byte = 1024 bytes
    9. DaemonSets
        a. DaemonSets are like replicaset, but in each node one instance of pod will be runnig always.
        b. Daemonset does the following
            1. If a new nodes are added  then creates the pod
            2. If node is deleted then pod also will be deleted 
        c. Example: Logging, monitor agent
        d. kube-proxy, weave-net can be deplyed as DaemonSet
        e. definition is same as replicaset
        f. kubectl get daemonset
    
    10. Static Pods
        a. Kublet can manage a node independenly
        b. kubelet can be configured to read a pod specfication from a specific directory(ex: /ect/kubernetes/manifests)
        c. Kubelet monirots this diectory and create pods.This is called static pods
        d. If file is remvoed from the dir, then the pods will be remvoed.
        e. Only pods can be created and other kinds(Deployment, replicaset) needs Mater node
        f. kubelet.service(configpath file)/kubeconfig.yaml contains a feild "StaticPod" to specify path for static pods manifest files
        g. Static pods viewed using "docker ps" command
        h. static pods also listed in master kubectl ( can be readonly)
        i. static pods are used to create controlplace ( kubeadm tool uses static pods concepts)
        j. Static pods are deplyed with pod name with  node as suffix
    11. Multiple Schedulers
        a. Kubernetes can have multiple scheduler 
        b. CustomScheduler can be deployed in kubernetes master node
        c. Scheduler name will be different for each scheduler
        d. default-scheduler is the name for default
        e. leader-elect will ensure multiple master to select one scheduler
        f. schedulerName filed in pod spec will select the scheduler
        g. kubectl get events to vetify the scheduler events
    12. Configure kubernetes scheduler
        a. kube-scheduler.service
        b. kubescheduler.yaml


04. Logging and Monitoring
============================
    1. Monitoring Kubernetes Cluster components
        a. node metris
        b. pod metrics
        c. cpu, memeory, network, etc
    2. Monotoring 
        Kubernets does not provide inbuilt metrics collection
        a. metrics-server    
            - 1 metric server per kubernetes cluster
            - metric-server retrives the metrics from each of kubernetes nodes and pods and stores in memory
            - Store the data only on memory
            - Kubelet on each node collects the metrics from nodes and pods and shre it with metric server using k8s API
            - Kubelet uses CAdvisor to collect the pod metrics
            - minikube addons enable metrics-server
            - other env. download and configure metrics-server(kubectl create -f deploy/1.8+/)
            - kubectl top node
                - cpu/memroy consumption on each node
            - kubectl top pod
    3. Logging 
        a. kubectl logs -f <pod-name> <container-name>
            - <pod-name> displays the log message of the pod with single contianer
            - <container-name> displays the log message of the container with multiple contianer
        b. "kubectl logs <pod-name> -c" to list all containers in the pod

05. Application Lifecycle Management
====================================
    1. Updates and Rollout
        a. Deployment 
            a. When a deployment is created then a rollout is created with a version(version1), if application is updated then a another rollout os created with another version(version2)
            b. kubectl rollout status <deployment-name>
            c. kubectl rollout history <deployment-name>
        b. Deployment strategy
            a. Recreate
            b. Rolling update(default)
        c. Recreate
            a. Bringdown all the old version application and deploy the new version application
            b. This leads to application downtime
        d. Rolloing update
            a. Bringdown the application one by one and installing new version
        e. Use kubectl apply command to change the pod configuration which leads to rollout/recreate
        f. Upgrade
            a. Deployment object create a replicaset for each upgrade
        g. Rollback
            a. kubectl rollout undo <deployment>
    2. Command and arguments in pod Definitions
        a. docker CMD command
            - CMD ["sleep", "5"]
            - ENTRYPOINT ["sleep"]
            - --entrypoint
        b. Command and arguments in Kubernetes Pod
        '''
            apiVerion: v1
            kind: Pod
            metadata:
            name: myapp-pod
            labels:
                app: myapp
            spec:
            containers:
                - name: nginx-container
                  image: nginx
                  commands: ["sleep", "5000"]
        ''''
 
    3. ENV varible
        a. env can be set as plain KeyValue or using ConfigMap or using Secrets
            spec:
                containers:
                    - name: nginx-container
                    image: nginx
                    commands: ["sleep", "5000"]
                    env:
                        - name: MYENV_
                          value: /home/madhu
    4. ConfigMap
        a. COnfigMaps are used to pass the configuration data using KV to kubernetes
        b. Create configMap and inject into the Pod
        c. configMap can be created using commands/manifestfiles
            -Imparative Method
                > kubectl create configmap <configmap-name> --from-literal=<key>=<value> --from-literal=<key1>=<value1>
                > kubectl create configmap <configmap-name> --from-file=<path-to-file>
            - Declarative Method
                > kubectl apply -f configmap.yaml
                    '''
                    apiVerion: v1
                    kind: configMap
                    metadata:
                    name: app-config
                    data:
                        APP_COLOR: blue
                        APP_MODE: prod
        d. commands
            - kubectl get configmap
            - kubectl describe configmap
            - kubectl explain pods --recursive | grep envFrom -A3
        e. Inject configMap to a pod
            - Inject as single env in pod spec( env:)
            - Inject as configMap in pof spec
            - Inject from volumes
            '''
            apiVerion: v1
            kind: Pod
            metadata:
                name: myapp-pod
                labels:
                    app: myapp
            spec:
                containers:
                    - name: nginx-container
                      image: nginx
                      commands: ["sleep", "5000"]
                      envFrom: 
                        - configMapRef:
                            name: app-config
            ''''
    5. Secrets
        a. Used to store the secret values like password, etc.
        b. Create Secret, Inject secret into a pod
        c. Create Secret
            - Imperative method
                > kubectl create secret generic <secre-name>  --from-literal=<key>=<value>
                >  kubectl create secret generic <secre-name>  --from-file=<path-to-file>
            - Declarative method

                > kubectl apply -f secret.yaml
                    '''
                    apiVerion: v1
                    kind: Secret
                    metadata:
                    name: app-config
                    data:
                        db_host: mysql
                        db_user: root
                        db_password: password
                    '''
        d. specify the secsitive data in encoded format
            echo -n 'mysql' | base64
        e. decode the value using
            echo -n 'xynde0=' | base64 --decode
        e. commands
            - kubectl get secrets
            - kubectl describe secrets
        f. Inject secret to pod
            - Inject as single env in pod spec( env:)
            - Inject as configMap in pof spec
            - Inject from volumes
            '''
            apiVerion: v1
            kind: Pod
            metadata:
                name: myapp-pod
                labels:
                    app: myapp
            spec:
                containers:
                    - name: nginx-container
                      image: nginx
                      commands: ["sleep", "5000"]
                      envFrom: 
                        - secretRef:
                            name: app-secret
            ''''

    6. Multi-Container Pods
        a. Need for running dependent application run todather
            - webserver and logging
            '''
            apiVerion: v1
            kind: Pod
            metadata:
                name: myapp-pod
                labels:
                    app: myapp
            spec:
                containers:
                    - name: web-app
                      image: nginx
                    - name: logging
                      image: log-agent
            ''''
        b. Design patterns
            - The Side car pattern
            - The adapter pattern
            - The ambassador pattern
    7. InitCOntainers
        a. initContianer specifed in pod spec will be started first and then start the remaing containers
    8. Self Healing containers
        a. Kubernetes supports self-healing applications through ReplicaSets and Replication Controllers
        b. Kubernetes provides additional support to check the health of applications running within PODs and take necessary actions through Liveness and Readiness Probes.

06. Cluster Maintenance
========================
    1. OS Upgrades
        a. If a node goes down and comesback with in 5 mins(pod eviction timeout) then Kubelet restarts the pod and make it avialable, else kubernetes terminates the pod
        b. In a planned maintenance, the following steps should be followedku
            1. drain a node, so that all the pods runnig on that node will be moved to another node. 
                > kubectl drain <node-name>
            2. Once the drain is done the node will be maked as "cordon", so that this node will not be consided for scheduling
                > kubectl cordon <node-name>
            3. Once the node comesup then  the node should be uncordon( no auto rebalancing)
                > kubectl uncordon <node-name>
    2. Kubernetes Software versions
        a. Kubernetes version contains following
            - Major.Minor.Path( ex: v1.11.3)
            - July 2015 - First Major version was released
            - ETCD, Core dns will have different version as they are different, the remaing kubernetes components will be same version
            - 
    3. Cluster Upgrade Process
        a. controlplane Components can be of different versions
            - api-server - should be the higher version and remaining componets can be lower than api-server
            - controller, scheduler can be 1 version lower
            - kube-proxy , kubelet can be 2 version lower
            - kubectl can be 1 version higher/ 1 version lower
            - Kubernetes supports only 3 minor versions
            - Upgrade cluster with 1 minor version at a time
            - All Kubernetes could  providers have their tool to upgrade 
            - Kubeadm tool can be used to upgrade (Master)
                > kubeadm upgrade plan
                > kubeadm upgrade apply
                > apt-get upgrade -y kubelet=1.12.0-00
                > systemctl restart kubelet
            - Kubeadm tool can be used to upgrade (Worker)
                > run "kubectl drain <worker-node>" on master
                > apt-get -y kubeadm=1.12.0-00
                > apt-get -y kubelet=1.12.0-00
                > kubeadm upgrade node config --kubelet-version v1.12.0
                > systemctl restart kubelet
                > run "kubectl uncordon <worker-node>" on master
            - Hardway
    4. Backup and Restore
        a. Backup canditates
            - Resource configuration
                > User declarative methods
                > Store the manifests files in github
                > Query the kube-apiserver and get the configuration and store
                > kubectl get all --all-namespaces -o yaml
                > Velero/ARK tools does the above
            - ETCD Cluster
                > --data-dir container the location of the data
                > etcdctl_api=3 etcdctl snapshot save snapshot.db
                > etcdctl_api=3 etcdctl snapshot status
                > service kube-apiserver stop
                > etcdctl_api=3 etcdctl restore snapshot.db --data-dir=/var/lib/data-from-etcdbackup
                > modify --data-dir to --data-dir=/var/lib/data-from-etcdbackup
                > systemctl daemon-reload
                > service etcd restart
                > service kube-apiserver start
                > providing endponits, certificates
                > if cluster is configured by kubeadm then chck if kubeadm provides a way to take the backup
            - commands:
                > export ETCDCTL_API=3
                > etcdctl --cacert="/etc/kubernetes/pki/etcd/ca.crt" --cert="/etc/kubernetes/pki/etcd/server.crt" --key="/etc/kubernetes/pki/etcd/server.key" snapshot save /opt/snapshot-pre-boot.db
                > etcdctl snapshot status /opt/snapshot-pre-boot.db
                > etcdctl snapshot restore /opt/snapshot-pre-boot.db --data-dir=/var/lib/etcd-from-backup
            - Persistance storage

07. Security
==============
    1. Kubernetes Security Primitives
        - Secure Hosts
            > Password based authentication disabled
            > Ssh based authentication enabled  
        - kube-apiserver
            > First level of Authentication
            > who can access ?
            > what they can do?
        - Authentication
            > who can access ? 
            > Defined by Authentication mechanism(userid,certificates)
        - Authorization 
            > what they can do?
            > RBAC Authorization 
            > ABAC, etc
        - All the kube components are secured using TLS
    2. Authentication
        - Types of users
            > Admin
            > Developer
            > End User
            > Third party users
        - Accounts
            > can be created and used service accounts
        - All users authentications is done through kube-apiserver
            > password
                - use the csv files with following columns, password, user,  user ID and group id
                - --base-auth-file=myfile.csv

            > token
                - --token-authfile=token.csv
            > certificates 
    3. TLS Certificates
        - Symmertic Encryption(Single key)
        - ASymmertic Encryption(key(private key) and a lock(public key))
        - ssh-keygen
            > id_rsa
            > id_rsa.pub
        - openssl genrsa -out <mykey.key> 1024
        - openssl rsa -in <mykey.key> -pubout > mykey.pem
        - example to read cert - openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout
    4. TLS in Kubernetes
        - Client Certificates
            > Scheduler to server(kube-apiserver)
            > Controller to server(kube-apiserver)
            > kube-proxy to server(kube-apiserver)
            > kube-apiserver to server(etcd)
            > kube-apiserver to server(kubelet)
            > kubelet to server(kubelet)
        - Server Certificates
            > etcd
            > kube-apiserver
            > kubelet
        - Certificate Authority
            > etcd CA
            > Kubernetes CA
        - Certificate Creation
            1. Kubernetes CA(Certificate Authority)
                > Generate key
                    - openssl genrsa -out ca.key 1024
                > Certificate Signing Request
                    - openssl req -new -key ca.key -sub "/CN=KUBERNETES=CA" -out ca.csr
                > Singn the Certificates
                    - openssl x509 -req -in ca.csr -signkey ca.key -out ca.crt
            2. Client Certificate(Admin user)
                > Generate key
                    - openssl genrsa -out admin.key 1024
                > Certificate Signing Request
                    - openssl req -new -key admin.key -sub "/CN=kube-admin" -out admin.csr
                > Singn the Certificates
                    - openssl x509 -req -in admin.csr -CA ca.crt -CAKey ca.key -out admin.crt
                > Adding group information with OU param makes the user as admin with previliges
            3. Create the client certificates for Kube-Scheduler, Kube-Controller, kube-Proxy
            4. Server Certificate(etcd)
                > create the etcd certicificate as same as client certificates
                > we must generate additional peer certificates
            5. kube-apiserver
                > All the names of kube-apiserver shuold be present in the certificates
                > create openssl config file to provide alternate name of the apiserver
                > apiserver needs following 
                    - ca certificates
                    - tls server certificate
                    - etcd client certificate
                    - kubelet client certificate
            6. kubelet 
                > create server certificates
                > it will be named with node name(kubelet-node01)
        - View Certificates
            1. Need to know how the cluster is deployed
                > hardway ( creatre certificates manually)
                > kubeadm way
            2. kubeadm way
                > check the location of yaml file for each components and in the command section all the certificate information is filled.
                > decode the certificate
                    openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout 
            3. inspect the logs 
                > use the system service log(journalctl -u etd.service -l)
                > kubectl logs etcd-master
                > docker ps -a
                > docker logs <container-id>
    5. Kubernetes Certificates APIs
        - CertificateSigninigRequest
            > User creates a key and send it to admin for generating the Certificates
                - openssl genrsa -out jane.key 2048
            > Admin Create Certificates for the key shared by user
                - openssl req -new-key jane.key -subj "/CN=jane" -out jane.csr
            > Use this certificate create get it signed by CA
                cat jane.csr | base64
                '''
                apiVersion: certificates.k8s.io/v1
                kind: CertificateSigningRequest
                metadata:
                    name: jane
                spec:
                    groups:
                    - system:authenticated
                    usages:
                    - digital signature
                    - key enciperment
                    - server auth
                    request:
                        <base64 output of certificate>
                    Issuername: ""
                    
                ''''
            > Commands
                - kubectl get csr -o yaml
                - kubectl certificate approve <csr-name>
            > Decode the certiface got from kubectl get command 
                echo "certifacte from kubectl get" | base64 --decode
            > All the certifacte related tasks are done by kube-controller
    6. KubeConfig
        - kubectl authetication details are stonred in a file.This file called as kubeconfig file
        - Kubeconfig file is stored in $HOME/.kube/config
        '''
        kubeconfig file
        --server mykube:6443(cluster)
        --client-key admin.key
        --client-certificate admin.crt
        --certificate-authority ca.crt
        '''
        - Cluster
            > Development
            > Production
        - Users
            > Admin 
            > dev user
        - Context
            > Admin@production
        - YAML
            '''
            apiVersion: v1
            kind: Config
            current-context: my-kube-admin@my-kube-playground
            clusters:
                - name: my-kube-playground
                  cluster:
                    certificate-authority: ca.crt
                    server: http://my-kube:6443
            contexts:
                - name: my-kube-admin@my-kube-playground
                  context: 
                    cluster: my-kube-playground
                    user: my-kube-admin
                    namespace: default
            users:
                - name: my-kube-admin
                  user:
                    client-certificate: admin.crt
                    client-key: admin.key
            '''
        - Commands
            > kubectl config view
            > kubectl config view --kubeconfig=<my-custom-config>
            > kubectl config use-context <prod-user@production>
            > kubectl config -h
    7. API Groups
        - Check the version of API Server and get pods
            > curl http://kube-master:6443/version
            > curl http://kube-master:6443/api/v1/pods
        - Different endpoints
            - /metrics
            - /healthz
            - /version
            - /api
            - /apis
            - /logs
        - Core group - /api
            - /v1
                > /pods
                > /namespace
                > /events
                > /PV
                > /PVC
                > /rc and more
        - Named Group - /apis
            - More organized groups
            - All new APIs are added into this group
            - /apps
                > /v1/deployments
                > /v1/replicasets
                > /v1/statefulsets
            - /authentication.k8s.io
        - kubectl proxy
            - Enable proxy service to access the kubeapi-server
    8. Authorization
        - Authorization defines a access to an object in kubernetes
        - Different Mechanishm of Authorization
            > Node
            > Attribute based Authorization
            > Role based Authorization
            > webhook
        - Node Authorizer
            > Kubelet requests are authorzied by Node Authorizer
        - Attribute based Authorization
            > Group of user with set of permissions
                - view Pod
                - Create Pod
                - Delete Pod
            > Use 'Kind: Ploicy' to define the ABAC
        - RBAC
            > Create a all Role for all set of users
            > Reduces the manual work 
            > Standard approch
        - webhook
            > External Authorization
        - AlwaysAllow, AlwaysDeny
        - These modes are set in  --authorization-mode=Node, RBAC, Webhook
    9. RBAC
        - k8s provides RBAC authorization as an object
        - Create Role
            '''
            apiVersion: rbac.authorization.k8s.io/v1
            kind: Role
            metadata:
                name: developer
            rules:
                - apiGroups: [""]
                  resources: ["pods"]
                  verbs: ["list", "get", "create", "update", "delete"]

            '''
        - Link the user to the Role
            Kind: RoleBinding
            subjects: 
            - Kind: User
        - Commands:
            > Kubectl get roles
            > kubeclt get rolebindings
            > kubectl describe role <role-name>
            > kubectl describe rolebinding <role-binding-name>
            > kubectl auth can-i create deployments
            > kubectl auth can-i delete nodes
            > kubectl auth can-i delete nodes --as <user-name>
            > kubectl auth can-i create pods --as <user-name> --namespace <test>
        - Resource Names
            '''
            apiVersion: rbac.authorization.k8s.io/v1
            kind: Role
            metadata:
                name: developer
            rules:
                - apiGroups: [""]
                  resources: ["pods"]
                  verbs: ["list", "get", "create", "update", "delete"]
                  resourceNames: ["blue-pod", "green-pod"]
            '''
        - Sample YAML to create role and rolebindings
            ---
            kind: Role
            apiVersion: rbac.authorization.k8s.io/v1
            metadata:
            namespace: blue
            name: deploy-role
            rules:
            - apiGroups: ["apps", "extensions"]
            resources: ["deployments"]
            verbs: ["create"]

            ---
            kind: RoleBinding
            apiVersion: rbac.authorization.k8s.io/v1
            metadata:
                name: dev-user-deploy-binding
                namespace: blue
                subjects:
                - kind: User
                  name: dev-user
                  apiGroup: rbac.authorization.k8s.io
                roleRef:
                    kind: Role
                    name: deploy-role
                    apiGroup: rbac.authorization.k8s.io

            ---
    10. Cluser Roles
        - Cluster Scoped are used to group nodes, PV, clusterroles, clusterrolebindings, CSR, namespace
        - kubectl api-resources namespaced=true
        - Clusterroles can be used to  create role to mange node realted opetations
            ---
                apiVersion: rbac.authorization.k8s.io/v1
                kind: ClusterRole
                metadata:
                namespace: blue
                name: cluster-admin
                rules:
                - apiGroups: [""]
                resources: ["nodes"]
                verbs: ["create"]
            ---
        - Clusterrolebindings can be used to  bind with the role to mange node realted opetations
            ---
                apiVersion: rbac.authorization.k8s.io/v1
                kind: ClusterRoleBinding
                metadata:
                    name:  cluster-admin-role-binding
                Subjects:
                    - Kind: User
                      name: cluster-admin
                      apiGroup: rbac.authorization.k8s.io
                roleRef:
                    - kind: CluserRole
                      name: cluster-administrator
                      apiGroup: rbac.authorization.k8s.io
            ---        
        - Sample YAML to create cluser role and binding for a user
            ---
            kind: ClusterRole
            apiVersion: rbac.authorization.k8s.io/v1
            metadata:
            name: node-admin
            rules:
            - apiGroups: [""]
            resources: ["nodes"]
            verbs: ["get", "watch", "list", "create", "delete"]

            ---
            kind: ClusterRoleBinding
            apiVersion: rbac.authorization.k8s.io/v1
            metadata:
            name: michelle-binding
            subjects:
            - kind: User
            name: michelle
            apiGroup: rbac.authorization.k8s.io
            roleRef:
            kind: ClusterRole
            name: node-admin
            apiGroup: rbac.authorization.k8s.io
        
            ---
            kind: ClusterRole
            apiVersion: rbac.authorization.k8s.io/v1
            metadata:
            name: storage-admin
            rules:
            - apiGroups: [""]
            resources: ["persistentvolumes"]
            verbs: ["get", "watch", "list", "create", "delete"]
            - apiGroups: ["storage.k8s.io"]
            resources: ["storageclasses"]
            verbs: ["get", "watch", "list", "create", "delete"]

            ---
            kind: ClusterRoleBinding
            apiVersion: rbac.authorization.k8s.io/v1
            metadata:
            name: michelle-storage-admin
            subjects:
            - kind: User
            name: michelle
            apiGroup: rbac.authorization.k8s.io
            roleRef:
            kind: ClusterRole
            name: storage-admin
            apiGroup: rbac.authorization.k8s.io
    11. Service Account
        - User Account
        - Service Account
            > Used by application to communicate b/w applications.
            > To Create : kubectl create serviceaccount <name>
            > To get : kubectl get serviceaccount <name>
            > kubectl describe serviceaccount <name>
            > It creates a token automatically. This token is stored as secret object
            > To view the secret object:  kubectl describe secret <name>

    12. Image Security
        - image: nginx - This is expansed as below
            > docker.io/nginx/nginx ( URL(Registry)/user(acoount)/imagename(repo))
            > Registry
                - docker.io(dcoker)
                - gcr.io (google)
            >  Private registry
                > docker login <private-registry.io>
                > docker run private-registry.io/apps/my-app
            > To login with private registory , create a secret 
                > example for docker:
                    kubectl create secret docker-registry regcred --docker-server=\
                            --dokcer-username=\
                            --dokcer-password=\
                            --dokcer-email=\
            > 'imagePullSecrets' section in the contaer spec, gets the secret name to login with the registry
    13. Security Contexts
        - Secutiry settings can be configured at pod level/ container level
        - 'SecurityContext' in pod spec metadata
        - Sample YAML
        ---
            apiVersion: v1
            kind: Pod
            metadata:
            name: ubuntu-sleeper
            namespace: default
            spec:
            securityContext:
                runAsUser: 1010
            containers:
            - command:
                - sleep
                - "4800"
                image: ubuntu
                name: ubuntu-sleeper

            apiVersion: v1
            kind: Pod
            metadata:
            name: ubuntu-sleeper
            namespace: default
            spec:
            containers:
            - command:
                - sleep
                - "4800"
                image: ubuntu
                name: ubuntu-sleeper
                securityContext:
                capabilities:
                    add: ["SYS_TIME"]
    14. Network Policy
        - Application
            > Database POD        
            > API POD
            > Webserver POD

        - Ingress
            > Accecpt traffic on a specifc port(policy to restrict incoming traffic)
        - Egress
            > Allow traffic on a specific port(Policy to allow outgoing traffic)
        - Network Security
            > By Default "All Allow" rule is applied for all pods to communicate with each other.
        - Network Policy is a kubecrnetes object
            > Policy can be : Only allow ingress trafic from API
            > Label the pod and seclect the network ploicy using selector
            > Network policies are suppprted by following network componets only
                - kube-router
                - Calico
                - Romana
                - Weave-net
            > Following n/w compnents does not support n/w policy
                - Flannel
            > Sample YAML 
                - To get reqeust from API pod from prod namespce and 192.185.3.2/32 - Used Ingress Rule
                - To send data to back up server - 192.185.3.2/80 - Used Egress Rule
            ---
            apiVersion: networking.k8s.io/v1
            kind: NetworkPolicy
            metadata:  
                name: db-policy
            spec:
                podSelector:
                    matchLabels:
                        role: db
                policyTypes:
                    - Ingress
                    - Egress
                ingress:
                    - from:
                        - podSelector:
                            - matchLabels:
                                name: api-pod
                          namespaceSelector:
                            - matchLabels:
                                name: prod
                        - ipBlock:
                            cidr: 192.185.3.2/32
                        ports: 
                            - protocol: TCP
                              port: 3306
                egress:
                    - to:
                        - ipBlock:
                            cidr: 192.185.3.2/32
                        ports: 
                            - protocol: TCP
                              port: 80 

                ---
                apiVersion: networking.k8s.io/v1
                kind: NetworkPolicy
                metadata:
                name: internal-policy
                namespace: default
                spec:
                podSelector:
                    matchLabels:
                    name: internal
                policyTypes:
                - Egress
                - Ingress
                ingress:
                    - {}
                egress:
                - to:
                    - podSelector:
                        matchLabels:
                        name: mysql
                    ports:
                    - protocol: TCP
                    port: 3306

                - to:
                    - podSelector:
                        matchLabels:
                        name: payroll
                    ports:
                    - protocol: TCP
                    port: 8080

                - ports:
                    - port: 53
                    protocol: UDP
                    - port: 53
                    protocol: TCP 

08. Storage
============
    1. Storage in Docker
        - /var/lib/docker
            > aufs
            > containers
            > images(ubultu:18.0.0)
            > volumes
        - Layered Architecture
            > In dockerfile each command creates layer on top of current container
            > for ex: from ubuntu; run api-install python;run pip install flask;copy . /opt/source;ENTRY_POINT 
                > This creates layer on top of each
                > This is called Image Layer(readonly)
            > Container Later
                > This is writable layer where container files are stored
                > Available only when containers are running
            > COPY-ON-WRITE
                > modilfed files are taken from container space
            
    2. Docker Volume  
        - docker volume create data_volumes
        - docker run -v data_volume:/var/lib/mysql mysql(volume mount)
        - docker run -v /data/mysql:/var/lib/mysql mysql(bind mount)
        - New command: docker run --mount type=bind,source=/data/mysql,target=/var/lib/mysql sql
        
    3. Storage drivers
        > Docker uses Storage drivers to create layered architecture
            > AUFS
            > ZFS
            > DTRFS
            > Device Mapper
            > overlay
            > overlay2
        > 
    4. Volume Driver Plugins
        > Local
        > Azure file storage
        > Convoy
        > Dgitial Ocean block storage
        > etc.
        > docker run -it --name mysql --volume-driver rexray/ebs --mount src=ebs-vol,target=/var/lib/mysql mysql
    5. Container Storage Interface
        > Container Runtime Interface (CRI )
        > Container Network Interface(CNI)
        > Conatianer Storage Interface(CSI)
            - Amazon EBS
            - portworx
            - DELL EMC
            - HPE
            - Hitachi
        > Kubernetes will call the interface gRPC method and implementor library shuold have the code the perform the actions
            - CreateVolume
            - DeleteVolume
            - ControllerPublishVolume
    6. Kubernetes Volumes
        -   apiVerion: v1
            kind: Pod
            metadata:
            name: myapp-pod
            labels:
                app: myapp
            spec:
            containers:
                - name: nginx-container
                  image: nginx
                  volumeMounts:
                    - mountPath: /opt
                      name: data-volume
                volume:
                    - name: data-volume
                      hostPath:
                        path: /data
                        type: Directory
                      awsElecticBlockStore:
                        volumeID: <volume-id>
                        fsType: ext4
    7. Persistant Volumes
        > sample YAML
        apiVersion: v1
        kind: PersistantVolume
        metadata:
            name: pv-v1
        spec:
            accessModes:
                - ReadWriteOnce
            capacity:
                storage: 1Gi
            hostPath:
                path: /tmp/data
        > Commands
            - kubectl get persistentvolume 
    8. Persistent Volume Claim
        > Claims make the volume avaiable to the cluster
        > Each PV bind with PVC
        > PVC automatically find the PV already created and bind with it.
        > Sample YAML
        apiVersion: v1
        kind: PersistantVolumeClaim
        metadata:
            name: pv-v1
        spec:
            accessModes:
                - ReadWriteOnce
            resources:
                requests:   
                    storage: 500Mi
        > Commands
            - kubectl get persistentvolumeclaim
        > Sample YAML with PVC and POD
            apiVersion: v1
            kind: Pod
            metadata:
            name: mypod
            spec:
            containers:
                - name: myfrontend
                image: nginx
                volumeMounts:
                - mountPath: "/var/www/html"
                    name: mypd
            volumes:
                - name: mypd
                persistentVolumeClaim:
                    claimName: myclaim
    9. Storage Class
        > PV Needs a manual creation of disk. This is called static provisonig volumes
        > With Storage class, we can provide the provisioner to craete dynamic volumes.This is called dynamic provision.
        > Sample YAML (For GCE)
            apiVersion: storage.k8s.io/v1
            kind: StorageClass
            metadata:
                name: google-class
            provisoner: kubernetes.io/gcd-pd
        > PVC needs storage class. and does not need PV. PV is automatocally created by StorageClass
        
09. Networking
===============
    1. Switching Routing
        > Switching
            - Switch Connects 2 computers using interface in each computer
            - ip link - lists the Interface (eth0)
            - ip addr to assign the ip address 
            - ip addr add <ip> dev eth0( /etc/network/interfaces)
        > Routing
            - Connect two network
            - route command to displays the routing table
            - ip route
            - ip route add  <router-ip> via <gateway-ip> command 
            - Gatewat acts as door for router
            - /proc/sys/net/ipv4/ip_forward 
            - /etc/sys
        > Default Gateway
    2. DNS
        > Name Resoulution
            - Name the remote system in /etc/host
        > Machine continas all remotesystem name is called Domain Name Server(/etc/resolv.conf)
        > www.google.com
            - Root : '.'
            - Top Level Domain  : '.com'
            - SubDomain : www drive maps apps
        > Record types
            A         webserver      192.168.1.1
            AAAA      web-server     <ip-v6>
            CNAME     food.web-server eat.web-server, hungry.web-server
        > nslookup to look for a host in DNS
        > dig 
    3. Core DNS
        > There are many DNS server solutions out there, in this lecture we will focus on a particular one – CoreDNS
        > Run the executable to start a DNS server. It by default listens on port 53
    4. Network NameSpaces
        > ROuting Table
        > ARP table
        > Everycontians has its own namespaces
        > ip netns exec <name-space > <ip link > command to create namespace and assign ip
        > ip -n red link
        > arp command
        > ip link add veth-red type veth peer name veth-blue
        > ip link set veth-red netns red
        > ip link set veth-blue netns blue
        > ip -n red link set veth-red up
        > ip -n blue link set veth-blue up
        > Needs virtual switch ( Linux Bridge)
        > NAT
        > While testing the Network Namespaces, if you come across issues where you can't ping one namespace from the other,
         make sure you set the NETMASK while setting IP Address. ie: 192.168.1.10/24
         ip -n red addr add 192.168.1.10/24 dev veth-red
        > Another thing to check is FirewallD/IP Table rules. Either add rules to IP Tables to allow traffic from one namespace to another. Or disable IP Tables all together (Only in a learning environment).
    5. Docker Networking
        > None Network(docker run --network none nginx)
        > Host Network (docker run --network host nginx)
        > Brigde Network
            - docker network ls
            - ip link
        > Docker automatically create namespace (Name starts with b3165 )
    6. CNI (Container Netwkrin Interface)
        > Common interface to implement networking operations
        > Plugins implements CRI provies all featuers
        >  weave, calico, flannel, etc
        > DOcker doest not implment CNI. it implements CNM
        > K8s create Dcoker contrainr with None Network and assin netwrk later using plugins
    7. Cluster Netwokring
        > Master 
            - 6443 for Api server
            - 10250 for kubelt
            - 10251 for scheduler
            - 10251 for controller
    8. Commands
        ifconfig -a : show the network interfaces
        ifconfig <interface-name>
        ip link eth0
        arp node01 - show ip and mac 
        ip route show default - Show the router/gateway info
        netstat -natulp | grep kube-scheduler
        netstat -anp | grep etcd
        ip r
    9. POD Networking
        - Requirement
            > Every piod should have own uniqe IP Address
            > Every piod should be able to communicate
        - CNI
            > Add Section
                - Create veth pair 
                - Attach veth pair
                - Assisgn IP Address
                - Bring Up Interface
                ip -n <namespace> link set ---
            > Delete section
                - Delete veth pair
                ip link del ---
            > kubelet --cni-conf-dir=/etc/cni/net.d --cni-bin-dir=/etc/cni/bin
    10. CNI in Kubernetes
        - CNI plugin is configued in kubelet service
            kubelet --cni-conf-dir=/etc/cni/net.d --cni-bin-dir=/etc/cni/bin
            ps -aux | grep kubelet
            ls /opt/cni/bin - 
            ls /etc/cni/net.d - bridge config file
            /opt/cni/bin
            ifconfig - show the network interfaces
            ifconfig <interface-name>
            ip link eth0
            arp node01 - show ip and mac 
            ip route show default - Show the router/gateway info
            netstat -anp | grep etcd


    11. CNI WeaveWorks
        - Weaveworks plugin agent is deployed in each node to communicate with each other and serve the request from other node
        - Each agent create a brigde network on a node and name it as "weave"
        - Single pod may connected with multiple bridge(ex: docker bridge, weave bridge)
        - Deploy Weave
            - Deploy as pods or deamonSet
                kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
                kubectl logs weave-net-5gcmb weave -n kube-system
    12. IP Address Management
        - Assigning IP address to a container and pod is resposible of CNI
        - CNI comes with 
            - host-local plugin
            - DHCP Plugin
        - weave assigns ip range of 10.32.0.0/12
    13. Service Networking
        - Service is to expose a pod on a cluser
        - Service created for cluser , but pod is created for Node
        - ClusterIP service works on cluster only
        - NodePort - Can access outside of cluster
        - Services are created and exposed via kube-proxy  
        - Services are assiged with pre-defined ipaddresses( kubeapiserver options defines this rage --service-clsuter-ip-range ipNet)
            - kube-proxy forwared the requerst comes to a serice to a appropriate pod
        - kube-proxy
            > proxy-mode
                - userspace
                - ipdtables
                - ipvs
    14. DNS
        - K8s deploys k8s DNS servers by default
        - All Services are registered to DNS Server
        - If all pods/services are in same namespcae then the serice can  be accessed using service name itself(web-service)
        - If different namespace then append namespace name(apps) in with the service name(web-service.apps)
    15. Core DNS
        - Core DNS runs as POD
        - /etc/coredns/corefile
        - /etc/resolv.conf
        - k get configmap -n kube-system
        - host web-service
    16. Ingress
        - Difference b/w Services and Ingress
        - Ingress sits on top of service , acts as a load balance for rout thr trffic b/w different services of same webapp
            > Ingress exposes the applications externaly
            > Provides SSL connections
            > Ingress can be configred as a k8s components
        - Ingress Configurestion
            > Ingress controller
                - Does not comes by default with k8s
                - Deploy GCE, HAPROXY, Nginx
                - Deply Nginx as a deployment(nginx-ingress-controller)
                - Need a service 
                - COnfigMap object shuold be created
                - Must pass 2 ENV
                    > POD_NAME
                    > POD_NAMESPACE
                - Needs Service accoint with role and rolebinding
            > Ingress Resources
                - Set of rules and configurations
                - apiversion: extensions/v1beta1
                  kind: Ingress
                  metadata:
                    name: <myname> 
                  spec:
                    rules
                        - host: abc.com
                          http:
                            - paths:
                                backend:
                                    serviceName:<>
                                    servicePort: <>
        - Ingress - Annotations and rewrite-target
            Different ingress controllers have different options that can be used to customise the way it works. NGINX Ingress controller has many options that can be seen here. I would like to explain one such option that we will use in our labs. The Rewrite target option.



            Our watch app displays the video streaming webpage at http://<watch-service>:<port>/

            Our wear app displays the apparel webpage at http://<wear-service>:<port>/

            We must configure Ingress to achieve the below. When user visits the URL on the left, his request should be forwarded internally to the URL on the right. Note that the /watch and /wear URL path are what we configure on the ingress controller so we can forwarded users to the appropriate application in the backend. The applications don't have this URL/Path configured on them:

            http://<ingress-service>:<ingress-port>/watch --> http://<watch-service>:<port>/

            http://<ingress-service>:<ingress-port>/wear --> http://<wear-service>:<port>/



            Without the rewrite-target option, this is what would happen:

            http://<ingress-service>:<ingress-port>/watch --> http://<watch-service>:<port>/watch

            http://<ingress-service>:<ingress-port>/wear --> http://<wear-service>:<port>/wear



            Notice watch and wear at the end of the target URLs. The target applications are not configured with /watch or /wear paths. They are different applications built specifically for their purpose, so they don't expect /watch or /wear in the URLs. And as such the requests would fail and throw a 404 not found error.



            To fix that we want to "ReWrite" the URL when the request is passed on to the watch or wear applications. We don't want to pass in the same path that user typed in. So we specify the rewrite-target option. This rewrites the URL by replacing whatever is under rules->http->paths->path which happens to be /pay in this case with the value in rewrite-target. This works just like a search and replace function.

            For example: replace(path, rewrite-target)
            In our case: replace("/path","/")



            apiVersion: extensions/v1beta1
            kind: Ingress
            metadata:
            name: test-ingress
            namespace: critical-space
            annotations:
                nginx.ingress.kubernetes.io/rewrite-target: /
            spec:
            rules:
            - http:
                paths:
                - path: /pay
                    backend:
                    serviceName: pay-service
                    servicePort: 8282


            In another example given here, this could also be:

            replace("/something(/|$)(.*)", "/$2")

            apiVersion: extensions/v1beta1
            kind: Ingress
            metadata:
            annotations:
                nginx.ingress.kubernetes.io/rewrite-target: /$2
            name: rewrite
            namespace: default
            spec:
            rules:
            - host: rewrite.bar.com
                http:
                paths:
                - backend:
                    serviceName: http-svc
                    servicePort: 80
                    path: /something(/|$)(.*)

         - create service:
            kubectl -n ingress-space expose deployment  ingress-controller --name ingress --port 80 --taeget-port 80 --type NodePort
    

10. Design And Install Kubernetes Cluster
==========================================
    1. Design K8s Cluster
        - Education
            - MiniKube
            - Single Node cluser
        - Development and LeaTesting
            - Multi Node Cluster with Single Master and Multiple workers
            - Setup Using Kubeadm tool or quick provision on GCP , AWS or AKS
        - Hosting Production
            - Highly avualble with multiple master and multiple worker nodes
            - Kubeadm, 
            - Upto 5000 Nodes
            - Upto 150000 Pods
            - Upto 300000 total Container
            - Upto 100 Pods Per node
        - Cloud or Onprem
            - Use Kubeadm for on-Perm
            - GKE for GCP 
            - Kops for AWS
            - Azure Kubernetes Services(AKS) for Azure
        - Storage
            - 
        - Node
            - Physical or virtual MAchine
            - 64 bit linux machine
    2. Choosing Kubernetes Infrastructure
        - MiniKube 
            - Deploys Vms automatically
        - Kubeadm
            - VMs should be ready
            - Single or multinode cluster
        - Trunkey Solutions
            - You provision VMs
            - You Configure Vms
            - You Maintain VMs by yourselfs
            - OpenShift 
            - Cloud Foundry Container Runtime
            - VMware Cloud PKS
            - Vagrant
        - Hosted Solutions
            - Kubernetes As a service
            - Provider provision VMs
            - Provider installs Kubernetes and Maintains Vms
            - GKE
            - OpenShit Online
            - Azure (AKS)
            - EKS(Amazon)
    3. HA Kubernetes Cluster
        - Multiple Master(two) Node/Control Plane
        - API Server(ACtive-Active) on both the masters are active at any point in time
            - Use Load Balancer
        - Controller Manger/Scheduler(Active-Passive)
            - Use Leader Elect option
            - kube-controller-manager --leader-elect true
        - ETCD
            - Stacked Topology- Part of Contol plane Node
            - External ETCD Topology
    4. ETCD HA
        - ETCD Distributed Key-Value Store, Simple , fast and reliable
        - Leader-follower
        - Write is done by Leader only
        - Leader send the write update to follower 
        - Follower forward to the writeLeader
        - RAFT Protocol is used to elect
        - Quorum = N/2+1
        - Odd number of master is recommeded
    5. Kubernetes Hardway
        > https://www.youtube.com/watch?v=uUupRagM7m0&list=PL2We04F3Y_41jYdadX55fdJplDvgNGENo
        > https://github.com/mmumshad/kubernetes-the-hard-way

11. Introduction to Kubeadm
===========================
    1. Steps
        - Provision Multiple VMs
        - Container runtime
        - Install Kubeadm tool
        - Initialize Master server
        - ENsure Network conectivity is up on all worker node
        - Join Worker Node
    2. Docs
        - https://github.com/kodekloudhub/certified-kubernetes-administrator-course
        - https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
    
    3. Deploy with kubeadm - Provision VMs with Vagrant

        - 
    4. Commands
        - kubelet --version    
        - kubeadm token create --print-join-command
12. End to End Test
===================
https://www.youtube.com/watch?v=eJQ-Yla85rM&list=PL2We04F3Y_41jYdadX55fdJplDvgNGENo&index=19

13. TroubleShooting
=====================
    1. Application Failure
        - Check the URL
        - Check the service ( k describe svc)
        - Check the pod ( k describe svc)
        - check the db svc
        - check the db pod
    2. Control Plane failure
        - Check the status of node
        - Check status of pods
        - Check status of pods in kube-system
        - Check the status of services(service status kubelet)
        - root@controlplane:/etc/kubernetes/manifests# kubectl -n kube-system logs kube-controller-manager-controlplane
    3. worker Node failure
        - kubectl describe node 
        - top
        - df -h
        - kubelet service ( certificates, Issuer)
        - service kubelet start
        - journalctl -u kubelet -f
        - kubectl cluster-info
        - systemctl status kubelet
        - systemctl start kubelet
        - systemctl restart kubelet
    4. Network
        - Check for installation of CRI plugins(weave, flunnel,etc.)
14. Other Topics
==================
    1. JSON Path
        In the upcoming lecture we will explore some advanced commands with kubectl utility. But that requires JSON PATH. If you are new to JSON PATH queries get introduced to it first by going through the lectures and practice tests available here.
        https://kodekloud.com/p/json-path-quiz
        Once you are comfortable head back here:
        I also have some JSON PATH exercises with Kubernetes Data Objects. Make sure you go through these:
        https://mmumshad.github.io/json-path-quiz/index.html#!/?questions=questionskub1
        https://mmumshad.github.io/json-path-quiz/index.html#!/?questions=questionskub2    

        - YAML
            > Access YAML
        - JSON Path
            - Access Dict and List
                > $.* - Access All
                > $.[*] - Access All Element in List
                > $.[0,4] - Access 1st and 5th element of the list
                > $.[0:8] - First 8 elements
                > $.[-1:0] - get the last element
                > $.[-3:0] - Last 3 elements
            - Access Kubernetes Objects
                > $.kind
                > $metadata.name
                > $.status.containerStatuses[?(@.name == 'redis-container')].restartCount
                > Access 
    2. JSON Path with Kubectl
        - Why JSON Path?
            - To parse 100 of nodes and 1000s of pods,deployments, etc.
        - Kubectl
            - Gets information from api-server in JSON format
            - Parses and displays easy readble format in console
            - kubectl get pods -o=jsonpath={.items[*].metadata.name}
            - loops
                kubectl get pods -o=jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.status.capacity.cpu}{"\n"}{end}'
            - custom-columns option in kubectl
                - kubectl get nodes  -o=custom-columns=NODE:metadata.name, CPU:.status.capacity.cpu
            - --sort-by option





    




    
         


