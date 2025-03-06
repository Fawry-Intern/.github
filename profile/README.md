### **REQUIREMENTS**  

- **Java >= 21** (Azul Zulu JVM is tested by our CI on GitHub Actions)  
- **PostgreSQL 15** (or compatible version)  

To run the required version of the database server in a container instead of installing it manually, use the following command:  

```sh
docker run --name postgres-15 -p 5432:5432 -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres -d postgres:15
```

To stop and remove the container:  

```sh
docker rm -f postgres-15
```
