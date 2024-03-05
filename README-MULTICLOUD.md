# Steps:
- Create your clusters.
- Get your access and configure your contexts.

oc config get-contexts
oc config use-context <rosa's cluster context>
oc config rename-context <rosa's cluster context> rosa
oc config use-context <aro's cluster context>
oc config rename-context <aro's cluster context> aro

- Configure the access to github actions to the clusters.
https://github.com/redhat-actions/oc-login/wiki/Using-a-Service-Account-for-GitHub-Actions

SA=github-actions-sa
oc config use-context rosa
ROSA_API=$(oc whoami --show-server)

PROJECT=ostoy-dev
oc new-project $PROJECT
oc create sa $SA
oc policy add-role-to-user edit -z $SA
SECRET=$(oc get secrets -o name | grep $SA-token)
ENCODED_TOKEN=$(oc get $SECRET -o jsonpath='{.data.token}{"\n"}' -n $PROJECT )
TOKEN=$(echo $ENCODED_TOKEN | base64 -d)
echo Set envvars: ------------------------------------- 
echo ROSA_SERVER=$ROSA_API
echo ROSA_TOKEN=$TOKEN

PROJECT=ostoy-prod 
oc new-project $PROJECT
oc create sa $SA
oc policy add-role-to-user edit -z $SA
SECRET=$(oc get secrets -o name | grep $SA-token)
ENCODED_TOKEN=$(oc get $SECRET -o jsonpath='{.data.token}{"\n"}' -n $PROJECT )
TOKEN=$(echo $ENCODED_TOKEN | base64 -d)

echo Set envvars: ------------------------------------- 
echo ROSA_TOKEN_TWO=$TOKEN


oc config use-context aro
ARO_API=$(oc whoami --show-server)
PROJECT=ostoy-prod 
oc new-project $PROJECT
oc create sa $SA
oc policy add-role-to-user edit -z $SA
SECRET=$(oc get secrets -o name | grep $SA-token)
ENCODED_TOKEN=$(oc get $SECRET -o jsonpath='{.data.token}{"\n"}' -n $PROJECT )
TOKEN=$(echo $ENCODED_TOKEN | base64 -d) 

echo Set envvars: ------------------------------------- 
echo ARO_SERVER=$ARO_API
echo ARO_TOKEN=$TOKEN

---Secrets to set in github actions:
QUAY_BEND_LINK = quay.io/danielpenagos/ostoy
QUAY_FEND_LINK = quay.io/danielpenagos/ostoy-microservice
ROSA_SERVER
ROSA_TOKEN
ARO_SERVER
ARO_TOKEN
ROSA_SERVER_TWO
ROSA_SERVER_TWO

envvars:
ARO_LIVE = true
ROSA_LIVE = true




