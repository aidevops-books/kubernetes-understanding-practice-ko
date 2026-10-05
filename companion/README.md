# Mini CloudShop 개정 실습 순서

학습용 Cluster에서만 사용한다. 기존 kind Cluster/namespace를 임의로 변경하거나 삭제하는 자동 스크립트는 제공하지 않는다. 현재 context와 Node 상태를 먼저 확인한다. 명령은 이 `companion/` 폴더 기준이다.

1. 3권의 `companion/api`, `companion/web`에서 각각 `intro-api:1.0`, `intro-web:1.0`을 빌드한다. 이번 개정에는 `/ready`, Redis 준비 확인, Web 프록시가 추가되어 이전 Image를 재사용하면 안 된다.
2. `kind load docker-image intro-api:1.0 intro-web:1.0 --name cloudshop`으로 4장에서 만든 학습 Cluster에 전달한다.
3. 5장 단독 Pod 실습 후 `07-deployment.yaml`, `09-service.yaml`을 차례로 적용한다. 초기 API는 Redis 없이 `/health`만 확인한다.
4. 10장의 Traefik 설치 절차를 수행하고 `10-web.yaml`, `10-ingress.yaml`을 적용한다. Web과 `/health` 라우팅을 확인한다.
5. `11-config.yaml`, `12-redis-pvc.yaml`을 적용한다. Secret 값은 예제용이며 실제 비밀번호가 아니다. 이 API는 Redis 인증을 구현하지 않으므로 DB_PASSWORD를 Redis 인증으로 오해하지 않는다.
6. 13장에서 `13-api-operational.yaml`을 적용한다. 같은 API Deployment를 ConfigMap 주입, 자원 설정, `/ready` Probe가 있는 버전으로 갱신한다.
7. Redis와 API의 rollout 완료, `/api/count`의 증가를 확인한다. 이후 metrics-server와 `14-hpa.yaml`로 14장을 진행한다.

```bash
kubectl apply -f k8s/11-config.yaml
kubectl apply -f k8s/12-redis-pvc.yaml
kubectl rollout status deployment/cloudshop-redis --timeout=180s
kubectl apply -f k8s/13-api-operational.yaml
kubectl rollout status deployment/cloudshop-api --timeout=180s
```

`13-api-operational.yaml`을 쓴 뒤 초기 `07-deployment.yaml`을 다시 적용하면 설정과 Probe를 되돌릴 수 있다. 파일 전체를 한 번에 무차별 apply하지 말고 단계에 맞는 파일을 선택한다. HPA 사용 뒤 replicas 수를 선언 파일로 덮어쓰는 일도 피한다.

8. 15장: `15-jobs.yaml`(Job, CronJob). Job Pod에는 17장 NetworkPolicy가 허용하는 `cloudshop/redis-client: "true"` Label이 있다.
9. 16장: `kubectl delete deployment cloudshop-redis` 뒤 `16-redis-statefulset.yaml`. 이 파일은 `cloudshop-redis` Service도 포함하므로 빈 Cluster에서도 단독으로 Redis를 세운다. 새 PVC를 사용하므로 12장 카운터는 이어지지 않는다.
10. 17장: `17-namespace-guardrails.yaml`(cloudshop-dev Namespace, Quota, LimitRange, RBAC), `17-network-policy.yaml`(default Namespace의 Redis 접근 제한).
11. 18장: Gateway API CRD v1.5.1과 Traefik Gateway provider 설치 뒤 `18-gateway.yaml`. 10장의 Ingress와 같은 경로를 동시에 다루지 않도록 비교 전에 Ingress를 삭제한다.
12. 20장(빈 Cluster): `11-config.yaml`, `16-redis-statefulset.yaml` → `13-api-operational.yaml`, `09-service.yaml`, `10-web.yaml` → `17-network-policy.yaml`, `15-jobs.yaml`, `18-gateway.yaml` 순서.

PVC/Volume 삭제는 별도 데이터 삭제 작업이다. Pod 교체 실습의 정리로 PVC를 지우지 않는다. 15~18장과 20장의 절차는 kind v0.33 3-Node(Kubernetes v1.37.0), Traefik Chart 41.6.0, Gateway API v1.5.1에서 실제로 실행해 확인했다. 20장의 순서는 빈 Cluster에서 처음부터 재실행했다.
