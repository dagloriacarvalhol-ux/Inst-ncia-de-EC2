Usuário → Route 53 → EC2 (API)

usuário usa o route53 para acessar o EC2  via API

usuário faz upload de um documento (imagem de comprovante)

EC2 valida o login , salva metadados no RDS e envia mensagem ao SQS

Fila de processamento SQS

salva um id para o arquivo , formato do arquivo XML e prioridade de processamento alta

Processamento via Lambda
lambda consome a fila , lê o arquivo no S3 , valida estrutura do XML , extrai os dados , identifica o tipo de documento e envia para o EBS ou S3 dependendo do tipo de arquivo

Uso do EBS ( processos internos)
Documentação crítica e de maior prioridade 

ec2 executa 
validação tributária e outros cálculos 
gera o resultado do cálculo e o arquivo segue para o S3

S3
arquivo processado no armazenamento principal
documentos/2026/01/nota_88231.xml

arquivamento em glacier após 30 dias (lifecycle rule)

cloudfront (consulta dos arquivos)
quando alguém com o devido acesso quer ver os arquivos

usuário > cloudfront > S3
baixar uma nota 

RDS
sistema registra tudo no banco e tabela documentos (id/cliente/tipo/status/data)

88231/xyztransportes/fiscal/aprovado/2026-01-24

Fluxo Completo                                    Consulta
Usuário                                              Usuário
   ↓                                                          ↓                                                          
Route 53                                            Cloudfront
   ↓                                                          ↓
EC2 (API)                                                S3
   ↓
RDS (metadados)
   ↓
SQS
   ↓
Lambda
   ↓
EBS (processamento crítico)
   ↓
S3 (armazenamento final)
   ↓
Glacier (arquivamento)


Exemplo de uso realista diário do sistema 
Funcionário faz upload de 5.000 notas fiscais
EC2 registra no RDS
SQS distribui o processamento
Lambdas processam em paralelo
Alguns documentos vão para EBS (validação fiscal)
Tudo é armazenado no S3
Após 30 dias, vai para Glacier automaticamente
Gerentes consultam via CloudFront
Sistema continua escalando sem travar
