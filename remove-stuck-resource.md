To remove project that is stuck on filnalizers do:
https://medium.com/@clouddev.guru/how-to-fix-kubernetes-namespace-deleting-stuck-in-terminating-state-5ed75792647e

```
kubectl get namespace acse-project -o json > acse-project.json
```
remove finalizers serction from file, then execute:

```
kubectl replace --raw "/api/v1/namespaces/acse-project/finalize" -f acse-project.json
```

To remove CRD stuck in deleted namespace:
```
kubectl patch --type=merge -n acse-project helmreleases.app.ibm.com/acse-frontend-acse-frontend-subscription-acse-project -p '{"metadata":{"finalizers":[]}}'
```
