# stack-thanos-receiver
fonte: https://thanos.io/v0.20/components/receive.md/

#Obs: Utilizar exportação das metricas do Prometheus via remote_write
Fonte: https://prometheus.io/docs/prometheus/latest/configuration/configuration/#remote_write

#The example of remote_write Prometheus configuration:

remote_write:
- url: http://<thanos-receive-container-ip>:10908/api/v1/receive


#Funcionamento:


1. Thanos Receiver

    O Receiver coleta métricas enviadas via remote_write do Prometheus.
    Ele distribui as métricas recebidas em diferentes "tenants" ou clusters, com base em configurações de labels (exemplo: receive_cluster).
    Utiliza um hashring para centralizar os endpoints para balancear as métricas entre os diferentes pods de Receiver.

2. Thanos Store Gateway

    Este componente é responsável por acessar dados históricos armazenados em um bucket de objeto (por exemplo, S3 ou GCS).
    Ele torna os dados de longa retenção acessíveis para consultas, integrando-os com as métricas mais recentes.

3. Thanos Compact

    Comprime e organiza os blocos de dados no bucket para economizar espaço e melhorar a eficiência de leitura.
    Realiza operações como compaction e deduplicação nos dados armazenados.

4. Thanos Querier

    Atende às consultas de métricas.
    Atua como uma interface central que consulta tanto dados em tempo real (do Receiver) quanto históricos (do Store Gateway).
    Pode realizar deduplicação de dados para garantir que múltiplos Prometheus com métricas redundantes não impactem os resultados das consultas.
