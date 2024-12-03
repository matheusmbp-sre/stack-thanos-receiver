# stack-thanos-receiver
fonte: https://thanos.io/v0.20/components/receive.md/

#Obs: Utilizar exportação das metricas do Prometheus via remote_write
Fonte: https://prometheus.io/docs/prometheus/latest/configuration/configuration/#remote_write

#The example of remote_write Prometheus configuration:
spec:
  remote_write:
  - url: http://<thanos-receive-container-ip>:10908/api/v1/receive
