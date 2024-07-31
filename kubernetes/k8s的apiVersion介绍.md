## 以下是k8s:V1.16.0版本的apiVersion和控制的资源和功能介绍

- `admissionregistration.k8s.io/v1` 和 `admissionregistration.k8s.io/v1beta1`：这两个版本包含了用于配置和管理准入控制器的API，如MutatingWebhookConfiguration和ValidatingWebhookConfiguration等。

- `apiextensions.k8s.io/v1` 和 `apiextensions.k8s.io/v1beta1`：这两个版本包含了用于自定义资源定义（CRD）和自定义资源（CR）的API，允许您扩展Kubernetes API和创建自定义资源对象。

- `apiregistration.k8s.io/v1` 和 `apiregistration.k8s.io/v1beta1`：这两个版本包含了用于API服务注册和发现的API，如APIService和APIServiceCondition等。

- `authentication.k8s.io/v1` 和 `authentication.k8s.io/v1beta1`：这两个版本包含了用于用户身份验证的API，如TokenRequest和TokenReview等。

- `authorization.k8s.io/v1` 和 `authorization.k8s.io/v1beta1`：这两个版本包含了用于访问控制的API，如SubjectAccessReview和LocalSubjectAccessReview等。

- `autoscaling/v1`、`autoscaling/v2beta1` 和 `autoscaling/v2beta2`：这些版本包含了用于自动扩展应用程序的API，如HorizontalPodAutoscaler等。

- `batch/v1`、`batch/v1beta1` 和 `batch/v2alpha1`：这些版本包含了用于批处理任务的API，如Job和CronJob等。

- `certificates.k8s.io/v1beta1`：这个版本包含了用于管理Kubernetes证书的API，如CertificateSigningRequest等。

- `coordination.k8s.io/v1` 和 `coordination.k8s.io/v1beta1`：这两个版本包含了用于协调处理的API，如Lease和LeaseSpec等。

- `events.k8s.io/v1beta1`：这个版本包含了用于事件记录和处理的API，如Event和EventSource等。

- `node.k8s.io/v1beta1`：这个版本包含了用于节点配置和管理的API，如RuntimeClass和Overhead等。

- `policy/v1beta1`：这个版本包含了用于策略配置和管理的API，如PodDisruptionBudget等。

- `rbac.authorization.k8s.io/v1` 和 `rbac.authorization.k8s.io/v1beta1`：这两个版本包含了用于角色基于访问控制（RBAC）的API，如Role、RoleBinding和ClusterRole等。

- `scheduling.k8s.io/v1` 和 `scheduling.k8s.io/v1beta1`：这两个版本包含了用于调度配置和管理的API，如PriorityClass和Priority等。

这些是您获取的结果中的所有API版本。每个版本都提供了不同的功能和资源类型，您可以根据需要选择适合的API版本来创建和管理Kubernetes资源。