# stack-thanos-receiver

#Objetivo

Este projeto tem como objetivo implementar uma solução de monitoramento e gerenciamento de métricas com o Thanos. Ele permite a coleta, armazenamento e consulta de métricas de múltiplos clusters Prometheus, garantindo alta disponibilidade, escalabilidade e retenção de longo prazo. Ideal para ambientes em larga escala com diversos clusters k8s. 
fonte: https://thanos.io/v0.20/components/receive.md/

#Obs: Utilizar exportação das metricas do Prometheus via remote_write
Fonte: https://prometheus.io/docs/prometheus/latest/configuration/configuration/#remote_write

#Exemplo de remote_write no Space do manifesto do Prometheus configuration:

![image](https://github.com/user-attachments/assets/19be8a3d-18b5-4802-882a-63c7fb89c753)



#Funcionamento:


1. Thanos Receiver

    - O Receiver coleta métricas enviadas via remote_write do Prometheus.
    - Distribui as métricas recebidas em diferentes "tenants" ou clusters, com base em configurações de labels (exemplo: receive_cluster).
    - Utiliza um hashring para centralizar os endpoints e balancear as métricas entre os diferentes pods de Receiver.

2. Thanos Store Gateway

    - Este componente é responsável por acessar dados históricos armazenados em um bucket de objeto (por exemplo: S3 ou GCS).
    - Ele torna os dados de longa retenção acessíveis para consultas, integrando-os com as métricas mais recentes.

3. Thanos Compact

    - Comprime e organiza os blocos de dados no bucket para economizar espaço e melhorar a eficiência de leitura.
    - Realiza operações como compaction e deduplication nos dados armazenados.

4. Thanos Querier

    - Atende às consultas de métricas.
    - Atua como uma interface central que consulta tanto dados em tempo real (do Receiver) quanto históricos (do Store Gateway).
    - Pode realizar deduplicação de dados para garantir que múltiplos Prometheus com métricas redundantes não impactem os resultados das consultas.


#Desenho da solução:
![image](https://github.com/user-attachments/assets/a5e6e864-73aa-45e0-bfad-b2b30c0222ca)
