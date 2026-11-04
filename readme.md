Permissao de cluster admin para a sa do argo cd

oc adm policy add-cluster-role-to-user cluster-admin -z openshift-gitops-argocd-application-controller -n openshift-gitops

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
