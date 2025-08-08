```bash
#!/bin/bash

# FIRST RUN ROC LOGIN -C CAAS_CLUSTER_NAME K8S_TENANT_NAME

if [ $# -lt 8 ] || [ $# -gt 9 ]; then
  echo "Usage: $0 <CAAS_CLUSTER_NAME> <CAAS_CLUSTER_NAMESPACE> <CLUSTER_NAME> <TENANT> <PROM_ENDPOINT> <AM_ENDPOINT> <BB_ENDPOINT> [RULER_ENDPOINT]"
  exit 1
fi

CAAS_CLUSTER_NAME=$1
CAAS_CLUSTER_NAMESPACE=$2
HARBOR_ENDPOINT=$3
CLUSTER_NAME=$4
TENANT=$5
PROM_ENDPOINT=$6
AM_ENDPOINT=$7
BB_ENDPOINT=$8
OIDC_ISSUER_URL="https://qa2-accounts-onecloud.rakuten-it.com/auth/realms/roc"


# Assign values to the deployments array
declare -A deployments
deployments["am-auth"]=$AM_ENDPOINT
deployments["bb-auth"]=$BB_ENDPOINT
deployments["prom-auth"]=$PROM_ENDPOINT

# If RULER_ENDPOINT is provided (8 arguments), add to the deployments
if [ $# -eq 9 ]; then
  deployments["ruler-auth"]=$9
fi


dir_path="manifests/$CAAS_CLUSTER_NAME"
echo "Cluster name : $CLUSTER_NAME"
echo "Path : $dir_path"

if [ ! -d "$dir_path" ]; then
    mkdir -p "$dir_path/base/"
    mkdir -p "$dir_path/overlays/"
fi

if [ ! -d "$dir_path/base/$CLUSTER_NAME" ]; then
  mkdir -p "$dir_path/base/$CLUSTER_NAME"
fi

echo "resources:" > "$dir_path/base/kustomization.yaml"

for deployment in "${!deployments[@]}"; do
  DEPLOY_NAME="$deployment-$CLUSTER_NAME"
  kubectl get deploy "$DEPLOY_NAME" -o yaml > "$dir_path/base/$CLUSTER_NAME/$DEPLOY_NAME.yaml"
  if [ $? -ne 0 ]; then
    echo "Error: Failed to fetch deployment $DEPLOY_NAME"
    exit 1
  fi
  echo "  - $CLUSTER_NAME/$DEPLOY_NAME.yaml" >> "$dir_path/base/kustomization.yaml"
done

if [ ! -d "$dir_path/overlays/$CLUSTER_NAME" ]; then
  mkdir -p "$dir_path/overlays/$CLUSTER_NAME"
fi

cat <<EOF > "$dir_path/overlays/kustomization.yaml"
resources:
  - ../base

patches:
EOF

for deployment in "${!deployments[@]}"; do
  DEPLOY_NAME="$deployment-$CLUSTER_NAME"
  PATCH_FILE="$dir_path/overlays/$CLUSTER_NAME/$DEPLOY_NAME-patch.yaml"
  ENDPOINT="${deployments[$deployment]}"
  cat <<EOF > "$PATCH_FILE"
- op: replace
  path: /spec/template/spec/containers/0/image
  value: $HARBOR_ENDPOINT/$CAAS_CLUSTER_NAMESPACE/oauth2-proxy:7.4.0
- op: replace
  path: /spec/template/spec/containers/0/args
  value:
    - --upstream=http://alertmanager-$CLUSTER_NAME.$CAAS_CLUSTER_NAMESPACE.svc:9093
    - --allowed-group=rns:roc:mon-aas::$TENANT:roles:maas-alertmanager-viewer,rns:roc:mon-aas:::roles:service-provider-admin
    - --http-address=0.0.0.0:3000
    - --provider=keycloak-oidc
    - --scope=openid
    - --oidc-issuer-url=$OIDC_ISSUER_URL
    - --redirect-url=https://qa-mon-aas-auth-proxy.r-local.net/oauth2/callback?redirect=$ENDPOINT/oauth2/callback
    - --pass-host-header=true
    - --pass-access-token=true
    - --set-xauthrequest=true
    - --reverse-proxy=true
    - --auth-logging=true
    - --cookie-secure=false
    - --cookie-expire=10m
    - --email-domain=*
    - --skip-provider-button=true
- op: replace
  path: /spec/template/spec/containers/0/env
  value:
    - name: OAUTH2_PROXY_CLIENT_ID
      valueFrom:
        secretKeyRef:
          key: client-id
          name: maas-keycloak-gatekeeper-client-secret
    - name: OAUTH2_PROXY_CLIENT_SECRET
      valueFrom:
        secretKeyRef:
          key: openid
          name: maas-keycloak-gatekeeper-client-secret
    - name: OAUTH2_PROXY_COOKIE_SECRET
      valueFrom:
        secretKeyRef:
          key: cookie-secret
          name: maas-keycloak-gatekeeper-client-secret
EOF

  cat <<EOF >> "$dir_path/overlays/kustomization.yaml"
  - target:
      kind: Deployment
      name: $DEPLOY_NAME
      namespace: $CAAS_CLUSTER_NAMESPACE
    path: $CLUSTER_NAME/$DEPLOY_NAME-patch.yaml
EOF
done

echo "$dir_path/overlays/kustomization.yaml is created"
```
