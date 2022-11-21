# pterohelm
Horizontal scaling is yet to be tested (Probably won't work as we use PVC)
Not ready for production use.
*This is my first public Helm chart, any contrubutions to fix/refactor/add functionality are welcome* 

# Installing
Setup you values file
```bash
helm upgrade --install -f values.yaml
```
## Adding S3 secrets
Delete default secret, where is `<secret-name>` - secret name from values.yaml (`<release-name>-s3-secrets` by default):
```
kubectl delete secret <secret-name>
```
Create generic secret:
```bash
kubectl create secret generic <secret-name> --from-literal=accessKey=<BOGUS_ACCESS_KEY> --from-literal=secretKey=<BOGUS_SECRET_KEY>
```
Delete the Panel pod for restart:
```
kubectl delete pods -lrelease=<helm release name>
```
Alternativly you can create custom secret before helm installation and specify it's name on installation

# Migrating from OS distribution
Install helm chart as usual. Run bash in the created pod, locate .env file and change APP_KEY and HASHID variables accordingly

# About Wings in Kubernetes
This chart installs the panel in Kubernetes only. You still need to create Wings instances as usual by hand. 

First of all, attaching Panel to Kubernetes might be possible but would require building another software agent that would mimick Wing's API and relay it to Kubernetes API so Pterodactyls containers would work in Kubernetes nativly. And, well, that's *A LOT* of work

Second of all, running Wings instance in Kubernetes on it's own is very hacky and would require a lot of in-depth knowledge about Kubernetes. Referencing myself: it's a lot of work *that don't really need to be done (see below)*

Third, Pterodactyl is an orchestration software by itself that is meant for "long-living" games. Meaning the game server's data should be presisted, and it's relevant for each run (Like Minecraft, which map is relevant between restarts). Kubernetes is not really made of such behavior (though the support was added with StatefulSet API, but K8S it made for horizontaly scalable sofware, games with long living maps are not really scalable on it's own)

If you need to create servers "on-premise" (Any other game that creates servers when needed, e.g. Overwatch, Dota 2 and etc.) look into [Agones](https://agones.dev/site/) which is scalable Kubernetes based setup made for these kind of use cases.

UPD: I must do justice for Minecraft. It can be scaled by using proxies, which are, by the way, very well suited for Kubernetes,. I don't think anyone would bring this enormous challange onto themself just to put software made specificly for Minecraft into Kube using Pterodactyl. 
If anything, it's possible to create Pterodactyl containers on request by using their API which is much easier.