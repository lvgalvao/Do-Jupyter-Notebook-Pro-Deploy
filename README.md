# Do Jupyter Notebook pro Deploy em Prod

##  Airflow

O Apache Airflow é uma plataforma projetada para criar, agendar e monitorar fluxos de trabalho de forma programática.

Quando os fluxos de trabalho são definidos como código, eles se tornam mais fáceis de manter, versionar, testar e colaborar.

Utilize o Airflow para compor fluxos de trabalho como grafos acíclicos dirigidos (DAGs) de tarefas. O agendador do Airflow executa suas tarefas em uma série de workers respeitando as dependências definidas. Ferramentas de linha de comando abrangentes facilitam a realização de operações complexas nos DAGs. A interface de usuário intuitiva permite visualizar facilmente os pipelines em execução, monitorar o progresso e resolver problemas quando necessário.

O Airflow é especialmente útil em contextos de engenharia de dados e ciência de dados, pois permite a automação e a orquestração de processos complexos de tratamento de dados, treinamento de modelos de machine learning, execução de ETLs e muito mais. Tudo isso contribui para uma gestão mais eficiente do ciclo de vida dos dados e dos modelos preditivos.

## User Interface

- **DAGs**: Visão deral do seu ambiente.

  ![DAGs](https://raw.githubusercontent.com/apache/airflow/main/docs/apache-airflow/img/dags.png)

- **Grid**: Visão de todas as execuções de um DAG.

  ![Grid](https://raw.githubusercontent.com/apache/airflow/main/docs/apache-airflow/img/grid.png)

- **Graph**: Visão de todas as tarefas de um DAG e suas dependências.

  ![Graph](https://raw.githubusercontent.com/apache/airflow/main/docs/apache-airflow/img/graph.png)

- **Task Duration**: Tempo de execução de cada tarefa de um DAG.

  ![Task Duration](https://raw.githubusercontent.com/apache/airflow/main/docs/apache-airflow/img/duration.png)

- **Gantt**: Duração de cada execução de um DAG.

  ![Gantt](https://raw.githubusercontent.com/apache/airflow/main/docs/apache-airflow/img/gantt.png)

- **Code**: Código de cada DAG.

  ![Code](https://raw.githubusercontent.com/apache/airflow/main/docs/apache-airflow/img/code.png)

### Instalação do Airflow em uma máquina EC2 AWS sem Docker

Este README fornece instruções passo a passo para instalar o Apache Airflow em uma instância EC2 da Amazon usando Ubuntu como sistema operacional. Certifique-se de ter as permissões adequadas e o acesso SSH à sua instância EC2 antes de começar.

Caso tenha dúvidas, visitar a [documentação](https://airflow.apache.org/docs/apache-airflow/stable/start.html)

### Criar conta na AWS

Criar uma conta na AWS e fazer o login.

![Página inicial](assets/aws-registrar-conta.png)

[Site AWS](https://www.googleadservices.com/pagead/aclk?sa=L&ai=DChcSEwjAsan6qK2CAxWznVoFHQsWDoQYABAAGgJ2dQ&ase=2&gclid=EAIaIQobChMIwLGp-qitggMVs51aBR0LFg6EEAAYASAAEgKmIvD_BwE&ohost=www.google.com&cid=CAASJORoDeOFQkYZaTPYRdzliq9Zx_XBUo7PjMlTDdyGYQmvH8K9Gg&sig=AOD64_3UuKgxK2EF8RXgITpJ7PsabxFV0A&q&nis=4&adurl&ved=2ahUKEwjhmp_6qK2CAxU6rpUCHSP5D5QQ0Qx6BAgGEAE)

Crie e ative um ambiente virtual para o Airflow.

```bash
python3 -m venv venv
source venv/bin/activate
```

## Instalação do Apache Airflow

Instale o Airflow com suporte a PostgreSQL.

```bash
pip install 'apache-airflow==2.7.1' \
 --constraint "https://raw.githubusercontent.com/apache/airflow/constraints-2.7.1/constraints-3.8.txt"
```

Inicialize o banco de dados do Airflow.

```bash
airflow db init
```

## Configuração do PostgreSQL

Instale o PostgreSQL e suas dependências.

```bash
sudo apt-get install postgresql postgresql-contrib
```

Mude para o usuário postgres e acesse o PostgreSQL.

```bash
sudo -i -u postgres
psql
```

Dentro do prompt do psql, crie o banco de dados e o usuário do Airflow.

```sql
CREATE DATABASE airflow;
CREATE USER airflow WITH PASSWORD 'airflow';
GRANT ALL PRIVILEGES ON DATABASE airflow TO airflow;
```

Saia do psql e volte para o usuário normal.

```bash
\q
exit
```

## Configuração do Airflow

Atualize a configuração do Airflow para usar o PostgreSQL e o executor local.

```bash
sed -i 's#sqlite:////home/ubuntu/airflow/airflow.db#postgresql+psycopg2://airflow:airflow@localhost/airflow#g' ~/airflow/airflow.cfg
sed -i 's#SequentialExecutor#LocalExecutor#g' ~/airflow/airflow.cfg
```

Inicialize novamente o banco de dados do Airflow para aplicar as configurações.

```bash
airflow db init
```

## Criação do Usuário do Airflow

Crie um usuário admin para o Airflow.

```bash
airflow users create -u airflow -f airflow -l airflow -r Admin -e airflow@gmail.com
```

## Inicialização do Servidor Web e do Agendador

Inicie o servidor web e o agendador do Airflow.

```bash
airflow webserver &
airflow scheduler
```

## Acesso ao Airflow

Após iniciar o servidor web e o agendador, você pode acessar a interface do Airflow em seu navegador, usando o endereço IP da sua instância EC2 seguido pela porta `8080`. Por exemplo: `http://<EC2-IP-ADDRESS>:8080`.

Certifique-se de que a porta `8080` está aberta no grupo de segurança associado à sua instância EC2.