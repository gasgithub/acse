# Logowanie poprzez command line

Pobrać i zainstalować klienta
https://mirror.openshift.com/pub/openshift-v4/clients/ocp/

Wejsc przez konsole webowa i skopiowac komende logowania "Copy Login command"

`oc login --token=TWOJ_TOKEN --server=https://URL_klastra:port`


# udostepnienie rejestru
https://cloud.ibm.com/docs/openshift?topic=openshift-registry

From the openshift-image-registry project, make sure that the image-registry service exists for the internal registry.
`oc project openshift-image-registry`
Now using project "openshift-image-registry" on server "https://c100-e.eu-de.containers.cloud.ibm.com:30676".

`oc get svc`

`NAME                      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)     AGE
image-registry            ClusterIP   172.21.52.235   <none>        5000/TCP    5d19h
image-registry-operator   ClusterIP   None            <none>        60000/TCP   5d19h`

Create a secured route for the image-registry service that uses reencrypt TLS termination. With re-encryption, the router terminates the TLS connection with a certificate, and then re-encrypts the connection to the internal registry with a different certificate. 

`oc create route reencrypt --service=image-registry`


`oc get route image-registry
NAME             HOST/PORT      PATH   SERVICES         PORT       TERMINATION   WILDCARD
image-registry   image-registry-openshift-image-registry.spx-ocp-c01-9cb7e5637afa55ea707aef0b31c19bc5-0000.eu-de.containers.appdomain.cloud          image-registry   5000-tcp   reencrypt     None`

Edit the route to set the load balancing strategy to source so that the same client IP address reaches the same server, as in a passthrough route setup. You can set the strategy by adding an annotation in the metadata.annotations section: haproxy.router.openshift.io/balance: source.

Login via docker

`docker login -u $(oc whoami) -p $(oc whoami -t) image-registry-openshift-image-registry.spx-ocp-c01-9cb7e5637afa55ea707aef0b31c19bc5-0000.eu-de.containers.appdomain.cloud`

# utworzyć projekt

`oc new-project acse-db`

# Wgranie obrazu
Pociagnac obraz
`docker pull openjdk:8-jre-alpine`

Przetagować:
`docker tag openjdk:8-jre-alpine image-registry-openshift-image-registry.spx-ocp-c01-9cb7e5637afa55ea707aef0b31c19bc5-0000.eu-de.containers.appdomain.cloud/acse-backend/openjdk:8-jre-alpine`

Wypchnac:
`docker push image-registry-openshift-image-registry.spx-ocp-c01-9cb7e5637afa55ea707aef0b31c19bc5-0000.eu-de.containers.appdomain.cloud/acse-backend/openjdk:8-jre-alpine`


# umozliwienie dzialania kontenerow jako root
Przejsc do konkretnego projektu:

`oc project acse-backend`

Wykonac komende:

```oc adm policy add-scc-to-user anyuid -z default
securitycontextconstraints.security.openshift.io/anyuid added to: ["system:serviceaccount:acse-backend:default"]```

