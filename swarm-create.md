## Create SWARM on AWS

```shell
# create swarm masters
#
for i in 1 2 3
do
  ./create-node master0$i t2.small
done

# create swarm workers
#
for i in 1 2 3 4 5
do
  ./create-node worker0$i t2.small
done
```

#### List created machines

```shell
$ docker-machine ls -t "30"
```

## Assign swarm managers

```shell
$ docker-machine ssh master01
$ sudo docker swarm init --advertise-addr 34.205.252.209:2377
Swarm initialized: current node (f15m9npvwumliqoe6wzor8tvh) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join \
    --token SWMTKN-1-2ohfpmuvx39e2o7wzqg1qcohoti8layg0vk7ivoebncmw37p9y-ezvmn0oj8a2o1l25l4fyahcn6 \
    34.205.252.209:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

$ sudo docker swarm join-token manager
To add a manager to this swarm, run the following command:

    docker swarm join \
    --token SWMTKN-1-2ohfpmuvx39e2o7wzqg1qcohoti8layg0vk7ivoebncmw37p9y-08zcw2jht968k1tdnf8dofcha \
    34.205.252.209:2377
```

```shell
$ docker-machine ssh master02
$ sudo docker swarm join \
--token SWMTKN-1-2ohfpmuvx39e2o7wzqg1qcohoti8layg0vk7ivoebncmw37p9y-08zcw2jht968k1tdnf8dofcha \
34.205.252.209:2377
$ exit

$ docker-machine ssh master03
$ sudo docker swarm join \
--token SWMTKN-1-2ohfpmuvx39e2o7wzqg1qcohoti8layg0vk7ivoebncmw37p9y-08zcw2jht968k1tdnf8dofcha \
34.205.252.209:2377
$ exit
```

From any swarm manager node you can view the status of managers:

```shell
$ sudo docker node ls
ID                           HOSTNAME  STATUS  AVAILABILITY  MANAGER STATUS
f15m9npvwumliqoe6wzor8tvh *  master01  Ready   Active        Leader
t77rsrfdrq9u3v4rftldyzsgj    master02  Ready   Active        Reachable
ye7iq8hswgacvkz8il51v6je1    master03  Ready   Active        Reachable
```

## Assign swarm workers

```shell
for i in 1 2 3 4 5
do
  docker-machine ssh worker0$i sudo docker swarm join \
  --token SWMTKN-1-2ohfpmuvx39e2o7wzqg1qcohoti8layg0vk7ivoebncmw37p9y-ezvmn0oj8a2o1l25l4fyahcn6 \
  34.205.252.209:2377
done
```

From a manager node:

```
$ sudo docker node ls -t "30"
ID                           HOSTNAME  STATUS  AVAILABILITY  MANAGER STATUS
8caeo3nvjfa5d3jrqamciyijv    worker04  Ready   Active
c4nc3wnr45ii53hli5yomw234    worker03  Ready   Active
dfjrl5767thytai4lz9dfk360    worker05  Ready   Active
f15m9npvwumliqoe6wzor8tvh *  master01  Ready   Active        Leader
fcvzbgziv3ptso1r9egazizqv    worker01  Ready   Active
t77rsrfdrq9u3v4rftldyzsgj    master02  Ready   Active        Reachable
vz489z1vywrthlt4r9bw94zda    worker02  Ready   Active
ye7iq8hswgacvkz8il51v6je1    master03  Ready   Active        Reachable
```