1. Core Concepts
-----------------
1. Archicture
    a. Master Node(Control Plane)
        1. API Server(kube-apiserver)
            > Authenticate user
            > vaidate request
            > retrive data 
            > update ETCD
            > scheduler
            > kubelet
        2. Scheduler
        3. ETCD
            > install etcd
            > ./etcd
            > Default port 2379
            > etcdctl - cli to mange ETCD
            > Any achange in cluster is updated in ETCD
            > stores data in directory /registery
            > etcdctl commands
                etcdctl snapshot save 
                etcdctl endpoint health
                etcdctl get
                etcdctl put
        4. Kubelet
        5. Controller(Contoller-Manager)
            1. watch status
            2. take action
        6. Network(kube-proxy)

    b. worker Node
        1. Kubelet
        2. Network(kube-proxy)
