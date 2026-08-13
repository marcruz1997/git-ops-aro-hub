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

ARGO INSTANCE CONFIG

apiVersion: argoproj.io/v1beta1
kind: ArgoCD
metadata:
  name: openshift-gitops
  namespace: openshift-gitops
spec:
  networkPolicy: {}
  server:
    autoscale:
      enabled: false
    grpc:
      ingress:
        enabled: false
    ingress:
      enabled: false
    resources:
      limits:
        cpu: 500m
        memory: 256Mi
      requests:
        cpu: 125m
        memory: 128Mi
    route:
      enabled: true
    service:
      type: ''
  grafana:
    enabled: false
    ingress:
      enabled: false
    resources:
      limits:
        cpu: 500m
        memory: 256Mi
      requests:
        cpu: 250m
        memory: 128Mi
    route:
      enabled: false
  monitoring:
    enabled: false
  notifications:
    enabled: false
  prometheus:
    enabled: false
    ingress:
      enabled: false
    route:
      enabled: false
  initialSSHKnownHosts: {}
  imageUpdater:
    enabled: false
  sso:
    dex:
      openShiftOAuth: true
      resources:
        limits:
          cpu: 500m
          memory: 256Mi
        requests:
          cpu: 250m
          memory: 128Mi
    provider: dex
  applicationSet:
    resources:
      limits:
        cpu: "2"
        memory: 1Gi
      requests:
        cpu: 250m
        memory: 512Mi
    webhookServer:
      ingress:
        enabled: false
      route:
        enabled: false
  rbac:
    defaultPolicy: ''
    policy: |
      g, system:cluster-admins, role:admin
      g, cluster-admins, role:admin
    scopes: '[groups]'
  repo:
    env:
      - name: KUSTOMIZE_PLUGIN_HOME
        value: /etc/kustomize/plugin
    initContainers:
      - name: policy-generator-install
        image: registry.redhat.io/rhacm2/multicluster-operators-subscription-rhel9:v2.17.0
        command:
          - /bin/bash
        args:
          - "-c"
          - cp /policy-generator/PolicyGenerator-not-fips-compliant /policy-generator-tmp/PolicyGenerator
        resources: {}
        volumeMounts:
          - name: policy-generator
            mountPath: /policy-generator-tmp
    resources:
      limits:
        cpu: "1"
        memory: 1Gi
      requests:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: policy-generator
        mountPath: /etc/kustomize/plugin/policy.open-cluster-management.io/v1/policygenerator
    volumes:
      - name: policy-generator
        emptyDir: {}
  resourceExclusions: |
    - apiGroups:
      - ""
      - discovery.k8s.io
      clusters:
      - '*'
      kinds:
      - Endpoints
      - EndpointSlice
    - apiGroups:
      - apiregistration.k8s.io
      clusters:
      - '*'
      kinds:
      - APIService
    - apiGroups:
      - coordination.k8s.io
      clusters:
      - '*'
      kinds:
      - Lease
    - apiGroups:
      - authentication.k8s.io
      - authorization.k8s.io
      clusters:
      - '*'
      kinds:
      - SelfSubjectReview
      - TokenReview
      - LocalSubjectAccessReview
      - SelfSubjectAccessReview
      - SelfSubjectRulesReview
      - SubjectAccessReview
    - apiGroups:
      - certificates.k8s.io
      clusters:
      - '*'
      kinds:
      - CertificateSigningRequest
    - apiGroups:
      - cert-manager.io
      clusters:
      - '*'
      kinds:
      - CertificateRequest
    - apiGroups:
      - cilium.io
      clusters:
      - '*'
      kinds:
      - CiliumIdentity
      - CiliumEndpoint
      - CiliumEndpointSlice
    - apiGroups:
      - kyverno.io
      - reports.kyverno.io
      - wgpolicyk8s.io
      clusters:
      - '*'
      kinds:
      - PolicyReport
      - ClusterPolicyReport
      - EphemeralReport
      - ClusterEphemeralReport
      - AdmissionReport
      - ClusterAdmissionReport
      - BackgroundScanReport
      - ClusterBackgroundScanReport
      - UpdateRequest
    - apiGroups:
      - tekton.dev
      clusters:
      - '*'
      kinds:
      - TaskRun
      - PipelineRun
  ha:
    enabled: false
    resources:
      limits:
        cpu: 500m
        memory: 256Mi
      requests:
        cpu: 250m
        memory: 128Mi
  kustomizeBuildOptions: --enable-alpha-plugins
  tls:
    ca: {}
  redis:
    resources:
      limits:
        cpu: 500m
        memory: 256Mi
      requests:
        cpu: 250m
        memory: 128Mi
  controller:
    processors: {}
    resources:
      limits:
        cpu: "2"
        memory: 2Gi
      requests:
        cpu: 250m
        memory: 1Gi
    sharding: {}