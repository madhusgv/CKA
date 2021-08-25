
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
    3. 

