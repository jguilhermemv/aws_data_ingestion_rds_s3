Pipeline de Migração de Dados do RDS para o s3

Stack Name: aws-cdc-dms-rds-to-s3

Origem dos Dados: RDS (PostgreSQL 14) - banco de dados relacional
Destino: AWS S3 (formato de saída: arquivos Parquet)

Ferramenta de Migraçã: AWS DMS (Servidor na nuvem AWS que executa software de replicação)

## AWS DMS - Detalhes Técnicos

- AWS DMS se conecta à fonte de dados de origem, lê os dados de origem (source endpoint) e formata os dados para consumo pela fonte de dados de destino (target endpoint)
- Carrega os dados na fonte de dados de destino (target endpoint)
- Maior parte desse processamento ocorre na memória (transações grandes podem exigir algum buffer no disco)

### DMS Task:



Requisitos:

1. Cria uma conexão de origem (source endpoint) para informar ao AWS DMS de onde extrair os dados
2. Criar conexão destino (target endpoint) para informar ao AWS DMS onde carregá-los
3. Agendar uma tarefa que é executada no servidor do DMS para mover seus dados

# Source - RDS - PostgreSQL

1. Create a RDS instance of a PostgreSQL 14
2. For full-load plus CDC tasks or CDC-only tasks, grant superuser permissions for the user account specified for the PostgreSQL source database. The user account needs superuser permissions to access replication-specific functions in the source. For full-load only tasks, the user account needs SELECT permissions on tables to migrate them.
3. Add the IP address of the AWS DMS replication server to the pg_hba.conf configuration file and enable replication and socket connections. An example follows.
AWS DMS supports change data capture (CDC) using logical replication. To enable logical replication of a self-managed PostgreSQL source database, set the following parameters and values in the postgresql.conf configuration file:
5. Set wal_level = logical.
6. Set max_replication_slots to a value greater than 1.
7. Set the max_replication_slots value according to the number of tasks that you want to run. For example, to run five tasks you set a minimum of five slots. Slots open automatically as soon as a task starts and remain open even when the task is no longer running. Make sure to manually delete open slots.
8. Set max_wal_senders to a value greater than 1.
9. The max_wal_senders parameter sets the number of concurrent tasks that can run.
10. The wal_sender_timeout parameter ends replication connections that are inactive longer than the specified number of milliseconds. The default is 60000 milliseconds (60 seconds). Setting the value to 0 (zero) disables the timeout mechanism, and is a valid setting for DMS.
11. When setting wal_sender_timeout to a non-zero value, DMS requires a minimum of 10000 milliseconds (10 seconds), and fails if the value is less than 10000. Keep the value less than 5 minutes to avoid causing a delay during a Multi-AZ failover of a DMS replication instance.
12. 

# Target - S3


1. Verifique se o bucket S3 que você está usando como destino está na mesma região da AWS que a instância de replicação do DMS que você está usando para migrar seus dados.
2. Garanta que a conta da AWS que você usa para a migração tem uma função IAM com permissões de gravação e exclusão para o bucket S3 que você está usando como destino.
3. Certifique-se de que essa função tem permissões de marcação para que você possa marcar quaisquer objetos S3 escritos no bucket de destino.
4. Adicione DMS (dms.amazonaws.com) como entidade confiável na função IAM.
5. Configure o acesso à conta, garantindo que a função atribuída à conta do usuário usada para criar a tarefa de migração tenha as permissões necessárias para colocar e deletar objetos, além de adicionar marcações.
6. Ao usar o AWS DMS para replicar alterações de dados usando uma tarefa CDC, o primeiro campo do arquivo de saída .csv ou .parquet indica como os dados da linha foram alterados.
7. Se você deseja armazenar as alterações de linha em arquivos CDC de acordo com a ordem da transação, você precisa usar as configurações do endpoint S3 para especificar isso e o caminho da pasta onde você deseja que os arquivos de transação CDC sejam armazenados no destino S3.
8. Para controlar a frequência de gravações em um destino Amazon S3 durante uma tarefa de replicação de dados, você pode configurar os atributos de conexão extra cdcMaxBatchInterval e cdcMinFileSize.
9. Esteja ciente das limitações de usar o Amazon S3 como destino. Por exemplo, não habilite a versão para S3. Se você precisar de versionamento S3, use políticas de ciclo de vida para excluir ativamente versões antigas.
10. O formato .csv é o formato de armazenamento padrão para objetos de destino Amazon S3. Para um armazenamento mais compacto e consultas mais rápidas, você pode usar o Apache Parquet (.parquet) como formato de armazenamento.
11. Você pode marcar objetos Amazon S3 que uma instância de replicação cria especificando objetos JSON apropriados como parte das regras de mapeamento de tabela de tarefas.
