# Configuração do Fluent Bit para Monitoramento de CPU e Envio ao Azure Blob Storage

Este documento descreve o processo passo a passo para configurar o Fluent Bit em um container Docker, coletar métricas de CPU e enviá-las para uma Azure Blob Storage. Abaixo, estão as etapas detalhadas para implementar essa solução, incluindo explicações sobre o arquivo de configuração e os comandos utilizados.

## Pré-requisitos
- Docker instalado no sistema.
- Conta no Azure com uma Storage Account configurada.
- Chave de acesso (`Access Key`) da Storage Account copiada do portal Azure.
- Container Docker para executar o Fluent Bit.

## Passo a Passo

### 1. Baixar a Imagem do Fluent Bit
Primeiro, baixe a imagem oficial do Fluent Bit versão 2.0 do repositório da Fluent Bit no Docker Hub. Utilize o comando abaixo:

```bash
sudo docker pull cr.fluentbit.io/fluent/fluent-bit:2.0
```

### 2. Executar o Fluent Bit
Execute o Fluent Bit com o comando abaixo para verificar se está funcionando corretamente:

```bash
sudo docker run -ti cr.fluentbit.io/fluent/fluent-bit:2.0 -i cpu -o stdout -f 1
```

### 3. Configurar o Fluent Bit
Crie um arquivo de configuração chamado `fluent-bit.conf` com o seguinte conteúdo:

```ini
[SERVICE]
    Flush        5
    Log_Level    info

[INPUT]
    Name         cpu
    Tag          cpu_metrics
    Interval_Sec 1

[OUTPUT]
    Name         azure_blob
    Match        *
    account_name fluentb
    shared_key   **************************
    path         logs/
    container_name logs
    auto_create_container on
    tls          on
```

### 4. Definir Permissões do Arquivo de Configuração
Defina as permissões do arquivo de configuração para leitura:

```bash
chmod 644 fluent-bit.conf
```

### 5. Executar o Fluent Bit com a Configuração
Execute o Fluent Bit utilizando o arquivo de configuração criado:

```bash
sudo docker run -v /home/jeremiasbarcelos/Downloads/jbfluent/fluent-bit.conf:/fluent-bit/etc/fluent-bit.conf \
    cr.fluentbit.io/fluent/fluent-bit:2.0 \
    /fluent-bit/bin/fluent-bit -c /fluent-bit/etc/fluent-bit.conf
```

---

Seguindo esses passos, você terá configurado o Fluent Bit para monitorar métricas de CPU e enviá-las para o Azure Blob Storage. Certifique-se de substituir os valores de `account_name` e `shared_key` pelos dados da sua conta no Azure.