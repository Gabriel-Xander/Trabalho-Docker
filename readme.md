# Tutorial Aplicação: Docker Swarm, Grafana, MySQL, WordPress & Prometheus

## 1. Preparação para a Execução

1. Certifique-se de ter o Docker, Git e WSL (caso esteja no Windows) instalados.
2. Verifique se as portas necessárias não estão sendo utilizadas por outros serviços.

## 2. Criar o Diretório

1. Crie um diretório e navegue até ele:
    ```bash
    mkdir sua-pasta 
    cd sua-pasta
    ```
2. Clone o repositório:
    ```bash
    git clone https://github.com/Gabriel-Xander/Trabalho-Docker.git
    cd Trabalho-Docker
    ```

## 3. Executar os Comandos

1. Inicialize o Docker Swarm:
    ```bash
    docker swarm init
    ```
2. Crie e atualize um stack (conjunto de serviços) a partir de um arquivo Compose no swarm:
    ```bash
    docker stack deploy -c arquivo.yml wordpress_stack
    ```
3. Verifique os nós do Docker Swarm:
    ```bash
    docker node ls
    ```
4. Obtenha o endereço IP do nó líder:
    ```bash
    docker node inspect -f '{{ .Status.Addr }}' nome_da_maquina
    ```
5. Verifique os contêineres em execução:
    ```bash
    docker ps
    ```
6. Acesse o contêiner do WordPress:
    ```bash
    docker exec -it ID_Do_Container_Wordpress bash
    ```
7. No contêiner do WordPress, atualize os pacotes e instale o nano:
    ```bash
    apt update
    apt install nano
    ```
8. Edite o arquivo `wp-config.php`:
    ```bash
    nano wp-config.php
    ```
    Adicione as seguintes linhas antes de `/* That's all, stop editing! Happy publishing. */`:
    ```php
    if ($configExtra = getenv_docker('WORDPRESS_CONFIG_EXTRA', '')) {
        eval($configExtra);
    }

    define('WP_CACHE', true);
    define('WP_REDIS_HOST', 'redis');
    define('WP_REDIS_PORT', 6379);
    /* That's all, stop editing! Happy publishing. */
    ```
    Salve e saia do editor (CTRL+X, Y e ENTER).

## 4. Verificar o MySQL

1. No contêiner do WordPress, instale o cliente MySQL:
    ```bash
    apt install default-mysql-client
    ```
2. Conecte-se ao banco de dados MySQL:
    ```bash
    mysql -h db -u wpuser -pwppassword wordpress
    ```
3. Verifique as tabelas:
    ```sql
    show tables;
    ```
4. Saia do MySQL e do contêiner:
    ```bash
    exit
    ```

## 5. Acessar o WordPress

1. No navegador, acesse `http://<ip-da-máquina-líder>:80` ou `localhost:8080`. Você deve ver a página de instalação e configuração do WordPress.
2. Acesse o painel de administração em `http://<ip-da-máquina-líder>:80/admin`.
    - Vá em "Ferramentas" e "Informações", e procure por "Banco de dados" para ver as configurações do banco de dados.
3. Vá em "Plugins" -> "Adicionar plugin", procure e instale o plugin "Redis".
    - Ative o plugin.

## 6. Acessar o Prometheus

1. Acesse o seguinte endereço: `http://<ip_da_máquina_líder>:9090`
2. Na aba "Expressions", digite "up" e clique em "Execute".
3. Observe as instâncias que estão em execução.

## 7. Acessar o Grafana

1. Acesse `http://<ip_da_máquina_líder>:3000`.
2. Faça login com as credenciais "Admin" e "Admin".
3. Vá para "Data sources":
    1. Clique em "Add new data source".
    2. Selecione "Prometheus".
    3. Na seção de conexão, insira o endereço `http://<ip_da_máquina_líder>:9090`.
    4. Clique em "Save" e teste a conexão.
4. Vá para "Dashboards", clique em "New" e selecione "Import".
    - Importe um código, como por exemplo '3662', e clique em "Import".

## 8. Verificação Final

1. Verifique se as aplicações estão em execução com sucesso acessando `http://<ip_da_máquina_líder>:9090/Targets`.

## FIM

Você agora configurou uma aplicação WordPress completa com MySQL, Redis, Prometheus, Grafana e monitoramento, tudo rodando em um cluster Docker Swarm.
