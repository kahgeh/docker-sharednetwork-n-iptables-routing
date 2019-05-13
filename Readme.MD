# Build
cd serviceA
docker build . -t=servicea

cd ../serviceB
docker build . -t=serviceb

# Run 
## start servicea normally
`docker run -d --rm --name=servicea -e CONTAINER_NAME=serivcea --cap-add=NET_ADMIN servicea`

## specify --network=container:servicea when starting serviceb
`docker run -d --rm --network=container:servicea -e CONTAINER_NAME=serivceb --cap-add=NET_ADMIN --name=serviceb serviceb`

## Setup iptables in container servicea to route a new port 9090 to serviceb port 8081

Connect to servicea container : `docker exec -it servicea sh`

Setup route : `iptables -t nat -A OUTPUT -o lo -p tcp --dport 9090 -j REDIRECT --to-port 8081`

# Test effect of iptables port forwarding and sharing same network namespace 

Connect to servicea container ( if not already available ) : `docker exec -it servicea sh`

Observe how request to port 9090 in servicea container gets directed to serviceb
```
/app # curl localhost:9090
{ "hello from": "serviceb" }
```