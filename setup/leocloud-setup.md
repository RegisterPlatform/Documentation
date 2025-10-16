---
layout: page
title: Leocloud setup
permalink: /leocloud-setup/
---

# Leocloud Setup

- [Firststep](https://cloud.htl-leonding.ac.at/html/user-manual.html)
- [Volume Claim setup](#volume-claim)

## Volume Claim
You need a volumne claim for the database and the image you will save. A simple example of the volume claim is this

```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: [volume name]
  namespace: student-[your username]
  labels:
    app: [name of the app]
spec:
  accessModes:
    - [Your access | (ReadOnlyMany, ReadWriteMany, ReadWriteOnce)]
  resources:
    requests:
      storage: [The size | z.B 10Ti]
```

The name of the app isn't needed, but it says which app should use it. (It's a art of style)

## Database
When you have a [volume claim](#volume-claim) you can create a database service on the cloud.
You can also follow the introductions of the [repository](https://github.com/marcel-dempers/docker-development-youtube-series)

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: [name of config]
  namespace: [namespace]
  labels:
    app: [name of app]
data:
  POSTGRES_DB: [database with "" | "sample-password"]
  POSTGRES_USER: [username with "" | "sample-user"]
  POSTGRES_HOST: [Hostname with "" | "sample-host"]
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: [config name]
data: 
  pg_hba.conf: |+
    # TYPE  DATABASE        USER            ADDRESS                 METHOD
    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            trust
    # IPv6 local connections:
    host    all             all             ::1/128                 trust
    # Allow replication connections from localhost, by a user with the
    # replication privilege.
    local   replication     all                                     trust
    host    replication     all             127.0.0.1/32            trust
    host    replication     all             ::1/128                 trust
    host    all             all             all                     trust
  postgresql.conf: |+
    data_directory = '/data/pgdata'
    hba_file = '/config/pg_hba.conf'
    ident_file = '/config/pg_ident.conf'

    port = 5432
    listen_addresses = '*'
    max_connections = 100
    shared_buffers = 128MB
    dynamic_shared_memory_type = posix
    max_wal_size = 1GB
    min_wal_size = 80MB
    log_timezone = 'UTC'
    datestyle = 'iso, mdy'
    timezone = 'UTC'

    #locale settings
    lc_messages = 'en_US.utf8'			# locale for system error message
    lc_monetary = 'en_US.utf8'			# locale for monetary formatting
    lc_numeric = 'en_US.utf8'			# locale for number formatting
    lc_time = 'en_US.utf8'				# locale for time formatting

    default_text_search_config = 'pg_catalog.english'

    #replication
    wal_level = replica
    archive_mode = on
    archive_command = 'test ! -f /data/archive/%f && cp %p /data/archive/%f'
    max_wal_senders = 3
  pg_ident.conf: |+
    # PostgreSQL User Name Maps
    # =========================
    #
    # Refer to the PostgreSQL documentation, chapter "Client
    # Authentication" for a complete description.  A short synopsis
    # follows.
    #
    # This file controls PostgreSQL user name mapping.  It maps external
    # user names to their corresponding PostgreSQL user names.  Records
    # are of the form:
    #
    # MAPNAME  SYSTEM-USERNAME  PG-USERNAME
    #
    # (The uppercase quantities must be replaced by actual values.)
    #
    # MAPNAME is the (otherwise freely chosen) map name that was used in
    # pg_hba.conf.  SYSTEM-USERNAME is the detected user name of the
    # client.  PG-USERNAME is the requested PostgreSQL user name.  The
    # existence of a record specifies that SYSTEM-USERNAME may connect as
    # PG-USERNAME.
    #
    # If SYSTEM-USERNAME starts with a slash (/), it will be treated as a
    # regular expression.  Optionally this can contain a capture (a
    # parenthesized subexpression).  The substring matching the capture
    # will be substituted for \1 (backslash-one) if present in
    # PG-USERNAME.
    #
    # Multiple maps may be specified in this file and used by pg_hba.conf.
    #
    # No map names are defined in the default configuration.  If all
    # system user names and PostgreSQL user names are the same, you don't
    # need anything in this file.
    #
    # This file is read on server startup and when the postmaster receives
    # a SIGHUP signal.  If you edit the file on a running system, you have
    # to SIGHUP the postmaster for the changes to take effect.  You can
    # use "pg_ctl reload" to do that.

    # Put your actual configuration here
    # ----------------------------------

    # MAPNAME       SYSTEM-USERNAME         PG-USERNAME
---
apiVersion: v1
kind: Secret
metadata:
  name: [name of secret]
  namespace: [namespace]
  labels:
    app: [name of app]
data:
  POSTGRES_PASSWORD: supersecretpassword
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: [name of deployment]
  namespace: [namespace]
  labels:
    app: [name of app]
    tier: database
spec:
  replicas: 1
  selector:
    matchLabels:
      app: [name of app]
      tier: database
  template:
    metadata:
      labels:
        app: [name of app]
        tier: database
    spec:
      terminationGracePeriodSeconds: 30
      initContainers:
        - name: init
          image: postgres:15.0
          command: ["bash", "-c"]
          args:
          - |
            mkdir -p /data/archive && chown -R 999:999 /data/archive
          volumeMounts:
            - name: postgredb
              mountPath: /data
              readOnly: false
      containers:
        - name: [name of container]
          image: postgres:13.5
          imagePullPolicy: "IfNotPresent"
          resources:
            limits:
              memory: 512Mi
              cpu: "0.5"
            requests:
              memory: 128Mi
              cpu: "0.1"
          ports:
            - containerPort: 5432
              name: database
          env:
            - name: POSTGRES_DB
              valueFrom:
                configMapKeyRef:
                  name: [name of config with database]
                  key: POSTGRES_DB
            - name: POSTGRES_USER
              valueFrom:
                configMapKeyRef:
                  name: [name of config with username]
                  key: POSTGRES_USER
            - name: POSTGRES_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: [name of secret with password]
                  key: POSTGRES_PASSWORD
            - name: PGDATA
              value: "/data/pgdata"
          args: ["-c", "config_file=/config/postgresql.conf"]
          volumeMounts:
            - name: config
              mountPath: /config
              readOnly: true
            - name: postgredb
              mountPath: /data
              readOnly: false
      volumes:
        - name: config
          configMap:
            name: [name of config with pg_ files]
            defaultMode: 0755
        - name: postgredb 
          persistentVolumeClaim:
            claimName: [name of volume claim]
---
apiVersion: v1
kind: Service
metadata:
  name: [name of service]
  namespace: [namespace]
  labels:
    app: [name of app]
spec:
  ports:
    - port: 5432
      name: external-postgres
      protocol: TCP
  selector:
    app: [name of app]
    tier: database
```

In this yaml deployment you create config maps and secrets. After this you create the deployment itself. And for every deployment you need a service. The labels help organizing kubernetes. When you set the label the deployment and service are added to a group with the name.

## Docker Secret
You can create a docker secret with the command.
```shell
kubectl create secret docker-registry NAME --docker-username=user --docker-password=password --docker-email=email [--docker-server=string] [--from-file=[key=]source] [--dry-run=server|client|none]
```

## React App
If you have created the docker secret for pulling. You can create a react app with the yaml file below.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: [name of deployment]
  labels:
    app: [name of app]
spec:
  replicas: 1
  selector:
    matchLabels:
      app: [name of app]
  template:
    metadata:
      labels:
        app: [name of app]
    spec:
      imagePullSecrets:
      - name: [name of your docker grand]
      containers:
      - name: [name of container]
        image: [ghcr image | nginx:stabel]
        ports:
        - containerPort: 80
        resources:
          limits:
            memory: "512Mi"
            cpu: "500m"
          requests:
            memory: "256Mi"
            cpu: "250m"
---
apiVersion: v1
kind: Service
metadata:
  name: [service name]
spec:
  selector:
    app: [name of app]
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

In this yaml file you create a deployment for nginx. You can pull a public image or a private image. For the private image you need a [docker secret](#docker-secret) for login. And if you let the Frontend run on another subpath. 

You need to set the public url
```
ENV PUBLIC_URL=/frontend
```

## Hints

### Permission View
You can run the auth command a second time. It can update your permissions if you get new permissions from the sysadmin
```shell
leocloud auth login --force
```

