# DIY Lab of Docker Swarm

1. Setting up instances
    1. run `vagrant up m1 m2 w1 w2 w3` for creating instances that consist of 2 master and 3 worker nodes
    2. run `vagrant up r0` for creating instance to put local image registry
    3. run `vagrant up db1` for creating instance to store database
2. Create context by running script `./host-setup.sh`