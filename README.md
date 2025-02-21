Stack Thanos Receiver

Objetivo

Este projeto implementa uma solução de monitoramento e gerenciamento de métricas com o Thanos, permitindo a coleta, armazenamento e consulta de métricas de múltiplos clusters Prometheus. A solução garante alta disponibilidade, escalabilidade e retenção de longo prazo. Ideal para ambientes de larga escala com diversos clusters Kubernetes.

Referências:

[Documentação do Thanos Receiver](https://thanos.io/tip/components/receive.md/)

[Documentação do Prometheus Remote Write](https://prometheus.io/docs/specs/remote_write_spec/)
-----------------------------------------------------------------------------------------------------------------------------------------
1. Requisitos

Antes de instalar esta stack, é necessário ter o Prometheus já instalado e configurado.

Dependências:

 - Kubernetes (k8s) v1.20+
 - Helm v3+ (opcional, mas recomendado)
 - Prometheus instalado e rodando no cluster
 - Bucket de armazenamento (S3/GCS ou equivalente) para retenção de dados a longo prazo
-----------------------------------------------------------------------------------------------------------------------------------------
2. Instalação

2.1. Clonar o repositório

 - ~ git clone https://github.com/matheusmbp-sre/stack-thanos-receiver.git
 - ~ cd stack-thanos-receiver

2.2. Configurar os manifests YAML

 Verifique e edite os arquivos YAML conforme necessário, principalmente o thanos-receiver.yaml e o thanos-s3-config.yaml.

 Exemplo de configuração para remote_write no Prometheus:

```yaml
    remoteWrite:
  - url: http://thanos-receiver.monitoring.svc.cluster.local:10908/api/v1/receive
    queueConfig:
      capacity: 50000
      maxSamplesPerSend: 100
      maxShards: 10
    writeRelabelConfigs:
      - action: replace
        replacement: us-east-2
        sourceLabels: [__address__]
        targetLabel: cluster
    remoteTimeout: 30s
```

2.3. Aplicar os manifests no Kubernetes

 - ~ kubectl apply -f thanos-receiver.yaml 
 - ~ kubectl apply -f thanos-store-gateway.yaml
 - ~ kubectl apply -f thanos-querier.yaml
 - ~ kubectl apply -f thanos-compactor.yaml
-------------------------------------------------------------------------------------------------------------------------------------------
3. Como Funciona?

3.1. Componentes da Stack

 a. Thanos Receiver
    - Coleta métricas enviadas via remote_write do Prometheus.
    - Distribui as métricas em diferentes "tenants" ou clusters, baseado em labels (exemplo: receive_cluster).
    - Usa hashing para balancear a carga entre os pods de Receiver.

 b. Thanos Store Gateway
    - Acessa dados históricos armazenados em um bucket de objeto (S3/GCS/etc.).
    - Permite que as consultas acessem tanto dados recentes quanto dados de longa retenção.
       
 c. Thanos Compact
    - Comprime e organiza os blocos de dados no bucket para economizar espaço e melhorar a eficiência de leitura.
    - Realiza compaction e deduplicação dos dados armazenados.

 d. Thanos Querier
    - Interface central que consulta tanto dados em tempo real (Receiver) quanto históricos (Store Gateway).
    - Pode realizar deduplicação para evitar métricas redundantes.
------------------------------------------------------------------------------------------------------------------------------------------
4. Verificação e Testes

4.1. Verifique os Pods

  - ~ kubectl get pods -n monitoring

Todos os pods do Thanos devem estar rodando sem erros.

4.2. Consultar Métricas no Thanos Querier
Acesse a interface do Thanos Querier para executar queries PromQL:
  - ~ kubectl port-forward svc/thanos-querier 9090 -n monitoring

Acesse no navegador: http://localhost:9090
------------------------------------------------------------------------------------------------------------------------------------------
5. Conclusão

Esta stack do Thanos permite que você expanda sua solução Prometheus para um ambiente escalável, distribuído e de alta disponibilidade.
Se você precisa de retenção de longo prazo para suas métricas, esta é a abordagem ideal!

Desenho da solução:
![image](https://github.com/user-attachments/assets/cecf1e52-82e4-442a-aefe-95ce567d1812)


