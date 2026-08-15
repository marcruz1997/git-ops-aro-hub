Permissao de cluster admin para a sa do argo cd

oc adm policy add-cluster-role-to-user cluster-admin -z openshift-gitops-argocd-application-controller -n openshift-gitops

token github

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

SA - Backup etcd permissao de executar o job

oc adm policy add-scc-to-user privileged
-z openshift-backup
-n ocp-etcd-backup
Liberar grupos e user para argocd

oc edit argocd openshift-gitops -n openshift-gitops

spec: rbac: policy: | g, ocp-cluster-admins, role:admin g, admin, role:admin defaultPolicy: '' scopes: '[groups]'

oc rollout restart deployment/openshift-gitops-server -n openshift-gitops.
--------
Secret Repo

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

--------