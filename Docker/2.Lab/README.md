
## 1. Explore Default Docker Networks

**Step 1: List Existing Networks**

```bash
docker network ls
```

You should see default networks: `bridge`, `host`, and `none`. Each has a specific use-case and driver

**Step 2: Inspect a Network**

```bash
docker network inspect bridge
```

This shows network details, including connected containers and subnet info

---

## 2. Create and Use a Custom Bridge Network

**Step 3: Create a User-Defined Bridge Network**

```bash
docker network create --driver bridge my_custom_net
docker network ls
```

User-defined bridge networks provide better DNS-based service discovery and isolation than the default bridge

**Step 4: Run Containers on the Custom Network**

```bash
docker run -d --name container1 --network my_custom_net nginx
docker run -d --name container2 --network my_custom_net busybox sleep 3600
```

**Step 5: Test Communication**

```bash
docker exec -it container2 ping container1
```

Containers on the same user-defined bridge can communicate using container names as DNS

---

## 3. Network Isolation and Connectivity

**Step 6: Try to Ping from a Container on the Default Bridge**

```bash
docker run -d --name container3 busybox sleep 3600
docker exec -it container3 ping container1
```

This should fail, demonstrating network isolation between custom and default bridges

**Step 7: Connect a Container to Multiple Networks**

```bash
docker network connect my_custom_net container3
docker exec -it container3 ping container1
```

Now, `container3` can reach `container1`, showing multi-network connectivity

---

## 4. Assign Static IPs to Containers

**Step 8: Assign a Static IP**

```bash
docker network create --subnet 172.18.0.0/16 mynet123
docker run -d --name static1 --network mynet123 --ip 172.18.0.22 nginx
docker network inspect mynet123
```

This is useful for legacy applications or specific network requirements

---

## 5. Explore Other Network Drivers

**Step 9: Host Network**

```bash
docker run -d --name hostnet --network host nginx
```

This container shares the host’s network stack—no isolation

**Step 10: None Network**

```bash
docker run -d --name nonenet --network none nginx
```

This container has no network connectivity

---

