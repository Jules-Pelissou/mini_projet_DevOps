Bonjour !

Voici mon mini projet de Dev Ops & déploiement CO/CD
L'application que j'ai choisi est une application monolithique CRUD React simple. 

L'architecture du projet est comme suit

=> crude-app
    => k8s
      => react-crud-deploy.yaml
      => react-crud-pod.yaml
      => react-crud-svc.yaml
    => public 
      => Fichiers de l'application
    => src
      => Fichiers de l'application également
    => Dockerfile
    => Jenkinsfile
    => LICENSE
    => nginx.conf
    => package.json
    => package-lock.json
=> jenkins
  => Dockerfile
=> docker-compose.yaml
=> readme.txt

Dans ce projet j'ai fait appel à plusieurs technologies :

-> Helm pour la mise en place de Prometheus et Graphana
-> Kubernetes pour l'orchestration
-> Nginx pour le serveur Web
-> React pour le front
-> Jenkins pour le CI/CD
-> Prometheus et Graphana pour le monitoring

Voici un petit tutoriel pour faire fonctionner cette application monolithique.

Tout d'abord il vous faut cloner ce repository. 

git clone https://github.com/Jules-Pelissou/mini_projet_DevOps.git
cd mini_projet_DevOps

Une fois le repository cloné, il vous faut lancer les différents containers docker avec la commande

docker compose up -d

Cela devrait lancer les containers Jenkins et l'application en elle même.
Si vous effectuez ces opérations dans les 90 jours, vous n'aurez pas à modifer les différents credentials.
Néanmois, si vous utilisez ce repository après 90 jours il vous faudra mettre a jour les credencials. 
Pour cela il vous faut aller sur votre docker Hub, créer un token et le sauvegarder dans les credentials de Jenkins (user and password).
Il faut que l'id de votre token soit exactement "dockerhub-credential".

Une fois ceci fait, vous ne devriez pas avoir de probmème avec Jenkins.

Sur le repo il vous faudra appliquer les différents outils de déploiements de kubernetes avec ces commandes

kubectl apply -f react-crud-pod.yaml
kubectl apply -f react-crud-deploy.yaml
kubectl apply -f react-crud-svc.yaml

Une fois tous ces fichiers appliqués si vous faites 

kubectl get pods -n default

Vous devriez voir tourner 5 pods (4 du deployment et 1 du pod)

A tout moment il vous est possible d'accéder à l'application sur votre localhost:8080.

Toutefois si vous voulez vérifier le fonctionnement du cluster il vous faudra accéder à Prometheus et Graphana.

Pour cela il vous faut lancer minikube

minikube start

Puis, une fois lancé, il vous faut faire cet enchainement de commandes :

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
kubectl create namespace monitoring
helm install monitoring prometheus-community/kube-prometheus-stack \
  --namespace monitoring

Cela va ajouter promethenus ainsi que graphana dans minikube et créer un namesapce monitoring dans lequel se trouvera Prometheus et Graphana

Lorsque c'est fait, il vous faut rediriger les ports de graphana et prometheus pour pouvoir accéder aux interface graphiques de ces outils

Pour graphana :
kubectl port-forward -n monitoring svc/monitoring-grafana 3000:80
kubectl get secret -n monitoring kube-prom-stack-grafana -o jsonpath="{.data.admin-password}" | base64 -d; echo
La dernière commande permet de récupérer le mot de passe de graphana. Le nom d'utilisateur sera "admin" par défaut.

Pour Prometheus :
kubectl port-forward -n monitoring svc/kube-prom-stack-kube-prome-prometheus 9090:9090

Ainsi sur les ports 9090 et 3000 tournerons Prometheus et Graphana.

Sur graphana pour importer un dahsboard et surveiller les métriques de l'application il vous faut aller dans l'onglet "Dashboard" puis dans "Importer"
Quand vous êtes sur l'interface d'importation, vous pouvez marquer l'id 6417 ou bien 315 pour importer des dashboards simples de suivis du cluster kube.
