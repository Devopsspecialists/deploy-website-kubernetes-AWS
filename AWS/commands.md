## configure AWS CLI
1. create user in aws
2. brew install awscli
3. aws config
4. aws ec2 describe-instances 
### It's done if you get {"Reservations": []}, otherwise follow further
5. create role in AdministratorAccess role in aws
6. aws sts 'YOUR OWN CREATED ROLE NAME' --role-arn 'ARN FROM AWS' --role-session 'NAME IT WHAT YOU WANT' --external-id 'YOUR PASSWORD'
7. export AWS_ACCESS_KEY_ID='YOUR ACCESSKEYID'
7.export AWS_SECRET_ACCESS_KEY='YOUR SECRETACCESSKEY'
8. export AWS_SESSION_TOKEN='YOUR SESSIONTOKEN'
9. aws eks update-kubeconfig --name explore-california-cluster
10. kubectl config set-context 'THE NAME OF USERS THAT WE CREATED IN POINT 9'
11. kubectl get nodes -n kubesystem

## Deploy Docker image into AWS ECR
1. aws ecr describe-repositories
2. registry="THE repositoryUri from point 1"
3. aws ecr get-login-password OR
4. password="$(aws ecr get-login-password)"
5. docker login "registry" --username AWS --password "$password"
6. docker tag explorecalifornia.com:latest "${registry}:latest"
7. docker push "${registry}:latest"

## Deploy Helm onto AWS
1. kubectl create sercret docker-registry explorecalifornia --docker-server=$registry --docker-username=AWS --docker-password=$password
2. helm upgrade --atomic --install explore-california-website ./chart --values ./chart/values-aws.yaml
3. kubectl describe ingress explore-california-website
### take the 'Address' there and paste in the webbrowser.
4. while true; do curl --connect-timeout 1 ADDRESS && brek || sleep 1
; done
### To speed up the process run this command.'ADDRESS', is the address has taken in point 3.

## Cleaning up everything.
1. helm uninstall explore-california
2. docker stop local-registry && docker rm local-registry
3. ./kind delete cluster --name explorecalifornia.com
4. docker images | grep explorecalifornia | awk '{print $3}' | xargs docker rmi -f


