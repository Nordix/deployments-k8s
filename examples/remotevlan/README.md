# NSM Remote Vlan VPP Examples

## Requires

- [spire](../spire)

## Includes

- [Kernel2RVlan](../use-cases/Kernel2RVlan)

## Run

Create secondary bridge network and connect kind-worker nodes:

```bash
docker network create bridge-2
docker network connect bridge-2 kind-worker
docker network connect bridge-2 kind-worker2
```

Rename the newly generated interface to eth1 in both kind-workers:

```bash
ifw1=$(echo $(docker exec kind-worker ip link | tail -2 | head -1) | cut -f1 -d"@" | cut -f2 -d" ")
docker exec kind-worker ip link set $ifw1 down
docker exec kind-worker ip link set $ifw1 name eth1
docker exec kind-worker ip link set eth1 up
ifw2=$(echo $(docker exec kind-worker2 ip link | tail -2 | head -1) | cut -f1 -d"@" | cut -f2 -d" ")
docker exec kind-worker2 ip link set $ifw2 down
docker exec kind-worker2 ip link set $ifw2 name eth1
docker exec kind-worker2 ip link set eth1 up
```

Create ns for deployments:

```bash
kubectl create ns nsm-system
```

Apply NSM resources for basic tests:

```bash
kubectl apply -k .
```

## Cleanup

To free resources follow the next command:

```bash
kubectl delete mutatingwebhookconfiguration --all
kubectl delete ns nsm-system
```

Delete secondary network and kind-worker node connections:

```bash
docker network disconnect bridge-2 kind-worker
docker network disconnect bridge-2 kind-worker2
docker network rm bridge-2
docker exec kind-worker ip link del eth1
docker exec kind-worker2 ip link del eth1
```
