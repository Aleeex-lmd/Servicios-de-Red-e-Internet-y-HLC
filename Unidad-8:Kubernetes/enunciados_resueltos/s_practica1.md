# Práctica: Kubernetes

## Ejercicio1: Despliegue en minikube

Vamos a desplegar la web php en nginx, pera esto usaremos la imagen que generamos en otra practica anterior, la imagen es `aleeexlmd/biblioteca`

Comenzaremos generando los ficheros para los servicios

### Persistent Volume y Persistent Volume Claim (MySQL)

```yml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data/mysql"
```

```yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

### Secreto, depployent y service de la base de datos

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysql-secret
type: Opaque
data:
  MYSQL_ROOT_PASSWORD: YWRtaW4=
  MYSQL_USER: YmlibGlvdGVjdXNlcg==
  MYSQL_PASSWORD: YmlibGlvdGVjYXBhc3M=
  MYSQL_DATABASE: YmlibGlvdGVjYQ==
```

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
        - name: mysql
          image: mysql:5.7
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-secret
                  key: MYSQL_ROOT_PASSWORD
            - name: MYSQL_USER
              valueFrom:
                secretKeyRef:
                  name: mysql-secret
                  key: MYSQL_USER
            - name: MYSQL_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-secret
                  key: MYSQL_PASSWORD
            - name: MYSQL_DATABASE
              valueFrom:
                secretKeyRef:
                  name: mysql-secret
                  key: MYSQL_DATABASE
          ports:
            - containerPort: 3306
          resources:
            requests:
              memory: "512Mi"
              cpu: "500m"
            limits:
              memory: "1Gi"
              cpu: "1"
      volumes:
        - name: mysql-storage
          persistentVolumeClaim:
            claimName: mysql-pvc
```

```yml
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  selector:
    app: mysql
  ports:
    - protocol: TCP
      port: 3306
      targetPort: 3306
```

### Secreto, depployment y service de la aplicación php

```yml
apiVersion: v1
kind: Secret
metadata:
  name: biblioteca-db-secret
type: Opaque
data:
  DB_HOST: bXlzcWw=
  DB_NAME: YmlibGlvdGVjYQ==
  DB_USER: YmlibGlvdGVjdXNlcg==
  DB_PASS: YmlibGlvdGVjYXBhc3M==
```

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: biblioteca
spec:
  replicas: 2
  selector:
    matchLabels:
      app: biblioteca
  template:
    metadata:
      labels:
        app: biblioteca
    spec:
      containers:
        - name: biblioteca
          image: aleeexlmd/biblioteca:v1
          env:
            - name: BASE_URL
              value: "http://biblioteca.local"
            - name: DB_HOST
              valueFrom:
                secretKeyRef:
                  name: biblioteca-db-secret
                  key: DB_HOST
            - name: DB_NAME
              valueFrom:
                secretKeyRef:
                  name: biblioteca-db-secret
                  key: DB_NAME
            - name: DB_USER
              valueFrom:
                secretKeyRef:
                  name: biblioteca-db-secret
                  key: DB_USER
            - name: DB_PASS
              valueFrom:
                secretKeyRef:
                  name: biblioteca-db-secret
                  key: DB_PASS
          ports:
            - containerPort: 80
          resources:
            requests:
              memory: "512Mi"
              cpu: "500m"
            limits:
              memory: "1Gi"
              cpu: "1"
```

```yml
apiVersion: v1
kind: Service
metadata:
  name: biblioteca-service
spec:
  selector:
    app: biblioteca
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

### Ingress para acceso

```yml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: biblioteca-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
    - host: biblioteca.local
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: biblioteca-service
                port:
                  number: 80
```
