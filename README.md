# Sompo_AWS_Infra

### Comandos usados na conexão do Banco RDS da AWS vinculado ao EC2 'sompo_ec2'.

Iniciamos a conexão com o EC2

    ssh -i <CHAVE_PEM> ubuntu@ec2-52-73-88-8.compute-1.amazonaws.com

Nos conectamos ao RDS (obs: caso não tiver instalado o psql é só fazer um sudo apt install "nome do pacote")

    psql -h <RDS_ENDPOINT> -U postgres -d bogg_db

Instalamos a extensão de vetorização para o banco de dados

    CREATE EXTENSION IF NOT EXISTS vector; 

Verificamos sua instalação

    \dx vector 

Criamos a tabela 'documents' para armazenar o arquivo txt

    CREATE TABLE documents (
    id SERIAL PRIMARY KEY,
    filename TEXT,
    content TEXT,
    embedding vector(3)
    );
    
E inserimos um arquivo teste

    INSERT INTO documents (filename, content, embedding) 
    VALUES ('teste.txt', 'Este é um documento de teste', '[0.1, 0.2, 0.3]'::vector);

Aqui fazemos uma consulta de similaridade para mostrar todos os arquivos que possuem conteúdo similar ao conteúdo na coluna 'embedding'

    SELECT filename, content, 
           1 - (embedding <=> '[0.1, 0.2, 0.4]'::vector) as similarity
    FROM documents 
    ORDER BY embedding <=> '[0.1, 0.2, 0.4]'::vector
    LIMIT 5;
