# elastic-stack-docker
Tutorial e execução da Pilha ELK em docker.

*Referência: https://www.elastic.co/pt/blog/getting-started-with-the-elastic-stack-and-docker-compose*

Veremos detalhes de como subir um ambiente Elastic Stack padrão, composto por Elasticsearch, Logstash, Kibana e Beats (ELK-B), com docker.


## Pré-requisitos
Como pré-requisito, o [Docker Desktop ou o Docker Engine com o Docker Compose](https://docs.docker.com/get-docker/) precisará ser instalado e configurado.


Assim, após o docker engine instalado, faça o git clone deste repositório, vá até o diretório e suba o ambiente:
```bash
git clone https://github.com/jandercalmeida/elastic-stack-docker/
```
> Caso não tenha o git no seu ambiente, para instalá-lo no linux: sudo apt install git


Então, execute o docker-compose dentro do diretório baixado:
```bash
docker-compose up
```
> [!NOTE]
> Executando em background:
> ```docker-compose up -d```


## Acessando o KIBANA
Então, para acessar a interface do KIBANA:
http://localhost:5601


## Considerações:
A estrutura de arquivos está definida da seguinte forma:

![image](https://github.com/user-attachments/assets/f15117ef-3336-477a-adbf-0cd2b22288c8)

### Diretório "certs"
Este é o diretório, montado como volume (ver docker-compose.yml), que estarão inseridos os certificados usados pelo elastic, kibana, etc ...



### Diretório "filebeat"
Este é o diretório, montado como volume (ver docker-compose.yml), que estarão inseridos os arquivos de configuração do filebeat.



### Diretório "logstash"
Este é o diretório, montado como volume (ver docker-compose.yml), que estarão os arquivos usados na pipeline e ingest data, usados pelo logstash.

*Uma ressalva aqui, o logstash, neste ambiente, executa todos os arquivos.conf colocados na **subpasta "pipeline"**.

*Os files usados como input de uma pipeline poderão ser colocados na subpasta **"logstash_ingest_data"**, bastando declarar no respectivo arquivo.conf o seu caminho dentro da imagem docker do logstash, como no exemplo:

> path => "/usr/share/logstash/ingest_data/meuarquivo.csv"

> [!NOTE]
> Não é necessário executar logstash -f arquivo.conf ...
> Ao reiniciar o logstash, os ".conf" dentro da pasta "pipeline" serão executados


### Arquivo .env
Aqui estão definidas diversas variáveis, assim como o usuário e senha padrão do kibana: 
> elastic:changeme

### Metricbeat
Nosso Metricbeat depende da integridade do nó ES01 (elastic search) e do nó do Kibana antes de iniciar. As configurações dignas de nota aqui estão no arquivo metricbeat.yml. Já estão habilitados quatro módulos para coleta de métricas, incluindo Elasticsearch, Kibana, Logstash e Docker. 
Isso significa que, depois de verificarmos que o Metricbeat está ativo, podemos entrar no Kibana e navegar até [“Stack Monitoring” (Monitoramento da stack)](http://localhost:5601/app/monitoring) para ver como estão as coisas. (Fonte: https://www.elastic.co/pt/blog/getting-started-with-the-elastic-stack-and-docker-compose)



## Comandos úteis

Reiniciar o logstash:
```docker-compose restart logstash01```

Visualizar os logs do logstash:
```docker-compose logs -f logstash01```
